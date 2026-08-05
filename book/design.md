# Machine Learning Systems (The Book): Design

*This is the contributor-facing design document for the Machine Learning Systems textbook, the flagship sub-project of `harvard-edge/cs249r_book`, living at `book/` in that repo. It explains what the book project is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Known issues" at the end covers a real naming collision worth understanding early, and "Project history" covers the CI incident that shaped the current test setup.*

## Problem

"Machine Learning Systems" is a large, two-volume, actively-evolving textbook: over thirty chapters across two published volumes plus a third in planning, built, checked, and published through a custom pipeline rather than hand-maintained. At that scale, a handful of problems become unavoidable without dedicated tooling: citations and cross-references drift out of sync as chapters change, numeric prose (units, currency, percentages) needs to stay internally consistent across chapters written at different times, figures and captions need structural validation that a human reviewer will eventually miss on a large enough diff, and building the book in three output formats (HTML, PDF, EPUB) across two operating systems needs to be fast and reliable enough that contributors and CI both actually run it, not something so slow or brittle it gets skipped.

The `book/` project is the textbook's content plus the substantial tooling built specifically to keep a book this size internally consistent, buildable, and reviewable as it grows.

## Goals

- Two complete volumes of textbook content: Volume I, "Build, Optimize, Deploy" (single-machine systems, following a Foundations, Development, Optimization, Deployment structure), and Volume II, "Scale, Distribute, Govern" (distributed and production-scale systems, following a Foundations of Scale, Distributed Systems, Production Challenges, Responsible Deployment structure), with a third volume already scaffolded for future content.
- Three build outputs from one source: HTML (the published web book), PDF (a downloadable, print-quality book), and EPUB (an e-reader format), all rendered from the same underlying Quarto chapter content.
- A single, comprehensive CLI ("Binder") that is the one public interface for building, checking, fixing, formatting, and publishing the book, so a contributor or CI workflow never needs to invoke Quarto, pandoc, or any underlying tool directly.
- A large, automated content-quality validation system, bibliography hygiene, citation and cross-reference integrity, figure and caption structure, prose style (numeric formatting, punctuation, terminology consistency, MIT Press style conventions), and EPUB structural validity, enforced consistently in both a local pre-commit hook and CI, so the same checks a contributor sees locally are exactly what blocks a PR.
- Reproducible build environments via Docker, for both Linux and Windows, so "it builds on my machine" isn't a real risk for a book this dependency-heavy (a full TeX Live install, R, Quarto, and more).
- A staged deployment pipeline: automatic dev-preview builds on every push to `dev`, and a manual, confirmation-gated production publish that merges to `main`, tags a release, and deploys the live book.
- An editor-integration layer (a VS Code extension) that surfaces the Binder CLI's build, debug, validate, and publish commands as a discoverable UI, plus book-specific authoring affordances like chapter navigation and cross-reference tooling.
- Free and open content, matching the parent repository's license, published at `mlsysbook.ai` and also distributed via a commercial print arrangement.

## Non-goals

- Not a general-purpose Quarto book template; the tooling here (the Binder CLI, the extensive check suite, the specific numeric/citation conventions) is built specifically for this book's own scale and style requirements, not designed for reuse as a generic starting point.
- Not a place where sibling sub-projects' own instructor or grading documentation lives. TinyTorch's grading workflow, for instance, lives in TinyTorch's own documentation and is only linked to from elsewhere in the ecosystem, not duplicated here.
- Not the current, active home of the Socratiq AI widget's source code, despite a `book/socratiQ/` directory existing. See "Known issues."

## Technology stack

| Technology | What it is | How the book project uses it |
|---|---|---|
| Quarto | A publishing system built on Pandoc. | The core rendering engine for all three output formats (HTML, PDF, EPUB) from one shared set of chapter `.qmd` files, with separate per-volume, per-format Quarto configuration files controlling each build variant. |
| Python (a hand-rolled CLI, no third-party CLI framework) | A general-purpose programming language. | Implements the entire Binder CLI: the command dispatcher, every subcommand, and the large validation-check suite. |
| LaTeX (a full TeX Live distribution) | A typesetting system. | Required for the PDF build output, and for the shared cover/title-page Quarto extension also used by the kits project. |
| R | A statistical programming language. | Used for a subset of the book's data visualizations and figures, installed and verified as part of the Docker build environment alongside Python and LaTeX. |
| Docker | A containerization tool. | Provides fully reproducible Linux and Windows build environments, published to GHCR, cutting local Linux build setup time from roughly 45 minutes to 5 to 10 minutes and giving CI a consistent, versioned environment rather than an ad hoc one assembled fresh on every run. |
| `pre-commit` | A framework for managing and running Git pre-commit hooks. | Runs the entire content-validation check suite (bibliography, citations, figures, prose style, and more) locally before a commit, and the exact same suite again in CI, so local and CI validation never disagree. |
| Vale, `mdformat`, `bibtex-tidy`, `epubcheck`, Lychee | A prose-linting tool, a Markdown formatter, a BibTeX-file formatter, an EPUB validator, and a link-checking tool. | Each is wired into the pre-commit/CI check suite for its specific domain: prose style, Markdown formatting, bibliography formatting, EPUB structural validity, and both internal and external link integrity respectively. |
| Playwright | A browser-automation framework for real, headless browsers. | Used by one of the book's own heavier, manual-stage checks to render the built HTML and scan for math-rendering leaks (raw, unrendered LaTeX visible on the page) that a purely structural check wouldn't catch. |
| TypeScript and the VS Code Extension API | A typed superset of JavaScript, and the API for building Visual Studio Code extensions. | Powers "MLSysBook Workbench," the book's own VS Code extension, a GUI layer over the Binder CLI plus book-specific authoring affordances (chapter navigation, cross-reference and label tooling, syntax highlighting for the book's custom callout and citation conventions). |

## Architecture

### Content structure: two (soon three) volumes, four parts each

Chapter content lives under `book/quarto/contents/{vol1,vol2,vol3}/`, one subdirectory per chapter, each containing its `.qmd` source, its own images, a concepts manifest, and a quiz-content file. Volume I is organized into four parts (Foundations, Development, Optimization, Deployment); Volume II mirrors that structure with its own four parts (Foundations of Scale, Distributed Systems, Production Challenges, Responsible Deployment). Volume III currently exists only as a scaffold, an index page and an outline, with no chapter content yet, a clear signal of where the project is headed rather than where it currently is.

### The Binder CLI: the one public interface

Every build, validation, formatting, and maintenance operation goes through a single entry point, the `book/binder` script, which is a thin shim that adds `book/cli` to the Python path and calls into the real command dispatcher. Binder is explicitly documented as "the public automation API for book build, validate, and maintenance workflows," meant to be used the same way whether you're a contributor at a terminal, an editor extension, or a CI workflow. It exposes commands spanning the whole lifecycle: `build` (with sub-targets for html, pdf, and epub, scoped to a full volume, a specific chapter, or the whole book), `preview` (a live-reload dev server), `doctor` (environment health checks), `check` (the validation suite, aliased as `validate`), `fix` (automated content repair, aliased as `maintain`), `format` (auto-formatters), `bib` (bibliography tooling), `info` (extracting statistics like word counts, figure lists, and acronyms), `layout` (a PDF whitespace and table-layout auditor), `debug` (bisecting a failing build down to the specific chapter and section responsible), `release` (a structured release-readiness report), and several more.

### The validation and quality system

This is the largest and most distinctive part of the project. Dozens of individual checks, organized into named groups (`refs`, `footnotes`, `figures`, `images`, `tables`, `listings`, `prose`, `punctuation`, `numbers`, `math`, `notation`, `index`, `sources`, `units`, `epub`, and more), each independently invocable via `binder check &lt;group&gt;`, cover everything from whether every citation resolves to a real bibliography entry, whether every figure has a properly formatted caption and alt text, whether numeric prose follows the book's own formatting conventions (currency, percentages, binary units), to whether the book's canonical terminology spelling (per its publisher's style guide) is used consistently. A smaller number of heavier checks, a full-book math-rendering audit that actually builds HTML and scans it with a real browser for rendering leaks, for example, are deliberately excluded from the default, every-commit check set and run only manually or on a longer cadence, since they're too slow to run on every single change.

This validation suite runs in exactly the same way in two places: as local pre-commit hooks (so a contributor sees failures before they even open a PR) and as a required CI job on every push to `dev` (so nothing that skips the local hook, deliberately or accidentally, slips through). The project's own configuration is explicit that this is a single source of truth, every `book-check-*` pre-commit hook dispatches directly through the same `./book/binder` commands CI itself runs, not a separately maintained parallel implementation.

### Bibliography as a first-class, semi-automated system

Citation and bibliography hygiene gets its own dedicated subsystem within Binder (`binder bib`), including a mechanical, automatic-fix pass that runs as an auto-formatting pre-commit hook (correcting common, unambiguous BibTeX formatting issues before a human ever needs to look at them) and a separate, stricter semantic validation pass that only checks, never silently rewrites, since some bibliography issues (a genuinely wrong citation, a missing source) require a human judgment call rather than an automated fix.

### Reproducible build environments

Two Docker images, one Linux-based and one Windows-based, encode the book's full build dependency chain (Quarto, a complete TeX Live LaTeX distribution, R with a specific package set, Ghostscript, Inkscape, and Python with the book's own tooling dependencies) as a versioned, published artifact rather than a set of setup instructions a contributor has to follow by hand and hope stays accurate. The Linux image in particular is published to a container registry and reused directly by CI, so the exact same environment that validates a PR is available to a contributor building locally, eliminating an entire class of "works in CI, fails locally" (or the reverse) discrepancy.

### Docker or Quarto: not silently produced

CI's book-build workflow itself is a reusable workflow, invoked by both the validation and the production-publish pipelines, so there's exactly one place that knows how to correctly matrix-build every combination of volume, output format, and operating system this project supports; neither the validate-on-every-push path nor the manual production-publish path maintains its own separate copy of that build logic.

### Deployment: dev preview, then a gated production publish

Every successful validation run on `dev` automatically triggers a dev-preview deployment to a separate, non-production site, so a contributor or reviewer can see a real, rendered preview of a change without needing a local build. Publishing to the actual production site is a deliberately separate, manual, confirmation-gated workflow (requiring an operator to type a literal confirmation string), which additionally bumps the book's version, merges the validated `dev` commit into `main`, rebuilds against that merged state, deploys the live HTML site, creates a version tag, and generates release notes, with AI-assisted drafting of the human-readable summary as one step in that longer chain, not the whole process.

### The VS Code extension

"MLSysBook Workbench" treats the Binder CLI as its entire operational backend; every build, debug, validate, and maintenance action it exposes in its sidebar shells out to a corresponding `binder` subcommand rather than reimplementing any of that logic in the extension itself. On top of that CLI wrapper, it adds genuinely book-specific editor affordances that don't correspond to any CLI command: syntax highlighting for the book's custom callout, label, footnote, and inline-Python-variable conventions inside `.qmd` files, a chapter navigator outline view, and tooling for renaming a cross-reference label consistently across every file that references it.

## Known issues

- **"Binder" is an overloaded, colliding name within this same monorepo, worth understanding before you get confused by it.** Inside `book/`, "Binder" refers unambiguously to this project's own CLI (`book/binder`), the single public interface for build, check, fix, and format operations described throughout this document. But a completely unrelated directory also named `binder/` exists at the repository root, holding standard mybinder.org (the third-party Jupyter-notebook-launching service) configuration, which is actually a redirect wrapper for the TinyTorch project's own Binder (mybinder.org) launch configuration, not this book project at all. The two concepts share a name purely by coincidence, and the repository root's top-level README doesn't mention the book's Binder CLI at all, only `book/README.md` and `book/docs/BINDER.md` do, reinforcing that the CLI concept is intentionally scoped to this sub-project. If you're searching the wider repository for "binder" and expecting to find this project's CLI, be aware you may land in the unrelated TinyTorch-adjacent directory instead.
- **`book/socratiQ/` is not the active source of the Socratiq widget.** It contains only a placeholder README describing the feature to a reader of the book, explicitly stating that the widget's real source code will be placed here (and eventually open-sourced into its own dedicated repository) "in the future." The actual, active Socratiq widget source lives in the separate `socratiq/` project at the repository root; if you're looking to modify the widget's behavior, `book/socratiQ/` is not where that work happens.

## Project history

- **A silent, difficult-to-diagnose PDF layout bug led directly to the project's current, extensive PDF and EPUB output verification.** The scale of checks this project runs specifically against build *output* (not just source content), a PDF layout and whitespace planner, chapter-level PDF and HTML verification steps, and a real-browser-based math-rendering audit, reflects a lesson learned from output-level problems that source-level content checks alone couldn't have caught: a chapter can pass every prose, citation, and figure check and still render incorrectly once actually built, so this project invests specifically in checking the built artifact, not only the source that produces it.
- **The extensive, dual-purpose (local pre-commit and CI) validation suite exists specifically so local and CI validation results can never disagree.** Every `book-check-*` pre-commit hook is documented as dispatching directly through the same Binder CLI commands CI itself invokes, a deliberate architectural choice to eliminate an entire class of "passed locally, failed in CI" (or the reverse) friction that a separately-maintained CI-only or pre-commit-only check implementation would otherwise create.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the full file map, the real Binder command reference, the check-suite structure, the Docker build environments, local setup steps, and common contribution workflows for editing a chapter, adding a check, or working on the CLI itself. The "Known issues" list above, especially the Binder naming collision, is worth rereading once more before your first PR, since it's the kind of thing that causes real confusion if you encounter it cold.
