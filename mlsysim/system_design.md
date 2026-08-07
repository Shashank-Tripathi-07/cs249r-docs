# MLSys·im: System Design

*This document describes how `mlsysim eval` actually turns a hardware and model name into a scorecard, how the registry system enforces data integrity, and how one Python package manages to run both as a normal pip install and as a WASM wheel inside a student's browser. It is written for a contributor changing the physics core, a solver backend, or the registry loader, not for someone reading about the product. Read [`design.md`](design.md) first for the framing; this document only covers mechanics. All facts below are sourced from `mlsysim/mlsysim/`, `pyproject.toml`, and `PROVENANCE.md`.*

## 1. Problem this system solves

An evaluation has to answer three different kinds of question with one command: does this configuration physically fit (memory, bandwidth), does it perform acceptably (latency, throughput), and is it economically sane (cost, carbon). Those three questions have genuinely different failure modes and need to be checked in that order, since a configuration that does not fit does not get to be evaluated for performance. Separately, every number the engine produces has to trace back to a real, citable source, a spec sheet, a paper, a benchmark, not an invented constant, because the entire value of the tool depends on its numbers being trustworthy rather than plausible-looking. This document covers how both of those requirements are actually implemented.

## 2. Dependencies and what each one actually does here

| Dependency | Role in this codebase |
|---|---|
| `pint>=0.24.4` | Every physical quantity in the engine is a `pint` `Quantity`, not a bare float. Unit conversions (`.to('GB')`, `.m_as("ms")`) happen throughout the evaluation pipeline, this is not decoration, a bug in unit handling here produces a wrong physics answer, not a crash. |
| `pydantic>=2.10.5` | Schema validation for CLI input (`EvalNodeSchema`, `MlsysPlanSchema`) and for the provenance/evaluation result types. |
| `numpy>=2.0` | Math primitives used inside the solvers. |
| `typer>=0.25.1` | The entire CLI is built on this. |
| `rich>=15.0.0` | Console rendering, with stdout and stderr deliberately split so a script piping stdout gets clean output. |
| `pyyaml>=6.0` | Parses `mlsys.yaml` evaluation plans and every registry data file. |
| `scipy>=1.15.3` (optional, `opt` group) | Continuous optimization backend, lazily imported, not required unless you actually use it. |
| `ortools>=9.15.6755` (optional, `opt` group) | Discrete optimization backend, same lazy pattern. |

One detail in the dependency floors is a direct signal of how tightly this package is coupled to the browser labs: the pydantic and numpy version floors are not chosen for their own sake, they are pinned to match exactly what version ships inside the Pyodide runtime, separately for the browser bundle and the Node bundle. This is because Pyodide's package installer cannot upgrade a package that is already present in the runtime, so if `mlsysim`'s floor asked for something newer than what Pyodide ships, installation inside a lab would fail outright.

## 3. Component inventory

```
                    mlsysim CLI (Typer)
        zoo | schema | eval | serve | audit | optimize
                          |
              +-----------+-----------+
              |                       |
         Registries               Solver backends
    (hardware, models,          (exhaustive, scipy,
     constants, solver)          or-tools, via a
              |                  lazy-loading registry)
              v
      loaded from YAML under
      mlsysim/hardware/data/,
      mlsysim/models/data/
              |
              v
      Provenance system (embedded citation
      metadata on every registry entry,
      checked by a separate standalone
      audit tool, not by the CLI itself)
```

The registries are built by a generic plugin mechanism (`Registry`, keyed by Python entry points, not a hardcoded list), and the actual `Hardware` and `Models` collections are constructed by a loader that reads per-item YAML files and builds typed registry subclasses from them dynamically. Solver backends implement one shared protocol (`compile()`/`solve()`) so the evaluation pipeline can call any of them identically regardless of which one actually ran.

## 4. Data flow: `mlsysim eval`

```
1. Input: either a positional target.yaml (cluster plan, has
   both "hardware" and "workload" keys) or CLI flags for a
   quick single-node check
                    |
2. Validation
   cluster path  -> MlsysPlanSchema.model_validate(raw_data)
   quick path    -> EvalNodeSchema(model_name=..., hardware_name=...)
   both resolve names to live registry objects internally
                    |
3. Solver dispatch (SystemEvaluator.evaluate)
   nodes == 1 or no fleet -> SingleNodeModel
   otherwise              -> DistributedModel
   fleet + duration given -> also runs EconomicsModel
   all run through a shared Pipeline
                    |
4. Output: a SystemEvaluation with three levels
   feasibility  -> memory_used_gb, memory_capacity_gb, ...
   performance  -> latency, throughput, mfu
   macro        -> tco_usd, carbon_footprint, energy_cost, capex
   each level carries its own PASS / FAIL / SKIPPED / WARNING
                    |
5. Assertions (cluster path only)
   schema.constraints.asserts checked against the flattened
   level.metric results, failures collected as strings
                    |
6. Rendering: text, json, markdown, or html, with a distinct
   exit code depending on what happened
```

The three-level structure in stage 4 is the direct implementation of the ordering described in section 1: feasibility is checked first, and a configuration that fails feasibility does not get a meaningful performance or economics answer, it gets marked accordingly rather than the pipeline proceeding to compute numbers for a configuration that cannot physically run.

## 5. Error handling

```
ExitCode (IntEnum)
  SUCCESS      = 0
  BAD_INPUT    = 1   validation or syntax failure
  PHYSICS_FAIL = 2   out of memory, pipeline starvation
  SLA_FAIL     = 3   a performance or cost assertion was violated
```

These codes are not incidental, they exist specifically so a CI pipeline calling `mlsysim eval` can branch on what actually happened rather than parsing text output. A shared `error_shield` context manager is the single place that maps exceptions to exit codes: a `pydantic.ValidationError` is reformatted into readable per-field messages and mapped to `BAD_INPUT`, a domain-specific `OOMError` maps to `PHYSICS_FAIL`, and an assertion violation is raised directly as `SLA_FAIL` from within the eval command itself.

Registry integrity is enforced at load time, not at use time. Loading the hardware or model registry raises immediately on a duplicate key across two YAML files, and a separate check inside the YAML loader itself raises on a duplicate key within one file, before that malformed data can ever reach a solver and produce a wrong-but-plausible-looking answer.

The optional solver backends fail loudly and specifically rather than with a generic import error. If `scipy` or `ortools` is not installed and a command tries to use the corresponding backend, the failure message names the exact extra to install (`pip install mlsysim[opt]`), not a bare `ModuleNotFoundError` a user would have to decode themselves. The `exhaustive` backend has no external dependency and is loaded eagerly, since it is always available regardless of which optional extras are installed.

## 6. The provenance system, and a naming collision worth knowing about

Provenance is enforced in two separate places that are easy to conflate. The first is structural: every registry entry embeds a `Provenance`/`Sourced` value that is validated at construction time, a citation rule violation is a hard failure the moment the data is loaded, not something checked later. The second is a standalone audit tool, run as `python -m mlsysim.tools.audit_provenance --scope all --strict`, which walks the live, already-loaded registries and checks every node's provenance metadata against a set of per-domain rules.

This audit tool is not wired into the `mlsysim` CLI in any way. It is a separate entry point, run directly as a module, typically from a release checklist or CI step, not from `mlsysim audit`. That CLI command with the same word in it does something unrelated: it profiles the actual machine running the CLI against the Iron Law model. Two different tools sharing the word "audit" is a real, existing naming collision in this codebase, not a documentation error, and it is worth remembering when someone says "run the audit" without specifying which one.

## 7. How this package serves two very different runtimes

```
Native install                    WASM (browser labs)
  pip install mlsysim               mlsysim built as a wheel,
  resolves dependency floors        micropip.install()'d into
  normally, no special              a Pyodide runtime whose
  constraints                       own bundled package versions
                                     cannot be upgraded
```

The `mlsysim.labs` subpackage is the one part of this codebase built specifically for the second runtime. It is the only subpackage that imports `marimo` or `pyodide`, and it is explicitly excluded from the project's type-checking configuration, since those imports do not resolve outside a Pyodide environment. The coupling between `labs/` and the rest of the package runs in one direction only: `labs/` imports the registries and the engine the same way any external consumer would, but nothing in the core physics engine, the registries, or the CLI imports anything from `labs/`. This means the labs UI layer is genuinely separable from the physics engine it sits on top of, even though both currently ship inside the same wheel rather than as two separate installable packages.

## 8. Contributing

If you are adding a new solver backend, implement the shared `OptimizerProtocol` and register it through the lazy-loading pattern the scipy and or-tools backends already use, do not import a heavy optional dependency eagerly at module load time. If you are changing registry data, remember that a duplicate key is a load-time failure, not a silent overwrite, and that a citation your entry relies on has to pass the structural provenance check before it will load at all, the standalone audit tool is a separate, additional check on top of that, not a replacement for it.
