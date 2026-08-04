# MLSys·im: Implementation Reference

> **Status: as-built, contributor-facing.** MLSys·im is a live, already-implemented and published package. This document is your map for reading and modifying the real source: file paths, line numbers, and representative code pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](mlsysim-design.md) first for the "what and why"; this doc is the "where and how." Section 11, "Common contribution workflows," is the fastest way in if you already know what you want to change.

## Prerequisites

| To work on... | You need |
|---|---|
| The core `mlsysim` library, physics, or registries | Python 3.10 or newer, a virtual environment, and `pip install -e "mlsysim/[dev]"` from the repository root. |
| The CLI (`mlsysim/mlsysim/cli/`) | The same as above; the CLI is part of the core package, no extra install needed. |
| A specific optimizer backend (SciPy or OR-Tools) | The above, plus `pip install -e "mlsysim/[opt]"`. |
| Visualization code (Plotly/Matplotlib output) | `pip install -e "mlsysim/[viz]"`. |
| The documentation site | The above, plus Quarto and `pip install -e "mlsysim/[docs]"`. |
| The research paper or ISCA tutorial slides | A LaTeX distribution (`pdflatex`, `bibtex`), Inkscape or `rsvg-convert` for figure conversion, and (for the tutorial slides specifically) a Beamer Metropolis theme. |
| A lab notebook (`labs/`) | The core library install above, plus `marimo` (`pip install marimo`) to edit and preview notebooks natively. |
| The labs' WASM build or browser smoke test | The above, plus `python -m build`, Node.js (for the Pyodide-in-Node install check), and Playwright with Chromium (`pip install playwright && playwright install chromium`). |
| The VS Code extension (`mlsysim/vscode-ext/`) | Node.js, npm, and the VS Code extension development tools. This is a separate, independently versioned artifact from the Python package. |

## Repository layout

```
cs249r_book/
  mlsysim/
    mlsysim/              # The installable package
      models/              # Layer A: workload registry
      hardware/             # Layer B: hardware registry
      infrastructure/       # Layer C: facility/grid registry
      systems/               # Layer D: nodes/racks/fleets/fabrics
      engine/                 # Layer E: the roofline engine, solvers, scenarios
      physics/                 # Pure formulas and physical constants
      core/                     # Provenance, units, shared types
      reference_stats/          # Non-executable, cited real-world anchors
      literature/                # Cited external benchmark figures
      platforms/, ops/, datasets/, tools/, infra/, sim/  # supporting subpackages
      labs/                       # Shared lab UI toolkit + DesignLedger persistence
      cli/                         # The `mlsysim` Typer CLI
      show.py, fmt.py, solvers.py  # Top-level convenience modules
    tests/                 # Flat pytest suite, marker-organized
    docs/                  # Quarto documentation site (mlsysbook.ai/mlsysim/)
    paper/                 # Standalone LaTeX research paper
    tutorial/              # ISCA full-day tutorial materials
    examples/              # Standalone runnable example scripts
    vscode-ext/            # MLSysim Workbench VS Code extension (separate package)
    pyproject.toml, pytest.ini, RELEASE.md, PROVENANCE.md, CITATION.cff, CHANGELOG.md
  labs/
    vol1/                 # 17 labs (00-16), Foundations
    vol2/                 # 17 labs (01-17), Systems at Scale
    mlsysbook_labs/        # Companion package: lab catalog + UI metadata
    tools/build_site.sh    # Builds wheels, exports labs to WASM, renders the site
    bootstrap.py            # Native-only dev convenience import shim
    tests/browser_smoke.py  # Real-Playwright-browser regression test
    LAB_*.md                # Internal maintainer design/spec/audit documents
  .github/workflows/
    mlsysim-validate-dev.yml, mlsysim-preview-dev.yml, mlsysim-publish-live.yml
    mlsysim-pypi-publish.yml, mlsysim-build-pdfs.yml, mlsysim-update-pdfs.yml
    labs-validate-dev.yml, labs-preview-dev.yml, labs-publish-live.yml
```

---

## 1. The five-layer stack: real code

### 1.1 Layer A, a workload (`mlsysim/models/`)

`mlsysim/models/types.py` defines the class hierarchy. The base `Workload` exposes `lower()`, which every subtype implements to produce a hardware-agnostic `ComputationGraph` (`total_ops`, `parameter_count`, `weight_bytes`, `arithmetic_intensity`). `TransformerWorkload` additionally implements a detailed `training_memory()` method covering Megatron-LM and ZeRO-style weight, gradient, optimizer-state, and activation memory accounting; `SparseTransformerWorkload` (mixture-of-experts) overrides `lower()` to use *active* parameters for FLOPs but *total* parameters for memory footprint.

A real registry entry, `mlsysim/models/data/language.yaml`:

```yaml
Llama3_70B:
  __type__: TransformerWorkload
  name: Llama-3.1-70B
  architecture: Transformer
  parameters: 70600000000.0 param
  inference_flops: 141200000000.0 flop
  layers: 80
  hidden_dim: 8192
  heads: 64
```

`mlsysim/models/registry.py` loads every category YAML file and composes them into `Models.Language`, `Models.Vision`, `Models.Tiny`, `Models.Recommendation`, `Models.StateSpace`, and `Models.GenerativeVision`.

### 1.2 Layer B, hardware (`mlsysim/hardware/`)

`mlsysim/hardware/types.py`'s `HardwareNode` composes a `ComputeCore` (peak FLOPs, per-precision throughput), a `MemoryHierarchy` (capacity, bandwidth, and for small devices an SRAM and flash tier), an `IOInterconnect`, TDP, cost, and embodied carbon. `ridge_point()` on `HardwareNode` computes the classic Roofline ridge point directly: peak FLOPs divided by memory bandwidth.

An H100 entry, `mlsysim/hardware/data/cloud/H100.yaml`:

```yaml
name: NVIDIA H100
compute:
  peak_flops: 989.0 TFLOPs / s
  precision_flops: {tf32: 494.0, fp8: 1979.0, int8: "1979 TOPS", fp32: 67.0}
  sm_count: 132
memory:
  capacity: 80 GiB
  bandwidth: 3.35 TB / s
nvlink:
  direction: bidirectional_total   # datasheet "900 GB/s" -> 450 GB/s one-way
  bandwidth: 900.0 GB / s
tdp: 700 W
unit_cost: 25000 dollar
embodied_carbon_kg: 164.0
```

An ESP32-S3 entry (`mlsysim/hardware/data/tiny/ESP32_S3.yaml`) shows the small-device side of the same schema: `peak_flops: 0.0005 TFLOPs/s`, `memory.capacity: 4 MB`, a separate `sram_capacity: 520 KiB`, and a `dispatch_tax: 1.0 ms`, the framework-overhead cost that matters much more at this scale than on a datacenter accelerator.

### 1.3 Layer C, infrastructure (`mlsysim/infrastructure/`)

`GridProfile` (`mlsysim/infrastructure/types.py`) carries `carbon_intensity_g_kwh`, `pue`, and `wue` per region. Concrete entries in `mlsysim/infrastructure/registry.py` include named PUE and WUE anchors (for example `PUE_LIQUID_COOLED`, `PUE_TYPICAL`, `WUE_EVAPORATIVE`), a `Grids` registry of real regional grid profiles (`Quebec`, `Norway`, `US_Avg`), and named `Datacenters` (`Quebec_Hydro`, `France_Nuclear`, `Poland_Coal`) pairing a grid with a specific cooling and PUE choice.

### 1.4 Layer D, systems and topology (`mlsysim/systems/`)

`mlsysim/systems/types.py` defines `Node` (an accelerator plus per-node topology), `RackProfile`, `PodEnvelope`, `NetworkFabric`, and `Fleet` (a node type times a count, plus a fabric, region, and reliability figures). `mlsysim/systems/registry.py` composes real examples: `Nodes.DGX_H100` (an 8-way H100 node), `Fabrics.InfiniBand_NDR` (400 Gbps, 5 microsecond latency), and `Clusters.Frontier_8K` (1,024 of those nodes on that fabric, 8,192 GPUs total).

### 1.5 Layer E, the engine (`mlsysim/engine/`)

`mlsysim/engine/engine.py`'s `Engine.solve()` is the core Roofline computation, in order: validate inputs, resolve peak FLOPs for the requested precision, lower the workload to a `ComputationGraph`, check memory feasibility against the hardware's capacity (raising an `OOMError` if requested), compute both a compute-bound time and a memory-bound time and take the larger as the bottleneck, add a dispatch-and-per-layer-overhead tax, and derive throughput, MFU, HFU, and an energy-proportional power estimate. The actual roofline arithmetic is a pure function, `calc_bottleneck()` in `mlsysim/physics/performance.py`.

`mlsysim/physics/` holds every formula as a pure, standalone function, re-exported from `mlsysim/physics/__init__.py`: networking and collective-communication formulas (ring all-reduce time, bisection bandwidth, alpha-beta crossover), memory formulas (KV-cache sizing, activation memory, checkpoint size), economics formulas (fleet TCO, monthly egress cost), reliability formulas (MTBF, availability), and transformer-specific FLOP-counting formulas. `mlsysim/physics/constants.py` deliberately holds only genuine physical constants (for example, the speed of light in optical fiber), with a docstring explicitly distinguishing that from hardware specs or tunable calibration knobs, which live elsewhere.

`mlsysim/solvers.py` (17 lines) is a stable, mechanically-generated re-export of every class in `mlsysim/engine/solvers/`; it exists purely as a shorter, stable public import path (`from mlsysim.solvers import ServingModel`) and contains no logic of its own.

### 1.6 `Scenarios.*` and `ReferenceStats.*`

`mlsysim/engine/scenarios.py`'s `Scenario` composes a workload, a hardware or fleet target, and SLA constraints, and its `evaluate()` runs a three-level pipeline: feasibility (memory fit and pipeline-stall checks), performance (dispatches to the single-node or distributed solver depending on target size), and macro (annualized economics and sustainability, when a fleet and duration are given). `Scenarios` itself is a registry of named, ready-to-use examples (`Scenarios.ChatbotServing`, `Scenarios.FrontierTraining`, `Scenarios.SmartDoorbell`).

```python
import mlsysim as mlsys
result = mlsys.Scenarios.ChatbotServing.evaluate(batch_size=1, precision="fp16")
mlsys.Scenarios.ChatbotServing.validate()  # raises OOMError/SLAViolation on failure
```

`mlsysim/reference_stats/registry.py` is a separate, non-executable namespace of cited real-world figures (typical smartphone battery capacity, Waymo's sensor data rate, and similar), used by textbook prose, not by any computation.

---

## 2. The CLI (`mlsysim/mlsysim/cli/`)

### 2.1 Entry point and commands

Declared in `pyproject.toml`: `mlsysim = "mlsysim.cli.main:app"`. `python -m mlsysim` (`mlsysim/__main__.py`) imports and calls the exact same Typer `app` object, so the two invocation forms are behaviorally identical.

| Command | What it does |
|---|---|
| `mlsysim zoo [hardware\|models]` | Read-only registry browser. Pulls a sorted listing straight from `Hardware.list(...)` or `Models.list(...)` and renders it as a table, markdown, or JSON. No computation happens. |
| `mlsysim eval <model> <hardware> [--batch-size N]` | Quick single-node evaluation: resolves the named model and hardware from the zoo registries, builds a `SystemEvaluator`, and runs the Roofline pipeline for one node. |
| `mlsysim eval <plan.yaml>` | Full-plan evaluation: loads and Pydantic-validates the file as a `MlsysPlanSchema`, resolves a `Fleet` if more than one accelerator is requested, evaluates via the distributed solver, adds economics if an `ops:` block and enough nodes are present, and checks any `constraints.assert` entries, exiting with a distinct `SLA_FAIL` code on violation. |
| `mlsysim serve` | LLM serving performance: prefill and decode modeled separately. |
| `mlsysim audit` | Profiles the local machine actually running the CLI against the Iron Law. |
| `mlsysim optimize parallelism\|batching\|placement` | Design-space search for a configuration meeting given constraints, using an exhaustive backend by default or SciPy/OR-Tools if installed. |
| `mlsysim schema --type plan\|hardware\|workload` | Exports the live JSON Schema for the corresponding Pydantic model, for editor autocompletion on `mlsys.yaml` files. |

### 2.2 The `mlsys.yaml` schema (`mlsysim/cli/schemas.py`)

`MlsysPlanSchema` is the top-level Pydantic model: `version`, `name`, a required `workload` block (`WorkloadConfig`: name, batch size, sequence length), a required `hardware` block (`HardwareConfig`: name, accelerator count, node topology, interconnect bandwidth, precision, efficiency, with a validator enforcing `node_count * accelerators_per_node == accelerators`), an optional `ops` block (`OpsConfig`: grid region, duration in days), and an optional `constraints` block (a list of metric assertions, keyed on the flattened `feasibility.*`/`performance.*`/`macro.*` result dictionary). Its own `model_validator` resolves workload and hardware names against the zoo registries, a local YAML file, or a `hf://<model_id>` Hugging Face import, and compiles a `Fleet` object automatically when more than one accelerator is requested.

Real example plans live in `mlsysim/examples/yaml/`, including `test_assert_plan.yaml`, deliberately built with a `constraints.assert` entry that fails, useful as a reference for exercising the `SLA_FAIL` exit path.

### 2.3 Output discipline (`mlsysim/cli/renderers.py`, `mlsysim/cli/context.py`, `mlsysim/cli/exceptions.py`)

A shared `--output`/`-o` flag supports `text`, `json`, `markdown`, and (for some commands) `html`. JSON output goes through `print_json()`, which wraps `json.dumps(..., allow_nan=False)`, strict JSON that rejects NaN and Infinity, after a `_json_safe()` pass that turns Pint quantities into compact strings. Every renderer keeps diagnostics and warnings on stderr and the final payload on stdout, an explicit, commented design choice so a script can pipe stdout straight into `jq` or similar without needing to filter anything out. `mlsysim/cli/exceptions.py` maps outcomes to semantic exit codes: `SUCCESS = 0`, `BAD_INPUT = 1`, `PHYSICS_FAIL = 2`, `SLA_FAIL = 3`.

---

## 3. Labs and WASM integration

### 3.1 The shared toolkit (`mlsysim/mlsysim/labs/`)

Inside the `mlsysim` package itself: `style.py` defines a shared color palette and CSS meant to be injected once per lab notebook; `components.py` defines reusable marimo UI building blocks (cards, metric rows, comparison rows, a Roofline visualizer, a latency waterfall, and similar) that every lab imports rather than reimplementing.

### 3.2 `DesignLedger` (`mlsysim/mlsysim/labs/state.py`)

The persistence layer for student progress across all 34 labs. `LedgerState` (a dataclass) holds `track`, `current_step`, a `history` dict keyed by step, and `last_updated`. `DesignLedger.is_wasm` checks `sys.platform == "emscripten"`, the standard way Pyodide identifies itself.

Native persistence writes `~/.mlsys/ledger.json` synchronously. WASM persistence is asynchronous: `save_async()` serializes the state to JSON, stashes it on the JS global object, and runs an embedded JavaScript IIFE that opens an IndexedDB database and writes the value. As documented in the design doc's "Known issues," the current bridge variable name (`globalThis.__mlsys_temp_state`, written from inside a class method) is subject to Python's name-mangling rule and does not actually match what the embedded JavaScript reads; a fix is pending as an open pull request. If you're touching this file, read that section before changing anything in `save_async()` or `load_async()`.

`save()` is the synchronous-looking public entry point every lab calls; under WASM it fires `save_async()` as a background `asyncio` task and returns immediately, so a caller that needs a persistence guarantee (rather than best-effort) should not assume `save()` alone is sufficient.

### 3.3 Lab notebooks (`labs/vol1/`, `labs/vol2/`)

Each lab is a marimo notebook: a plain `.py` file starting with `import marimo` and `app = marimo.App(...)`, containing `@app.cell`-decorated functions. A representative first cell, from `labs/vol1/lab_00_introduction.py`:

```python
if sys.platform == "emscripten":
    import micropip
    await micropip.install(["pydantic", "pint", "plotly", "pandas"], keep_going=False)
    await micropip.install("../../wheels/mlsysim-0.1.2-py3-none-any.whl", keep_going=False)
    await micropip.install("../../wheels/mlsysbook_labs-0.1.0-py3-none-any.whl", keep_going=False)
else:
    from bootstrap import native_bootstrap
    native_bootstrap(__file__)

from mlsysim.labs.state import DesignLedger
from mlsysim.labs.style import COLORS, LAB_CSS
from mlsysim.labs.components import DecisionLog

ledger = DesignLedger()
if getattr(ledger, "is_wasm", False):
    _ = await ledger.load_async()
```

Every one of the 34 labs repeats this same branch, with the wheel filename hardcoded to the exact version being shipped; `labs/tools/build_site.sh` cross-checks that every lab's hardcoded wheel filename actually matches the wheel it just built, and fails the build with instructions if they've drifted apart.

`labs/bootstrap.py` is a native-only convenience shim: under Pyodide it's a no-op (marimo's WASM export only bundles the notebook file itself, not this file, so it can never run there anyway); natively, it puts the repository root on `sys.path` so a lab can `import mlsysim` from source without installing the wheel, useful for local editing and for native test runs.

### 3.4 Building the WASM site (`labs/tools/build_site.sh`)

1. Builds a wheel for `mlsysim` and a wheel for the companion `mlsysbook_labs` package via `python -m build --wheel`.
2. Cross-checks every lab's hardcoded `micropip.install(...)` wheel filename against what was actually built.
3. Runs `marimo export html-wasm <lab>.py -o <output>/index.html --mode run --no-show-code` for every lab in both volumes, producing one self-contained HTML page per lab.
4. Runs `quarto render` for the surrounding site shell, copies the WASM exports and wheels into the right build directories (wheels are duplicated into each volume's own build directory, since Pyodide workers resolve wheel paths relative to the worker, not the page).
5. Emits a `release-manifest.json` recording the lab count, volume count, and runtime.

### 3.5 The real-browser regression test (`labs/tests/browser_smoke.py`)

Exists specifically because issue #1353 (a lab that shipped broken to production) was invisible to every check except an actual browser session; see "Project history" in the design doc. It serves the exported WASM labs behind the same cross-origin-isolation headers production uses (required for `SharedArrayBuffer`), drives a real headless Chromium via Playwright, waits for a marimo DOM signal to confirm the shell rendered, then waits for network idle (with a generous timeout, since a first-load Pyodide boot plus wheel installation can take a while) to let Python actually finish initializing, and scans the browser's console output for a structured JSON exception marker, since marimo routes Python tracebacks through `console.log` rather than `console.error`. It also has a dedicated check for labs using tabbed UI, to catch the "network went idle but a tab never actually rendered" failure mode from issue #1388.

---

## 4. Testing (`mlsysim/tests/`)

### 4.1 Configuration

`mlsysim/pytest.ini` is the authoritative pytest configuration (it takes precedence over the separate, currently-inert `[tool.pytest.ini_options]` block in `pyproject.toml`, see "Known issues" in the design doc). It declares `testpaths = tests`, `--strict-markers`, and four markers:

```ini
markers =
    smoke: Fast subset (~1s) for CI pull request checks
    solver: Individual solver correctness tests
    empirical: Validation against published benchmarks (MLPerf, Chinchilla)
    integration: Cross-solver and end-to-end tests
```

### 4.2 Structure

The suite is a flat directory (no unit/integration subfolder split); the markers above are how you slice it, for example `pytest -m smoke` for a fast pull-request check. Notable files: `test_solver_suite.py` and `test_formulas.py` are the largest files (broad solver and formula correctness coverage); `test_registry_no_duplicate_specs.py` and `test_registry_loader_contract.py` guard registry data integrity; `test_provenance.py` and `test_provenance_audit.py` are the provenance-gate tests described in the design doc; `test_golden_regressions.py` pins known-good numeric outputs; `test_cli_contract.py` and `test_evaluation_contract.py` guard the CLI and scorecard's public shape; `test_exhaustive_backend.py`, `test_scipy_backend.py`, and `test_ortools_backend.py` each test one optimizer backend independently.

`tests/conftest.py` centrally defines shared fixtures (real hardware and model objects like `h100`, `a100`, `jetson`) used across the suite.

---

## 5. Packaging and release

### 5.1 `pyproject.toml`

Build backend `hatchling`. Core runtime dependencies: `pint`, `pydantic`, `numpy`, `typer`, `rich`, `pyyaml`, each with a version floor chosen deliberately to match what the two Pyodide bundles (browser and Node) already ship, documented inline (see "Known issues" and "Project history" in the design doc). Optional dependency groups: `viz` (Plotly, Matplotlib), `opt` (SciPy, OR-Tools), `mcp`, `full` (everything plus pandas), `docs`, `dev` (pytest, pytest-cov, pyright). `[project.scripts]` registers the single `mlsysim` console entry point. `[tool.hatch.build.targets.sdist]` explicitly limits the source distribution to the package itself plus `README.md`, `LICENSE.md`, `CHANGELOG.md`, and `CITATION.cff`, excluding tests, docs, examples, the paper, and the VS Code extension.

### 5.2 The release runbook (`RELEASE.md`) and what actually runs (`mlsysim-pypi-publish.yml`)

`RELEASE.md` documents the happy path as three commands: check out `dev`, tag `mlsysim-vX.Y.Z`, push the tag. Everything else is automated:

1. **`verify`**: checks the tag matches `^mlsysim-v[0-9]+\.[0-9]+\.[0-9]+`, greps the version out of `pyproject.toml`, `mlsysim/__init__.py`, and `CITATION.cff` and confirms they agree, checks `CHANGELOG.md` has a matching `## vX.Y.Z` heading, and confirms the tagged commit is actually reachable from `origin/dev`.
2. **`test`**: the full suite across Python 3.10 through 3.13.
3. **`build`**: `python -m build`, then `twine check dist/*`.
4. **`publish-pypi`**: `pypa/gh-action-pypi-publish` with OIDC Trusted Publishing and PEP 740 attestations enabled, no stored token anywhere.
5. **`verify-pypi`**: installs the just-published version from real PyPI (retrying for CDN propagation delay), checks `mlsysim.__version__` and the installed package's declared license, and runs an actual CLI smoke test (`mlsysim --help`, `mlsysim eval Llama3_8B H100 --batch-size 32`) against the real, freshly published package.
6. **`github-release`**: composes release notes, preferring a hand-written `RELEASE_NOTES_<version>.md` if one exists, otherwise extracting the matching section from `CHANGELOG.md` automatically.
7. **`docs-redeploy`**: dispatches `mlsysim-publish-live.yml` to redeploy the documentation site.

The pre-release checklist in `RELEASE.md` also documents a manual fallback (`make build` plus `twine upload` using a local `~/.pypirc`) for if the automated workflow itself is broken, and a rollback procedure via PyPI's "yank" mechanism.

### 5.3 Provenance tooling

`PROVENANCE.md` documents the three audit gates described in the design doc, and the actual command that runs them: `python -m mlsysim.tools.audit_provenance --scope all --strict`. This is run as part of the pre-release validation checklist alongside the test suite, `ruff check`, and a full documentation-site render.

---

## 6. Documentation site (`mlsysim/docs/`)

Rooted at `mlsysim/docs/_quarto.yml`, which delegates to `mlsysim/docs/config/_quarto-html.yml`, the real project configuration (`site-url: https://mlsysbook.ai/mlsysim/`). The API reference section is generated automatically via `quartodoc` from the live `mlsysim` package across every major subpackage (hardware, models, infrastructure, systems, platforms, datasets, literature, ops, core, engine, and the roughly 25 solver classes), so it's always in sync with the actual code. The sidebar also links out to `contributing.qmd` (the actual, detailed MLSys·im contribution guide, distinct from the repository's root-level `CONTRIBUTING.md`, which is just a router pointing here) and `provenance.qmd` and `extending-the-engine.qmd` for anyone adding new registry entries or new solvers.

---

## 7. The paper and tutorial

`mlsysim/paper/paper.tex` is a standalone LaTeX research paper, built via `mlsysim/paper/Makefile` (SVG figures converted to PDF via `rsvg-convert`, then `pdflatex` and `bibtex` in sequence), independent of the Quarto site. `mlsysim/tutorial/` holds the materials for a full-day ISCA tutorial: a detailed backward-designed lesson plan (`DESIGN.md`), exercises, a cheatsheet, prerequisites, an instructor quickstart, and Beamer slide sources, built by the same `mlsysim-build-pdfs.yml` workflow that builds the paper.

`mlsysim/examples/` is a third, separate thing from both: small, standalone, runnable Python scripts (not notebooks, not Quarto pages), each demonstrating one concept end to end with an expected-output comment block for reproducibility, directly referenced by the ISCA tutorial materials as live demo scripts.

---

## 8. CI/CD implementation notes

### 8.1 `mlsysim-validate-dev.yml`

Runs the full pytest suite (with `dev` and `viz` extras installed) and a documentation-site build check, plus a non-blocking link check, on every pull request and push touching `mlsysim/**`.

### 8.2 `mlsysim-pypi-publish.yml`

Covered in detail in section 5.2 above.

### 8.3 `labs-validate-dev.yml`

The most involved of the CI workflows relevant to this project. Beyond a standard notebook-validation and site-build stage, its `wasm-smoke-test` job (roughly 25 minutes, the slowest single job in the labs pipeline) builds real wheels for `mlsysim` and the labs helper package, verifies a Node-based Pyodide runtime can install and import them via micropip, exports a representative set of labs (always including `lab_05_dist_train`, the one that previously shipped broken, per issue #1353) to WASM HTML, installs Playwright and Chromium, and runs `labs/tests/browser_smoke.py` against the real exported site. The job fails the whole workflow if this step fails.

### 8.4 Preview and publish workflows

`mlsysim-preview-dev.yml` and `labs-preview-dev.yml` build and deploy to separate dev-preview sites automatically once their respective validate workflow succeeds on `dev`. `mlsysim-publish-live.yml` and `labs-publish-live.yml` are both manual-only, gated behind their validate workflow being green, and deploy to `mlsysbook.ai/mlsysim/` and `mlsysbook.ai/labs/` respectively via the shared `gh-pages` branch.

---

## 9. Local development setup

1. From the repository root, create and activate a virtual environment.
2. `pip install -e "mlsysim/[dev]"` (add `,viz`, `,opt`, or `,docs` as needed for what you're working on).
3. `pytest mlsysim/tests/ -v` to confirm the install works. Use `-m smoke` for a fast subset while iterating.
4. For engine or registry changes, write or update a test in `mlsysim/tests/` under the appropriate marker, and if you're adding a new hardware or model entry, follow the provenance requirements in `mlsysim/docs/contributing.qmd` (every entry needs a cited source) and confirm `python -m mlsysim.tools.audit_provenance --scope all --strict` still passes.
5. For lab changes, install `marimo` and edit the notebook natively (`marimo edit labs/vol1/lab_NN_slug.py`); native mode uses `bootstrap.py` to import `mlsysim` straight from source, so you don't need to rebuild a wheel just to iterate on a lab. Only rebuild and test the WASM export (`labs/tools/build_site.sh`, then `labs/tests/browser_smoke.py`) once your change is close to done, since that loop is much slower.
6. Follow the root `CONTRIBUTING.md`'s universal policy: branch from `dev`, never `main`; install pre-commit hooks once per clone; stage files explicitly rather than a blanket `git add .`; open PRs against `dev` referencing an issue number.

---

## 10. Known-broken or inaccurate as of this document

- `mlsysim/mlsysim/labs/state.py`'s `save_async()` silently fails to persist to IndexedDB under WASM due to a Python name-mangling bug in the bridge variable name, described in detail in the design doc's "Known issues." A fix exists as an open, verified pull request but is not yet merged.
- `pyproject.toml`'s `[tool.pytest.ini_options]` block is inert; `pytest.ini` at the repository root is the configuration that actually applies.
- `DesignLedger.get_baseline()` always returns an empty dictionary; it's a documented but unimplemented stub.

---

## 11. Common contribution workflows

### Adding a new hardware entry to the zoo

1. Add a new YAML file under the appropriate tier in `mlsysim/hardware/data/` (`cloud/`, `workstation/`, `mobile/`, `edge/`, or `tiny/`), following the `HardwareNode` schema (`mlsysim/hardware/types.py`): compute core, memory hierarchy, interconnect, TDP, cost, embodied carbon.
2. Every field needs a cited source; follow the provenance pattern already used by neighboring entries, and pay particular attention to interconnect bandwidth, get the `direction` field (`per_direction` versus `bidirectional_total`) wrong and every solver that uses this hardware silently overstates achievable bandwidth by up to two times.
3. Confirm `pytest mlsysim/tests/test_registry_no_duplicate_specs.py mlsysim/tests/test_registry_loader_contract.py -v` passes, then `python -m mlsysim.tools.audit_provenance --scope all --strict`.
4. `mlsysim zoo hardware` should show your new entry; sanity-check it with `mlsysim eval <some model> <your new hardware>`.

### Adding or improving a solver

1. Solvers live under `mlsysim/engine/solvers/`, grouped by concern (`performance.py`, `distributed.py`, `serving.py`, `training.py`, `economics.py`, `reliability.py`, and similar), built on the shared `BaseSolver`/`ForwardModel` interface in `base.py`.
2. Any new formula the solver needs should go in `mlsysim/physics/`, as a pure, unit-checked function with a cited source in its docstring, not inlined into the solver itself; the solver should call into `physics/`, mirroring how the existing Roofline engine calls `calc_bottleneck()`.
3. Add tests under the `solver` marker, and if you're validating against a published external benchmark, consider the `empirical` marker instead.
4. If the change affects the public solver export list, remember `mlsysim/solvers.py` re-exports everything in `mlsysim/engine/solvers/__all__` automatically; you shouldn't need to touch that file directly.

### Fixing a bug in the CLI

1. Locate the relevant command in `mlsysim/cli/commands/`.
2. Keep the stdout/stderr discipline intact: diagnostics and warnings go through `print_warning`/`print_error` (stderr), the final payload goes through the appropriate `render_*` function (stdout). If your change affects JSON output, make sure it still round-trips through `_json_safe()` cleanly (Pint quantities, NaN, and Infinity all need explicit handling).
3. Add or update a test in `test_cli_contract.py`, and confirm exit codes still match `mlsysim/cli/exceptions.py`'s semantic codes if you touched an error path.

### Working on a lab

1. Edit the notebook natively with `marimo edit labs/volN/lab_NN_slug.py`. Import shared UI pieces from `mlsysim.labs.components`/`style` rather than rebuilding a card or a chart from scratch.
2. If your change touches student-progress persistence, read the design doc's "Known issues" entry on `DesignLedger` first; this is a part of the codebase with a real, subtle, currently open bug at exactly the Python-to-JavaScript boundary.
3. Before opening a PR, run the lab's Level 1 to 4 checks locally if you can, and rely on `labs-validate-dev.yml`'s WASM smoke test in CI for the real-browser verification, since that part of the loop is slow to run locally.
4. If you're adding a new lab entirely, check `labs/LAB_STRUCTURE_AND_REPORT_CONTRACT.md` and `labs/LAB_SINGLE_SOURCE_OF_TRUTH_POLICY.md` first: they define the mandatory section structure every lab follows, and the rule that quantitative facts must come from `mlsysim`'s registries, not be hardcoded into the lab itself.
