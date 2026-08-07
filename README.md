# CS249r docs

Contributor-facing design and implementation documentation for sub-projects of [`harvard-edge/cs249r_book`](https://github.com/harvard-edge/cs249r_book) (the "Machine Learning Systems" course repository).

New here? Start with [`ecosystem-map.md`](ecosystem-map.md) for how all eleven projects below actually connect to each other (shared infrastructure, deployment order, real dependencies versus apparent ones), then drill into whichever project you're contributing to. Keep [`glossary.md`](glossary.md) open alongside whatever you're reading; it defines every recurring technical term in plain language, from Quarto and Pyodide to the roofline model and CORS. If you're debugging a CI failure or a red badge, [`ci-workflows.md`](ci-workflows.md) inventories all 66 GitHub Actions workflows in the main repo, per-project and repo-wide, with real, dated CI incidents mined from git history documented at the end; each project's own `ci-workflows.md` (linked below) covers just that project's workflows. If you're reviewing a dependency bump, [`dependency-map.md`](dependency-map.md) covers the full 1,629-package dependency graph, what each project actually declares directly, and which dependencies are shared across projects in ways that aren't obvious from any single manifest file; [`packages.md`](packages.md) is its exhaustive companion, an alphabetical, ctrl+F-able table of all 1,629 packages (direct and transitive) with which project pulls in each one and through what, built specifically so a Dependabot PR for an unrecognized package name has a fast answer.

## [`book/`](book/)

The core two-volume textbook, plus its custom build/validate/publish tooling (the "Binder" CLI).

- [`design.md`](book/design.md)
- [`implementation.md`](book/implementation.md)
- [`system_design.md`](book/system_design.md): the Binder CLI's command dispatch, its content-validation check suite, and the real build chain from a `.qmd` chapter to HTML/PDF/EPUB.
- [`ci-workflows.md`](book/ci-workflows.md)

## [`staffml/`](staffml/)

Interview-prep question bank and practice app.

- [`design.md`](staffml/design.md)
- [`implementation.md`](staffml/implementation.md)
- [`system_design.md`](staffml/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the vault pipeline or the Workers backend.
- [`ci-workflows.md`](staffml/ci-workflows.md)

## [`tinytorch/`](tinytorch/)

Hands-on course where students build an ML framework from scratch.

- [`design.md`](tinytorch/design.md)
- [`implementation.md`](tinytorch/implementation.md)
- [`system_design.md`](tinytorch/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the export pipeline or the milestone system.
- [`ci-workflows.md`](tinytorch/ci-workflows.md)

## [`mlsysim/`](mlsysim/)

First-principles analytical modeling framework for ML systems, also the physics engine behind the browser-based interactive labs.

- [`design.md`](mlsysim/design.md)
- [`implementation.md`](mlsysim/implementation.md)
- [`system_design.md`](mlsysim/system_design.md): dependencies, components, data flow, and error handling, for a contributor changing the physics core or a solver backend.
- [`ci-workflows.md`](mlsysim/ci-workflows.md)

## [`labs/`](labs/)

The 34 browser-based interactive labs (Volumes I and II), built on the mlsysim engine and exported to WASM via marimo.

- [`system_design.md`](labs/system_design.md): dependencies, components, the export and boot sequence, and progress persistence, for a contributor adding a lab or debugging the WASM path. Labs share their product framing with [`mlsysim/design.md`](mlsysim/design.md), there is no separate `design.md` for this folder.
- [`ci-workflows.md`](labs/ci-workflows.md)

## [`kits/`](kits/)

Hands-on embedded ML labs for real devices (Arduino, Seeed, Raspberry Pi).

- [`design.md`](kits/design.md)
- [`implementation.md`](kits/implementation.md)
- [`system_design.md`](kits/system_design.md): dependencies, content structure, and the build chain from a lab `.qmd` to a deployed site plus a downloadable PDF.
- [`ci-workflows.md`](kits/ci-workflows.md)

## [`mlperf-edu/`](mlperf-edu/)

A locally executable, quality-gated benchmark specification adapted from MLPerf's own discipline for classroom use.

- [`design.md`](mlperf-edu/design.md)
- [`implementation.md`](mlperf-edu/implementation.md)
- [`system_design.md`](mlperf-edu/system_design.md): the workload registry, the provenance-manifest anti-cheat system, and how the GraderPipeline class from PR #1933 was later deleted and replaced with in-process grading.
- [`ci-workflows.md`](mlperf-edu/ci-workflows.md)

## [`socratiq/`](socratiq/)

An embeddable, AI-powered learning widget for static HTML pages.

- [`design.md`](socratiq/design.md)
- [`implementation.md`](socratiq/implementation.md)
- [`system_design.md`](socratiq/system_design.md): the chat provider-fallback chain, the Shadow DOM and IndexedDB persistence, the XSS sanitization boundary, and the fully offline quiz fallback.
- [`ci-workflows.md`](socratiq/ci-workflows.md)

## [`design-grammar/`](design-grammar/)

A formal vocabulary and rewrite-rule catalog for deriving ML systems techniques from first principles.

- [`design.md`](design-grammar/design.md)
- [`implementation.md`](design-grammar/implementation.md)
- [`system_design.md`](design-grammar/system_design.md): `grammar.yml` as the single source of truth, and the verified sync mechanism that generates StaffML's `/framework` page from it.
- [`ci-workflows.md`](design-grammar/ci-workflows.md)

## [`slides/`](slides/)

35 Beamer decks (Volumes I and II) plus packaged TinyML courseware.

- [`design.md`](slides/design.md)
- [`implementation.md`](slides/implementation.md)
- [`system_design.md`](slides/system_design.md): the shared Beamer theme, the SVG-to-PDF-to-PPTX build chain, and why the compile engine is `pdflatex`, not `xelatex`.
- [`ci-workflows.md`](slides/ci-workflows.md)

## [`instructors/`](instructors/)

"The Blueprint": syllabi, pedagogy, assessment, and TA guidance for adopting instructors.

- [`design.md`](instructors/design.md)
- [`implementation.md`](instructors/implementation.md)
- [`system_design.md`](instructors/system_design.md): a short, honest non-finding, this project has no custom code at all, pure Quarto content.
- [`ci-workflows.md`](instructors/ci-workflows.md)

## [`site/`](site/)

The ecosystem's public front door: home, about, community, newsletter, and mini-games.

- [`design.md`](site/design.md)
- [`implementation.md`](site/implementation.md)
- [`system_design.md`](site/system_design.md): the newsletter sync CLI, the two-stage stats pipeline, and the mini-games.
- [`ci-workflows.md`](site/ci-workflows.md)

---

Start with each project's `design.md`, then use its `implementation.md` as your map once you're ready to make a change. All documents describe their project as of `dev` HEAD (commit `8fb87d81`) in the main repository.
