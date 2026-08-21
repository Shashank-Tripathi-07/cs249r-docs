# CS249r docs

Contributor-facing design and implementation documentation for sub-projects of [`harvard-edge/cs249r_book`](https://github.com/harvard-edge/cs249r_book) (the "Machine Learning Systems" course repository).

## Start here

New to the repo? Read [`ecosystem-map.md`](ecosystem-map.md) first, then jump to whichever project below you're contributing to.

| Doc | What it's for |
|---|---|
| [`ecosystem-map.md`](ecosystem-map.md) | How all eleven projects actually connect: shared infrastructure, deployment order, real dependencies versus apparent ones. |
| [`design_decisions.md`](design_decisions.md) | What Prof. Vijay Janapa Reddi has actually decided, rejected, or ruled out, sourced from real GitHub comments with dates and links, not inferred from code. Four projects (book, StaffML, TinyTorch, labs) also have their own shorter `perspective.md`, linked in their sections below. |
| [`pr-history.md`](pr-history.md) | A complete, data-derived record of every merged PR since 2023 (1208 and counting): contributor rankings, a monthly timeline, and the full chronological table. |
| [`stats.md`](stats.md) | A per-project cheat sheet of counts, how many labs, how many TinyTorch modules, how many dependencies StaffML actually has, dated so you know when it was last true. |
| [`glossary.md`](glossary.md) | Plain-language definitions for every recurring technical term, from Quarto and Pyodide to the roofline model and CORS. Keep it open in a tab. |
| [`ci-workflows.md`](ci-workflows.md) | All 66 GitHub Actions workflows, repo-wide and per-project, with real dated CI incidents mined from git history. Each project's own `ci-workflows.md` (linked below) covers just that project. |
| [`troubleshooting.md`](troubleshooting.md) | A symptom-first lookup table across every documented incident, for when you're staring at a red run right now. |
| [`release-process.md`](release-process.md) | How `dev` reaches the live site and PyPI: the publish-guard gate, `site_only` mode, and each project's own release quirks. |
| [`dependency-map.md`](dependency-map.md) | The full 1,629-package dependency graph: what each project actually declares directly, and what's shared across projects in non-obvious ways. |
| [`packages.md`](packages.md) | `dependency-map.md`'s exhaustive companion, an alphabetical, ctrl+F-able table of all 1,629 packages, direct and transitive, with which project pulls in each one and through what. |
| [`coding-style.md`](coding-style.md) | The honest answer to "what linter applies here": there's no single repo-wide style, three Python setups and one real TypeScript config. Each project's own `coding-style.md` covers its specific setup. |

## Projects

[`book/`](#book) · [`staffml/`](#staffml) · [`tinytorch/`](#tinytorch) · [`mlsysim/`](#mlsysim) · [`labs/`](#labs) · [`kits/`](#kits) · [`mlperf-edu/`](#mlperf-edu) · [`socratiq/`](#socratiq) · [`design-grammar/`](#design-grammar) · [`slides/`](#slides) · [`instructors/`](#instructors) · [`site/`](#site)

Every project has the same core doc set (`design.md`, `implementation.md`, `system_design.md`, `ci-workflows.md`, `coding-style.md`); a project only lists the ones it actually has, and only adds a one-line note when the doc covers something non-obvious.

### `book/`

The core two-volume textbook, plus its custom build/validate/publish tooling (the "Binder" CLI).

- [`design.md`](book/design.md)
- [`implementation.md`](book/implementation.md)
- [`system_design.md`](book/system_design.md): the Binder CLI's command dispatch, its content-validation check suite, and the real build chain from a `.qmd` chapter to HTML/PDF/EPUB.
- [`ci-workflows.md`](book/ci-workflows.md)
- [`coding-style.md`](book/coding-style.md)
- [`perspective.md`](book/perspective.md): what Prof. Vijay has explicitly decided or ruled out for the book, the two-volume scope narrowing, quiz-objective coupling, caption format, and build infrastructure calls.

### `staffml/`

Interview-prep question bank and practice app.

- [`design.md`](staffml/design.md)
- [`implementation.md`](staffml/implementation.md)
- [`system_design.md`](staffml/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the vault pipeline or the Workers backend.
- [`ci-workflows.md`](staffml/ci-workflows.md)
- [`coding-style.md`](staffml/coding-style.md)
- [`perspective.md`](staffml/perspective.md): the discuss-before-you-PR expectation for new features, stated directly by a collaborator and reinforced by Vijay's own review pattern.
- Whitepaper: [`interviews/paper/paper.tex`](https://github.com/harvard-edge/cs249r_book/blob/dev/interviews/paper/paper.tex), "StaffML: A Physics-Grounded Interview Question Bank for Machine Learning Systems Engineers." Makes the case for testing "mechanical sympathy" (quantitative hardware reasoning) over algorithmic coding puzzles; describes the question bank's four-axis classification and its validated LinkML/Pydantic schema. Compiled to PDF in CI, separately from the Quarto-built textbook.

### `tinytorch/`

Hands-on course where students build an ML framework from scratch.

- [`design.md`](tinytorch/design.md)
- [`implementation.md`](tinytorch/implementation.md)
- [`system_design.md`](tinytorch/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the export pipeline or the milestone system.
- [`ci-workflows.md`](tinytorch/ci-workflows.md)
- [`coding-style.md`](tinytorch/coding-style.md)
- [`perspective.md`](tinytorch/perspective.md): why solutions are still visible in module source, the "durable foundation" bar for new core-module algorithms, and the confirmed `src/` source-of-truth and `dev`-branch-target rules.
- [`command-reference.md`](tinytorch/command-reference.md): every `tito` CLI command and flag, grepped directly from `tito/main.py` and `tito/commands/`, not from any existing help text.
- [`deep-dive.md`](tinytorch/deep-dive.md): how TinyTorch actually works end to end, from `install.sh` through module export to grading, sourced from reading the real code and a real installed environment on disk, not estimates.
- Whitepaper: [`tinytorch/paper/paper.tex`](https://github.com/harvard-edge/cs249r_book/blob/dev/tinytorch/paper/paper.tex), "TinyTorch: Building Machine Learning Systems from First Principles," by Vijay Janapa Reddi. Argues ML education has an "algorithm-systems divide" and presents the 20-module build-your-own-framework curriculum as the fix, runnable on 4GB RAM with no GPU. Compiled to PDF in CI, separately from the Quarto-built textbook.

### `mlsysim/`

First-principles analytical modeling framework for ML systems, also the physics engine behind the browser-based interactive labs.

- [`design.md`](mlsysim/design.md)
- [`implementation.md`](mlsysim/implementation.md)
- [`system_design.md`](mlsysim/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the physics core or a solver backend.
- [`ci-workflows.md`](mlsysim/ci-workflows.md)
- [`coding-style.md`](mlsysim/coding-style.md)

### `labs/`

The 34 browser-based interactive labs (Volumes I and II), built on the mlsysim engine and exported to WASM via marimo.

- [`system_design.md`](labs/system_design.md): dependencies, components, the export and boot sequence, and progress persistence, for a contributor adding a lab or debugging the WASM path. Labs share their product framing with [`mlsysim/design.md`](mlsysim/design.md); there is no separate `design.md` for this folder.
- [`ci-workflows.md`](labs/ci-workflows.md)
- [`coding-style.md`](labs/coding-style.md)
- [`perspective.md`](labs/perspective.md): the corrected scope of the marimo widget-cascade bug and the PR that fixed it the wrong way first, plus how new hardware/board proposals get evaluated.

### `kits/`

Hands-on embedded ML labs for real devices (Arduino, Seeed, Raspberry Pi).

- [`design.md`](kits/design.md)
- [`implementation.md`](kits/implementation.md)
- [`system_design.md`](kits/system_design.md): dependencies, content structure, and the build chain from a lab `.qmd` to a deployed site plus a downloadable PDF.
- [`ci-workflows.md`](kits/ci-workflows.md)
- [`coding-style.md`](kits/coding-style.md)

### `mlperf-edu/`

A locally executable, quality-gated benchmark specification adapted from MLPerf's own discipline for classroom use.

- [`design.md`](mlperf-edu/design.md)
- [`implementation.md`](mlperf-edu/implementation.md)
- [`system_design.md`](mlperf-edu/system_design.md): the workload registry, the provenance-manifest anti-cheat system, and how the GraderPipeline class from PR #1933 was later deleted and replaced with in-process grading.
- [`ci-workflows.md`](mlperf-edu/ci-workflows.md)
- [`coding-style.md`](mlperf-edu/coding-style.md)

### `socratiq/`

An embeddable, AI-powered learning widget for static HTML pages.

- [`design.md`](socratiq/design.md)
- [`implementation.md`](socratiq/implementation.md)
- [`system_design.md`](socratiq/system_design.md): the chat provider-fallback chain, the Shadow DOM and IndexedDB persistence, the XSS sanitization boundary, and the fully offline quiz fallback.
- [`ci-workflows.md`](socratiq/ci-workflows.md)
- [`coding-style.md`](socratiq/coding-style.md)

### `design-grammar/`

A formal vocabulary and rewrite-rule catalog for deriving ML systems techniques from first principles.

- [`design.md`](design-grammar/design.md)
- [`implementation.md`](design-grammar/implementation.md)
- [`system_design.md`](design-grammar/system_design.md): `grammar.yml` as the single source of truth, and the verified sync mechanism that generates StaffML's `/framework` page from it.
- [`ci-workflows.md`](design-grammar/ci-workflows.md)
- [`coding-style.md`](design-grammar/coding-style.md)

### `slides/`

35 Beamer decks (Volumes I and II) plus packaged TinyML courseware.

- [`design.md`](slides/design.md)
- [`implementation.md`](slides/implementation.md)
- [`system_design.md`](slides/system_design.md): the shared Beamer theme, the SVG-to-PDF-to-PPTX build chain, and why the compile engine is `pdflatex`, not `xelatex`.
- [`ci-workflows.md`](slides/ci-workflows.md)
- [`coding-style.md`](slides/coding-style.md)

### `instructors/`

"The Blueprint": syllabi, pedagogy, assessment, and TA guidance for adopting instructors.

- [`design.md`](instructors/design.md)
- [`implementation.md`](instructors/implementation.md)
- [`system_design.md`](instructors/system_design.md): a short, honest non-finding, this project has no custom code at all, pure Quarto content.
- [`ci-workflows.md`](instructors/ci-workflows.md)
- [`coding-style.md`](instructors/coding-style.md)

### `site/`

The ecosystem's public front door: home, about, community, newsletter, and mini-games.

- [`design.md`](site/design.md)
- [`implementation.md`](site/implementation.md)
- [`system_design.md`](site/system_design.md): the newsletter sync CLI, the two-stage stats pipeline, and the mini-games.
- [`ci-workflows.md`](site/ci-workflows.md)
- [`coding-style.md`](site/coding-style.md)

---

Start with each project's `design.md`, then use its `implementation.md` as your map once you're ready to make a change. All documents describe their project as of `dev` HEAD (commit `8fb87d81`) in the main repository.
