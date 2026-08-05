# MLPerf EDU: Design

*This is the contributor-facing design document for MLPerf EDU, a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `mlperf-edu/` in that repo. It explains what MLPerf EDU is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers real audit and readiness reviews that shaped the current state, and "Known issues" lists what's genuinely still incomplete, since this project is explicit about its own limitations.*

## Problem

Benchmarking discipline exists on a spectrum. At one end, classroom benchmarking exercises are typically informal: run a model, note a number, move on, with no real quality gate, no measurement-boundary discipline, no provenance trail, and no way to know if a comparison between two students' results is actually valid. At the other end, production benchmark suites like MLPerf enforce exactly that discipline, reproducibility, verification, disclosure, comparability, but are built for datacenter-scale submissions with an operational weight (committee review, multi-organization coordination, extensive infrastructure) that's completely impractical for a single class period or a single graded assignment.

MLPerf EDU exists in the gap between those two extremes: a locally executable, quality-gated benchmark specification that adapts the actual discipline of a mature benchmark suite, not just its name, down to something a student can run on a laptop in a single sitting, while still producing evidence rigorous enough that a "my model is faster" claim can actually be checked rather than taken on faith.

## Goals

- A curated, not invented, portfolio of benchmark workloads. Every task, model, dataset, and evaluator traces back to an authoritative upstream definition (MLPerf Tiny, nanoGPT, DistilBERT and SST-2, OGB, PatchTST, and others); this project never substitutes an easier, project-invented task when an authoritative one is inconvenient to run locally.
- A stable workload identity system: what a workload is (its mode, phase, and profile) stays fixed, while batching, precision, quantization, and similar knobs are just configuration on top of that fixed identity, never a reason to mint a new, incomparable workload.
- A staged evidence model (functional integration, then quality conformance, then stabilization, then promotion) so a workload's status is always honestly represented, "this runs" is a clearly different, weaker claim than "this is a promoted, verified score," and the project never conflates the two.
- A hard rule that a fast, invalid result is never presented as a benchmark result: every score-bearing run must pass its inherited quality gate before its timing is interpreted as meaningful at all.
- A concrete, auditable Five-Run Promotion Protocol: what it actually takes for a result to count as promoted, repeatable evidence, not just a single lucky run.
- Full provenance on every registered run: a cryptographically hashed manifest binding the report to its exact inputs, so tampering (even if not authorship) can always be independently verified.
- An honest, explicit non-endorsement stance: MLPerf EDU is not an official MLCommons benchmark, is not endorsed by MLCommons, and never claims to be, in its rules, its CLI output, or its published results.
- A single CLI (`mlperf`) that covers the whole lifecycle: environment setup, fetching assets, running a workload, verifying evidence, generating reports, packaging a submission for grading, and grading it.
- A supported assignment and grading workflow, so an instructor can define a lightweight assignment (for example, one functional run of one workload) and grade a student's packaged submission automatically against it.

## Non-goals

- Not an official MLCommons benchmark, and not seeking to become one as its primary goal; the project is explicit that it is an independent, unaffiliated adaptation of MLPerf's discipline, not a step toward MLCommons submission.
- Not distributed or datacenter-scale benchmarking. Every workload is scoped to what's locally executable on a single node; claims of distributed or datacenter relevance are explicitly listed as a prohibited claim in the project's own public rules.
- Not a benchmark suite where every workload is fully working yet. As of this document, two of the fourteen workloads in the portfolio (a recommendation task and a reinforcement-learning task) have complete, fail-closed runner code but don't yet run locally end to end, due to real resource constraints (a very large checkpoint, and a legacy runtime dependency respectively); the project documents this honestly rather than hiding it.
- Not a project that fills a missing authoritative benchmark component with an invented substitute. If an upstream task, model, or evaluator isn't available, the workload stays blocked rather than being quietly swapped for something easier.

## Technology stack

| Technology | What it is | How MLPerf EDU uses it |
|---|---|---|
| Python | A general-purpose programming language. | The entire benchmark harness, CLI, workload runners, and reference implementations are written in Python. |
| PyTorch and torchvision | A deep-learning framework and its companion vision library. | The execution substrate every workload runner actually trains or runs inference with. |
| A hand-rolled `argparse`-based CLI (`mlperf` / `mlperf-edu`) | Two console-script entry points pointing at the same underlying command implementation. | The single interface covering the whole benchmark lifecycle: environment checks, asset fetching, running, verifying, reporting, packaging, and grading. |
| SHA-256 hashing, applied throughout | A cryptographic hash function. | Backs the project's entire provenance system: every report, dataset split, model checkpoint, and artifact is hashed, and a `.provd.json` manifest binds a report to the exact inputs that produced it, so tampering (though not authorship) can always be independently detected. |
| `torch-geometric` and `ogb` (the Open Graph Benchmark library) | A graph-neural-network library, and the reference toolkit for a widely used node-classification benchmark. | Power the graph-domain workload, using the authoritative OGB dataset split and evaluator rather than a project-invented graph task. |
| `transformers` and `sentence-transformers` | Hugging Face's model and embedding libraries. | Power the language-domain workloads, including causal language modeling and retrieval/reranking tasks. |
| `ai-edge-litert` (TensorFlow Lite Runtime) | A lightweight, mobile-oriented inference runtime. | Used specifically for a cross-backend parity check, verifying that a PyTorch model and its TFLite-converted counterpart produce matching predictions, as part of the project's conformance auditing rather than as a primary execution path. |
| `uv` | A fast Python package and project manager. | The project's primary supported install and dependency-lock mechanism, used both for local development and in CI. |
| Quarto | A publishing system built on Pandoc. | Builds the project's documentation site, published at `mlsysbook.ai/mlperf-edu/`, explicitly scoped in the project's own documentation as a preview of the docs only, never a claim of package publication or MLCommons endorsement. |
| LaTeX | A typesetting system. | Builds a standalone research paper, "MLPerf EDU: A Pedagogical Evaluation Framework for AI Systems Benchmarking," describing the project's design and v0.1 registry status. |

## Architecture

### Workload identity: stable across configuration

A workload's identity is defined by its mode (training or inference), phase (full, prefill, or decode, for models where that distinction matters), and profile (a named configuration tier, for example `min` for a quick functional check versus `pro` for a full, comparable classroom run). Batching, numeric precision, and quantization choices are configuration layered on top of that identity, never a reason to mint a new workload identity. This matters because it's what makes results comparable at all: two runs of the same workload identity with the same profile are meant to be directly comparable, even if their configuration knobs differ slightly, while a workload with a genuinely different identity is never presented as comparable to another.

### The evidence spiral: four honest stages

Every workload progresses through four stages, and the project is explicit that these stages are not interchangeable claims:

1. **Functional integration**: the execution, reporting, and provenance machinery works for this workload. No quality or timing claim is made yet.
2. **Quality conformance**: the workload uses its pinned model, its full authoritative dataset, its authoritative evaluator, and has a published target to compare against.
3. **Stabilization**: repeated fresh-process runs demonstrate timing repeatability (a coefficient of variation at or below 5%).
4. **Promotion**: one complete, source-locked evidence set has been reviewed and imported as the workload's canonical, promotable result.

A workload can sit at any of these four stages, and the project's own status tracking (see "Project history") is explicit about exactly which stage each of the fourteen portfolio workloads is currently at, rather than presenting them as uniformly "done."

### Quality gates performance, always

The project's own stated principle: "a fast invalid model is not a benchmark result." Every score-bearing run must pass its inherited quality contract, the workload's accuracy or correctness threshold, drawn from its authoritative upstream definition, before its timing is interpreted as meaningful at all. No aggregate or median timing figure is allowed to hide a run that individually failed its quality gate; every run in a promoted evidence set must pass independently.

### The Five-Run Promotion Protocol

The concrete mechanism behind "promoted" evidence: five fresh, independent operating-system processes are run at a fixed, canonical random seed. Every one of the five must complete without a timeout or artifact loss; every quality and functional gate must pass on every run; the aggregate timing's coefficient of variation must be at or below 5%; every run must share the same comparison fingerprint (the same hardware and software configuration signature); the source tree must be clean and bound to exactly one git commit; and every report, manifest, and artifact digest from all five runs is preserved. A single failed process invalidates the entire five-run attempt, there's no partial credit or best-of-five selection.

### Provenance and verification

Every registered run writes a JSON report, a CSV, an HTML rendering, and a `.provd.json` provenance manifest, a SHA-256-hashed binding between the report and the exact inputs (dataset digests, model digests, source commit) that produced it. Verifying a `.provd.json` manifest can detect tampering, someone editing a report after the fact, but the project is explicit that this is integrity verification, not authorship authentication; a hash proves the file matches what was recorded, not who recorded it.

### Result roles: five honest categories

Every result is classified into one of five roles, so a reader always knows exactly how much weight to put on a number: **score-bearing** (a full metric and timing result, after the quality gate passed), **performance-bearing** (timing only, after just the lighter functional gate), **systems-only** (an observation, not a comparable score), **deferred** (no admitted local-hardware contract exists yet for this case), or **rejected** (the result failed the project's own admission rules outright).

### Measurement boundary discipline

Asset fetching, model construction, and untimed warmup are explicitly excluded from the timed region of a benchmark run (unless a workload's own upstream contract specifies otherwise), accelerator operations are synchronized at timing boundaries so asynchronous execution doesn't produce a misleadingly short measured time, and the power source and power mode are recorded; any sleep or power-state change during a timed run invalidates that attempt entirely.

### The CLI's two audiences

The CLI's own help text distinguishes a "common user path" (`init`, `health`, `list`, `fetch`, `run`, `report`, the commands a student actually uses to run a benchmark and see their result) from an "instructor and maintainer path" (`audit`, `validate`, `grade`, the commands used to check registry and license health, run the bundled validation presets, and grade a student's packaged submission). Both paths go through the same single binary; the split is about intended audience, not a separate tool.

### The assignment and grading workflow

An instructor defines a lightweight assignment as a small YAML file (for example, requiring one functional, non-quality-gated run of a specific workload). A student packages their run into a portable zip via the CLI's `package` command; the instructor (or an automated grading pipeline) runs `grade` against that package and the assignment definition to produce a pass/fail or scored result. This is deliberately lightweight compared to the full Five-Run Promotion Protocol, since a graded classroom assignment and a promoted, public benchmark result are different use cases with different evidentiary bars, and the project keeps them as explicitly distinct paths.

### Documentation as an interface, not just an artifact

A substantial share of this project's own top-level files are structured review and audit documents (a design-philosophy statement, public rules, a quality-target review justifying every workload's specific numeric gate, a dataset-release review tracking licensing status per dataset, a simulated independent audit, and a readiness tracker), not just narrative documentation. This reflects a project-level design choice: the rules and their justifications are treated as load-bearing project infrastructure, reviewed and versioned the same way code is, not an afterthought written once and left stale.

## Known issues

These are good starting points if you're looking for a first contribution. Notably, several of these are things the project's own documentation already tracks openly, rather than gaps discovered by outside review.

- **Two of the fourteen portfolio workloads don't run locally end to end yet.** A recommendation-domain workload needs an out-of-core CPU backend to handle a very large checkpoint that doesn't fit in typical laptop memory; a reinforcement-learning workload currently depends on a legacy TensorFlow 1/CUDA runtime that doesn't run on modern hardware without significant rework. Both have complete, fail-closed runner code, meaning they correctly refuse to produce misleading results rather than silently degrading, but neither currently produces a real local result. The project's own `LOCAL_EXECUTION_PLAN.md` has detailed, staged implementation plans for closing both gaps.
- **A documented, planned CLI surface doesn't exist yet.** The project's own local-execution planning document explicitly labels `doctor --local`, `run --resume`, and `fetch --collection all` as "planned CLI surface, not yet implemented," and says so directly in its own text. If you're looking for a self-contained CLI feature to build, this is an explicit, pre-scoped starting point rather than something you'd need to propose from scratch.
- **A large share of the workload portfolio's quality-gate status is "conditional" or "missed" rather than fully passing, and the project documents this itself.** Its own simulated independent audit records exactly which workloads currently pass their target cleanly, which pass conditionally, and which currently miss, rather than presenting a uniformly green status. Treat any claim about "how many workloads work" with that per-workload nuance rather than a single aggregate number.
- **Dataset licensing and public-release status is unresolved for a meaningful share of the registered datasets**, tracked explicitly in the project's own dataset release review, with several datasets currently blocked on a licensing decision before they can be freely redistributed, and the project's stated policy is to never silently substitute a synthetic or reduced dataset to work around that, so those workloads simply stay in a "fetch instructions only" state until the licensing question is resolved.

## Project history

- **The project's own "Independent Audit" is explicitly a simulated one**, conducted internally by cycling through three reader personas, a student, an instructor, and a benchmark reviewer, rather than a genuine external, third-party review. It's documented this way deliberately and transparently, not presented as if it were an outside audit, and its own sign-off boundary is narrow: it approves the suite for use as "an experimental, supervised classroom and research design preview," explicitly not for unsupervised public release, promoted baselines, or external benchmark governance claims.
- **The readiness tracker distinguishes an "initial usability" milestone from a separate, later "production readiness" milestone**, and is explicit that things like licensing sign-off, MLCommons review, signed releases, and independent reproduction are production-readiness concerns that are deliberately out of scope for the current, local-execution-focused stage of the project.
- **A real cross-backend parity audit (PyTorch versus TFLite) is already part of the project's conformance-checking history**, comparing prediction outputs and accuracy between the two backends on the image-classification workload and recording the result (including, in at least one recorded audit, an overall run status of "failed" because at least one workload in that audit run didn't pass), a concrete instance of the project's verification tooling actually catching and honestly recording a failure rather than only ever reporting clean passes.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, the real CLI command table, the harness and registry code, the provenance and grading mechanics, local setup steps, and common contribution workflows. The "Known issues" list above, especially the two blocked workloads and the explicitly-planned-but-unimplemented CLI surface, is a good place to find a first task, since the project has already done the scoping work for you.
