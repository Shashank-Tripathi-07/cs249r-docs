# MLSys·im: Design

*This is the contributor-facing design document for MLSys·im (package name `mlsysim`), a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `mlsysim/` in that repo, with a closely related sibling at `labs/` (the interactive browser labs built on top of it). It explains what MLSys·im is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers real bugs and design decisions that shaped the current architecture, and "Known issues" lists documented gaps and currently open problems, both good places to look for a first contribution.*

## Problem

Reasoning about an ML system's performance, cost, and sustainability before you build it is hard. Empirical benchmarking (actually running the workload on the actual hardware) gives ground truth, but it's slow, expensive, and often impossible early in a design process, when you're still choosing between hardware options, parallelism strategies, or datacenter locations. What's missing is a fast, physics-grounded way to reason about the trade-offs first: is this workload compute-bound or memory-bound on this GPU, will it fit in memory at all, roughly what will it cost, roughly how much carbon will it emit, before you spend a cent on real infrastructure.

MLSys·im is a first-principles analytical modeling framework that answers exactly that class of question. It formalizes classic performance-modeling ideas (the Roofline model, memory-wall analysis, queueing theory for serving, TCO and carbon accounting) into a typed, unit-strict Python engine, backed by provenance-tracked registries of real hardware and real models, so every number it produces traces back to a cited source rather than a hardcoded guess. It exists both as a standalone library and CLI for engineers and researchers, and as the physics engine underneath 34 interactive, browser-based lab exercises that accompany the MLSysBook textbook.

## Goals

- A five-layer analytical stack (workloads, hardware, infrastructure, systems and topology, and an execution engine) that composes cleanly: any workload can be evaluated against any hardware target without hand-writing new modeling code for each combination.
- A Roofline-model-based physics engine, grounded in the "Iron Law" of ML performance (time equals FLOPs divided by effective compute throughput), that classifies a given workload and hardware pairing as compute-bound or memory-bound, checks whether it fits in memory at all, and reports latency, throughput, MFU/HFU, and energy.
- A registry of real hardware (cloud accelerators, workstation, mobile, edge, and tiny devices) and real models (language, vision, tiny, recommendation, state-space, generative-vision), every entry provenance-tracked back to a citation, so results are auditable rather than opaque.
- A CLI (`mlsysim`) that works equally well interactively (Rich-formatted terminal output) and as a scriptable CI/CD tool (strict JSON output with semantic exit codes), plus a declarative `mlsys.yaml` format for full cluster-and-SLA audits as infrastructure-as-code.
- A published, versioned PyPI package (`pip install mlsysim`) with a fully automated, auditable release process: tag-triggered, OIDC-based Trusted Publishing (no stored PyPI token), and automatic verification that the package actually installs and runs correctly straight from PyPI before a release is announced.
- The physics engine and registries powering 34 browser-based interactive labs (two volumes, matched to the MLSysBook textbook's chapters) that run entirely client-side via Pyodide, no server and no installation required for a student.
- Supporting materials that make the framework usable beyond the codebase itself: a Quarto documentation site with auto-generated API reference, a from-scratch research paper, and a full-day ISCA tutorial curriculum.
- Free and open source (Apache-2.0 for the code, CC-BY-NC-SA 4.0 for the documentation content), deployable and forkable by anyone.

## Non-goals

- Not a cycle-accurate hardware simulator. MLSys·im is explicitly positioned against tools like gem5: it trades simulation fidelity for speed and accessibility, answering "roughly what will happen and why" rather than "exactly what will happen down to the clock cycle."
- Not a replacement for empirical benchmarking. The project's own accuracy guidance is explicit: trust it for bottleneck classification and relative comparisons; well-calibrated absolute latency predictions are often within plus or minus 15 to 30 percent, and production serving can be 1.5 to 2 times slower than idealized roofline bounds. Production capacity planning should validate with real benchmarks.
- No GPU or real hardware execution anywhere in the framework itself; every number is computed analytically from registry data and physics formulas.
- The VS Code extension ("MLSysim Workbench") is a separate, independently versioned and packaged artifact. It is not shipped with the PyPI package and is not covered by the same release process.

## Technology stack

Everything MLSys·im uses, what it is, and why it's the right tool for this project.

### Core library

| Technology | What it is | How MLSys·im uses it |
|---|---|---|
| Python | A general-purpose programming language. | The language the entire framework, CLI, and test suite are written in. Supports Python 3.10 through 3.13. |
| NumPy | A numerical computing library. | Used for the numerical routines underneath the physics formulas and solvers. |
| Pint | A Python library for defining, operating on, and converting physical quantities with units attached. | Every physical quantity in the framework (FLOPs, bytes, watts, seconds, dollars) is a Pint `Quantity`, not a bare float, so unit errors (like accidentally comparing gigabytes to gigabits) are caught structurally rather than by convention. |
| Pydantic | A Python data-validation library. | Defines the typed models behind hardware and workload registry entries, and validates the `mlsys.yaml` configuration format before any computation runs on it. |
| PyYAML | A YAML parsing library. | Reads every registry data file (hardware specs, model specs, infrastructure profiles) and any user-supplied `mlsys.yaml` plan. |
| SciPy and OR-Tools | A scientific-computing library, and Google's operations-research/optimization toolkit. | Optional dependencies backing some of the design-space-search optimizers (parallelism, batching, and placement search) alongside a simpler built-in exhaustive-search backend. |

### The CLI

| Technology | What it is | How MLSys·im uses it |
|---|---|---|
| Typer | A Python library for building command-line tools from typed function signatures. | The `mlsysim` CLI (`zoo`, `eval`, `serve`, `audit`, `optimize`, `schema`) is built entirely on Typer. |
| Rich | A Python library for formatted terminal output. | Powers the CLI's interactive tables and panels; carefully kept separate from the strict-JSON output path so scripts and humans never get mixed output on the same stream. |

### Documentation, papers, and slides

| Technology | What it is | How MLSys·im uses it |
|---|---|---|
| Quarto | A publishing system built on Pandoc. | Builds the public documentation site at `mlsysbook.ai/mlsysim/`, including a large tutorials section and an auto-generated API reference. |
| quartodoc | A tool that generates API reference documentation for Python packages inside a Quarto site. | Generates the entire `api/` reference section of the docs site directly from the `mlsysim` package's own docstrings and type signatures, so the reference docs can't drift from the actual code. |
| LaTeX (via `pdflatex`/`bibtex`) and Beamer | A typesetting system, and a LaTeX presentation framework. | Compiles the standalone MLSys·im research paper (`paper/paper.tex`) and the ISCA tutorial's slide decks, both independent of the Quarto site. |
| Inkscape and `rsvg-convert` | Vector-graphics tools. | Convert the paper's SVG figure sources to PDF at build time. |

### Labs and browser deployment

| Technology | What it is | How MLSys·im uses it |
|---|---|---|
| marimo | A reactive Python notebook framework where notebooks are plain, git-friendly `.py` files rather than JSON. | Every one of the 34 interactive labs is authored as a marimo notebook, importing the real `mlsysim` package to do its physics computation. |
| Pyodide | A distribution of CPython compiled to WebAssembly, letting real Python run in a browser. | Every lab, once exported, runs entirely client-side via Pyodide; no server executes any Python for a student session. |
| micropip | Pyodide's in-browser package installer. | Each lab's first cell installs the real `mlsysim` wheel (and a small companion UI package) over HTTP via micropip before the notebook's own code runs, exactly mirroring a normal `pip install`. |
| Playwright | A browser-automation framework for real, headless browsers. | Runs the labs' real-browser regression test, which specifically exists because a past bug (an import-order issue that broke a lab in production) was invisible to every other kind of test and only a real browser caught it. |

### Packaging, release, and CI/CD

| Technology | What it is | How MLSys·im uses it |
|---|---|---|
| Hatchling | A build backend for Python packaging. | Builds the `mlsysim` wheel and source distribution. |
| PyPI Trusted Publishing (OIDC) | A GitHub-Actions-to-PyPI authentication method that uses short-lived, workflow-scoped tokens instead of a long-lived stored secret. | The entire PyPI release step authenticates this way; there is no PyPI API token stored anywhere in the repository's secrets. |
| PEP 740 attestations | A standard for cryptographically attesting that a published package was built by a specific, verifiable CI workflow run. | Every PyPI release carries these attestations, so a consumer can verify the package actually came from this project's CI, not from a compromised or spoofed upload. |
| pytest | Python's standard test framework. | Runs the framework's test suite (over 350 tests as of the initial release), organized by marker (`smoke`, `solver`, `empirical`, `integration`) rather than by directory. |
| GitHub Actions | GitHub's built-in CI/CD platform. | Runs every validation, preview, publish, and PyPI-release workflow described later in this document. |

## Architecture

### The five-layer analytical stack

MLSys·im's own framing is "Progressive Lowering": each layer adds concrete, physical detail on top of the one below, and a computation always flows from an abstract workload down to a concrete, unit-checked answer.

- **Layer A, Workload Representation** (`mlsysim.models`): a workload (a language model, a vision model, and so on) is described in hardware-agnostic terms: parameter count, FLOPs, architecture-specific fields like layer count and hidden dimension. Every workload type implements a `lower()` method that reduces it to a plain `ComputationGraph` of total operations, parameter count, weight bytes, and arithmetic intensity, the minimal information the execution engine actually needs. Concrete entries (`Models.Language.Llama3_70B`, `Models.Vision.ResNet50`, and similar) live as YAML data files, loaded into a registry at import time.
- **Layer B, Hardware Registry** (`mlsysim.hardware`): concrete specs for real silicon, from cloud accelerators like an H100 down to microcontroller-class devices like an ESP32, each with a compute core (peak FLOPs, per-precision throughput), a memory hierarchy (capacity, bandwidth, and for small devices, a separate SRAM/flash tier), interconnect details, TDP, and cost and embodied-carbon figures. Also concrete YAML data, loaded into a registry.
- **Layer C, Infrastructure** (`mlsysim.infrastructure`): the physical facility a workload runs in, modeled as grid carbon intensity, power usage effectiveness (PUE), and water usage effectiveness (WUE), so a training run's carbon and water footprint depends on where, not just what, it runs.
- **Layer D, Systems and Topology** (`mlsysim.systems`): composes hardware into real deployment shapes, nodes (a server with several accelerators and an interconnect), racks, fleets, and network fabrics, so multi-accelerator and multi-node scenarios can be modeled, not just a single device.
- **Layer E, Execution and Resolvers** (`mlsysim.engine`): the actual math engine, described in detail below.

Sitting above all five layers, `Scenarios.*` is the runnable composition layer: a `Scenario` pairs a `Models.*` workload with a `Hardware.*` or `Systems.*` target plus SLA constraints (a latency budget, a power budget), and exposes a single `.evaluate()` call. Separately, `ReferenceStats.*` holds non-executable, citation-backed real-world anchors (for example, typical smartphone battery capacity, or Waymo's sensor data rate), used by the textbook's prose to cite real numbers without hardcoding them redundantly.

### The Roofline engine and the Iron Law

The core of Layer E, `mlsysim.engine.engine`, is explicitly a Roofline-model implementation. Its own documentation states the governing formula directly as "the Iron Law of ML training": time equals FLOPs divided by the product of accelerator count, peak FLOPs, model FLOPs utilization, scaling efficiency, and goodput.

Given a workload and a hardware target, evaluation proceeds in stages: the workload is lowered into a `ComputationGraph`; the hardware's peak throughput for the requested numeric precision is resolved; a feasibility check compares the workload's memory footprint against the hardware's memory capacity (the "memory wall"); if it fits, the engine computes both a compute-bound time (FLOPs divided by effective FLOPs per second) and a memory-bound time (bytes moved divided by effective bandwidth), and the larger of the two determines both the bottleneck classification and the resulting latency. On top of that, the engine adds a per-layer dispatch and framework-overhead tax (grounded in published work on framework overhead as a distinct performance wall), and derives throughput, model FLOPs utilization (MFU), hardware FLOPs utilization (HFU), and an energy-proportional power estimate.

This same Roofline core is reused, not reimplemented, by the higher-level solvers: a distributed-training solver adds communication modeling (ring all-reduce, hierarchical all-reduce, alpha-beta latency-bandwidth crossover) on top of it; a serving solver adds prefill-versus-decode and queueing-theory-based tail-latency modeling; a training-memory solver adds Megatron-LM and ZeRO-style optimizer-state and activation-memory accounting; an economics solver adds total-cost-of-ownership, carbon, and water accounting using the infrastructure layer.

### Solvers and optimizers

Above the base Roofline engine sits a family of over two dozen domain-specific solver classes (single-node, distributed, serving, training-memory, economics, reliability, compression, data-pipeline, and orchestration modeling), all built on a common `BaseSolver`/`ForwardModel` interface, and a smaller family of optimizers (parallelism, batching, placement search) built on a common `BaseOptimizer` interface that searches a design space (for example, over parallelism strategies or batch sizes) for a configuration that satisfies given constraints, backed by either a simple exhaustive search or, optionally, SciPy or OR-Tools.

### The CLI

The `mlsysim` command, built on Typer, has five top-level commands: `zoo` (browse the hardware and model registries), `eval` (the core "evaluate a workload against hardware" command, either via quick CLI flags for a single node or via a full `mlsys.yaml` plan for a multi-node, multi-day audit), `serve` (LLM serving performance, prefill and decode), `audit` (profile the machine actually running the CLI against the Iron Law), `optimize` (design-space search over parallelism, batching, or placement), and `schema` (export the JSON Schema for the `mlsys.yaml` format, for editor autocompletion). Every command supports `text` (the default, Rich-formatted), `json`, `markdown`, and (for some commands) `html` output, with all diagnostic and warning output kept strictly on stderr and the final payload on stdout, and semantic process exit codes (success, bad input, physics failure, SLA failure) so a CI pipeline can branch on the actual result rather than parsing text.

### The `mlsys.yaml` format

A declarative, Pydantic-validated configuration format for a full analytical audit: a `workload` block (which model, batch size, sequence length), a `hardware` block (which accelerator, how many, per-node topology, interconnect bandwidth, precision), an optional `ops` block (which grid region, how many days of operation, for TCO and carbon accounting), and an optional `constraints` block of SLA assertions (for example, that decode latency stays under a threshold), which the CLI checks after evaluation and fails loudly, with a specific `SLA_FAIL` exit code, if violated. This is the mechanism the README calls "Infrastructure as Code" for cluster audits.

### Provenance

Every registry entry and every cited constant in the codebase is expected to carry a `Provenance` record rather than being a bare, unexplained number. The project defines three specific audit gates, enforced by dedicated tooling: registry metadata (every hardware and model entry must cite its source), sourced scalars (physical constants, reference statistics, and calibration coefficients scattered through the codebase must carry a note and, where applicable, a URL), and appendix lineage (the textbook's own generated appendix tables must not reference stale or removed registry paths). This is a deliberate design stance: the project's own documentation states plainly that citation prose belongs outside the package, but structured lineage belongs inside it, so every number the framework produces can be traced back to why it has that value.

### Registries, formatting, and display

Two related but distinct formatting layers exist in the codebase, and it's worth knowing which is which: `mlsysim/show.py` provides small, plain-print tutorial display helpers meant for use inside notebooks, while `mlsysim/fmt.py` is a much larger suite of numeric and prose formatters specifically for the MLSysBook textbook's own Quarto prose pipeline (so that, for example, a dollar figure or a percentage renders correctly inside Quarto's markdown-and-math processing without being mangled). Neither of these is the CLI's own output layer; CLI rendering lives separately in `mlsysim/cli/renderers.py`.

### Labs: browser-based interactive exercises

The `labs/` directory (a sibling to `mlsysim/` in the repository, not inside the `mlsysim` package) hosts 34 interactive lab exercises across two volumes, matched chapter-for-chapter to the MLSysBook textbook: Volume I ("Foundations," labs 00 through 16) and Volume II ("Systems at Scale," labs 01 through 17). Each lab is authored as a marimo notebook, a reactive Python notebook stored as a plain, diffable `.py` file rather than JSON, and every lab imports the real `mlsysim` package to do its actual physics computation; there is no separate, simplified "teaching" implementation of the engine.

Labs run entirely client-side. At build time, the `mlsysim` package (and a small companion UI/metadata package, `mlsysbook_labs`) are compiled into wheels; each lab's own first cell detects whether it's running under Pyodide (`sys.platform == "emscripten"`) and, if so, installs those exact wheels over HTTP via `micropip` before anything else runs. This means the browser labs are running the literal same `mlsysim` code as the PyPI package and the CLI, not a JavaScript reimplementation or a mocked subset.

A shared UI toolkit lives inside the `mlsysim` package itself, at `mlsysim.labs` (a subpackage, distinct from the top-level `labs/` directory): reusable marimo components (cards, metric rows, comparison rows, a Roofline visualizer, a latency waterfall) and a shared design system (color tokens, CSS, progress bars) that every lab notebook imports, so the 34 labs share one visual language instead of each reinventing it.

Student progress persistence across all 34 labs is handled by a single `DesignLedger` class in `mlsysim.labs.state`, which detects whether it's running natively (writes a JSON file to disk) or under Pyodide (writes to the browser's IndexedDB via an embedded JavaScript bridge). See "Known issues" below for a real, currently open bug in exactly this bridge.

### Documentation site

The public documentation site (`mlsysbook.ai/mlsysim/`) is a Quarto project rooted at `mlsysim/docs/`. It includes a getting-started guide, a CLI reference, a large tutorials section (both a self-paced set of Quarto lessons and companion pages for the in-person ISCA tutorial), a "Zoo" section documenting the hardware and model registries, role-specific landing pages (for students, instructors, and engineers), and an API reference section generated automatically from the package's own code via `quartodoc`, so it cannot drift out of sync with the actual public API the way hand-written reference docs can.

### Testing

The test suite (`mlsysim/tests/`) is a flat directory of over 30 files, organized by pytest marker rather than by subdirectory: `smoke` (a fast subset for pull-request CI checks), `solver` (individual solver correctness), `empirical` (validation against published external benchmarks like MLPerf and Chinchilla scaling results), and `integration` (cross-solver and end-to-end tests). Coverage spans formula-level unit tests, per-solver-backend tests (a built-in exhaustive backend, plus optional SciPy and OR-Tools backends), registry-integrity tests (no duplicate hardware or model specs, every entry loadable and provenance-complete), a CLI contract test, and a dedicated set of golden-regression tests pinning known-good numeric outputs.

### Packaging and release

MLSys·im is published to PyPI (`pypi.org/project/mlsysim/`) via a fully automated, tag-triggered pipeline: pushing a `mlsysim-vX.Y.Z` tag kicks off a workflow that first verifies version coherence across every file that declares a version (`pyproject.toml`, the package's own `__init__.py`, the citation metadata file, and the changelog), then re-runs the full test suite across every supported Python version, then builds and checks the distribution, then publishes to PyPI using Trusted Publishing (no stored token) with cryptographic build attestations, then actually installs the freshly published package from real PyPI and runs a CLI smoke test against it before creating a GitHub Release and triggering a documentation-site redeploy. This end-to-end verify-after-publish step exists specifically so a broken release can't silently reach users; if the just-published package doesn't actually import and run, the release process itself catches it.

### CI/CD

Separate validate, preview, and publish workflows exist for `mlsysim` (the Python package) and for `labs` (the browser exercises), since they have different concerns and different release cadences, though `labs` depends on `mlsysim` and both workflow families watch changes to `mlsysim/**`.

- **`mlsysim-validate-dev.yml`**: runs the full test suite and a documentation-site build check on every relevant pull request and push.
- **`mlsysim-preview-dev.yml`** and **`mlsysim-publish-live.yml`**: build and deploy the documentation site to a dev preview and to production (`mlsysbook.ai/mlsysim/`) respectively, the latter manual-only with a typed confirmation.
- **`mlsysim-pypi-publish.yml`**: the release pipeline described above.
- **`mlsysim-build-pdfs.yml`** and **`mlsysim-update-pdfs.yml`**: build the research paper and ISCA tutorial slide decks as artifacts, and support a PDF-only refresh of the published downloads without a full site rebuild.
- **`labs-validate-dev.yml`**: runs the labs' own multi-level test suite, then a dedicated WASM smoke-test job that builds real wheels for `mlsysim` and the labs helper package, verifies a Pyodide runtime can install and import them in Node, exports a representative set of labs (deliberately including the one lab that previously shipped broken to production, see "Project history") to real WASM HTML, and runs them in a real, headless, Playwright-driven Chromium browser, since past experience showed that every other kind of check (static analysis, engine unit tests, even a Node-based Pyodide import check) can pass while the actual browser experience is broken.
- **`labs-preview-dev.yml`** and **`labs-publish-live.yml`**: build and deploy the labs site to dev preview and to production (`mlsysbook.ai/labs/`), gated on the validate workflow being green.

## Known issues

These are good starting points if you're looking for a first contribution.

- **`DesignLedger.save_async()` in `mlsysim/mlsysim/labs/state.py` has a real, currently unresolved silent-data-loss bug.** It bridges a serialized state string from Python to an embedded JavaScript IndexedDB write via `globalThis.__mlsys_temp_state`, a double-underscore-prefixed attribute written inside a class method. Python's name-mangling rule silently rewrites any `__name` attribute assignment made inside a class body to `_ClassName__name`; the embedded JavaScript, being a plain string literal that Python never parses, still reads the unmangled `globalThis.__mlsys_temp_state`, a variable that is therefore never actually set by the Python side. The practical effect is that `save_async()` can report success while never having written the real value, meaning a student's lab progress silently fails to persist. A fix exists as an open pull request (renaming the bridge variable to a single-underscore name, which Python does not mangle, and switching the IndexedDB resolution point from the individual write request's success callback to the transaction's completion callback for a more durable commit signal), verified against real Pyodide and real IndexedDB in a headless browser, but it is not yet merged as of this document.
- **Two separate, conflicting pytest configuration blocks exist for the same package.** `mlsysim/pyproject.toml` has a `[tool.pytest.ini_options]` section, but a standalone `mlsysim/pytest.ini` also exists at the repository root and takes precedence whenever both are present, meaning the `pyproject.toml` block is currently inert. The two also aren't fully redundant: only `pytest.ini` declares the project's custom markers (`smoke`, `solver`, `empirical`, `integration`).
- **`DesignLedger.get_baseline()` is an acknowledged stub.** Its docstring describes providing a "Gold Standard" baseline for students who haven't completed previous labs, but the method currently just returns an empty dictionary.
- **The `IOInterconnect` hardware type's `direction` field exists specifically to prevent a modeling pitfall that's easy to reintroduce.** NVLink and similar interconnects are often quoted as a single bidirectional bandwidth figure in vendor datasheets, but a naive model that treats that number as available in each direction simultaneously silently overstates achievable bandwidth by up to two times. Any new hardware entry or new interconnect-aware solver needs to get this distinction right; it isn't automatically enforced beyond the type system requiring the field.

## Project history

- **A lab shipped broken to production because `plotly` was imported before `micropip.install(...)` completed**, tracked as issue #1353. Every non-browser check (static analysis, the native-Python engine tests, even a Node.js-based Pyodide import smoke test) passed; only an actual browser session failed, because the import ordering only matters under Pyodide's real, asynchronous package-loading behavior. This is the direct reason `labs-validate-dev.yml` runs a dedicated Playwright-driven, real-headless-Chromium smoke test as part of every validation run, and specifically why that smoke test always includes the exact lab that broke, not just an arbitrary sample.
- **A related browser-only failure mode, tracked as issue #1388, involved a lab appearing to finish loading (the network went idle) while a tabbed UI component was actually still stalled.** The browser smoke test was extended with an additional check specifically for labs using tabbed UI, waiting for the tab content itself to render rather than trusting network-idle state alone as a sign the lab had actually finished loading.
- **The `mlsysim` package's dependency floors for `pydantic` and `numpy` were chosen for a non-obvious reason: matching what's already bundled inside the two Pyodide runtimes the labs run under (browser Pyodide and Node Pyodide), since neither will upgrade a package at WASM runtime.** A dependency declared with a floor higher than what Pyodide actually bundles would fail exactly the WASM smoke test described above, with a confusing "requested version X but version Y is already installed" error, even though the same code works fine natively. The floors are documented inline in `pyproject.toml` with this exact reasoning, specifically so a future contributor bumping a dependency doesn't reintroduce the failure by "helpfully" tightening the version floor.
- **Version 0.1.1 was a metadata-only patch release,** correcting the research paper's title as it appeared in the citation file, README, and one source-code docstring, with no code or API changes at all, a reminder that not every tagged release implies a functional change.
- **Version 0.1.2, "CLI and Website Release Polish,"** added a `prefill_chunk_tokens` field across several serving and training solvers, and fixed two CLI correctness issues: where the `-o`/`--output` flag was accepted in the argument order, and a case where the `audit` command's JSON output mode wasn't actually pure JSON on stdout.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the full file map, real code from the engine, CLI, and labs integration, local setup steps, and common contribution workflows. The "Known issues" list above is a reasonable place to find a first task, and the "Project history" section shows the kind of bug that tends to surface in this codebase (silent failures at the Python-to-JavaScript boundary inside WASM, and dependency-version assumptions that only break under Pyodide's specific runtime constraints) so you know what to watch for when reviewing your own changes.
