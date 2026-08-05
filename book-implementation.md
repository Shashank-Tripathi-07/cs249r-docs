# Machine Learning Systems (The Book): Implementation Reference

> **Status: as-built, contributor-facing.** The book is a live, already-published, two-volume textbook with an extensive custom tooling layer. This document is your map for reading and modifying the real source: file paths and real commands pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](book-design.md) first for the "what and why," especially its "Known issues" section on the Binder naming collision; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| Chapter content only (`.qmd` editing, HTML preview) | Quarto, and `./binder setup` from `book/` to install the rest of the toolchain. The Docker image (below) is the fastest path to a fully working environment. |
| A full local build (HTML, PDF, EPUB) | Everything `book/docker/linux/Dockerfile` installs: Quarto, a full TeX Live distribution, R with its required packages, Ghostscript, Inkscape, and Python. Using the published Docker image directly is faster than assembling this by hand. |
| The Binder CLI itself (`book/cli/`) | Python 3, installed via the repository root's `pyproject.toml` (`book/setup.py` just runs `pip install -e .` against it). |
| The VS Code extension | Node.js, npm, and the VS Code extension development tools. |
| The math-rendering audit or other Playwright-based checks | Playwright with a Chromium install, in addition to the above. |

## Repository layout

```
cs249r_book/
  book/
    binder                       # CLI entry point: shim into cli/main.py
    cli/
      main.py                     # MLSysBookCLI, command dispatch table
      commands/
        build.py, preview.py, doctor.py, clean.py, audit.py, validate.py,
        maintenance.py, formatting.py, info.py, bib.py, render.py,
        newsletter.py, headings.py, layout.py, reset.py, release.py, debug.py
      checks/                      # ~18 individual check modules
        bib_lint.py, cli_contract.py, currency_style.py, math_canonical.py,
        mitpress_terms.py, percent_prose.py, binder_canonical.py, ...
      core/
        config.py, discovery.py, artifacts.py, bib_mechanical.py
    quarto/
      contents/
        vol1/{introduction, ml_systems, ..., conclusion}/
          parts/{foundations_principles,build_principles,optimize_principles,deploy_principles}.qmd
        vol2/{introduction, compute_infrastructure, ..., conclusion}/
          parts/{distributed_ml_principles,fleet_principles,responsible_fleet_principles,deployment_principles}.qmd
        vol3/{index.qmd, OUTLINE.md}     # Scaffolded, no chapters yet
        frontmatter/, backmatter/
          frontmatter/socratiq/socratiq.qmd   # Reader-facing Socratiq documentation page
      config/
        _quarto-html-vol1.yml, _quarto-pdf-vol1.yml, _quarto-epub-vol1.yml
        _quarto-html-vol2.yml, _quarto-pdf-vol2.yml, _quarto-epub-vol2.yml
      _extensions/, assets/, filters/, scripts/, tex/, calc/, audits/, publish/
    config/
      quarto/_publish.yml           # Quarto Pub deploy target metadata
      linting/                       # .vale.ini, .markdownlintignore, .lycheeignore, ...
      dev/.luarc.json                 # Lua language-server config for quarto/filters/
    tools/
      audit/                          # Durable audit records + checkers (checks/, fmt/, index/, release/)
      scripts/
        content/, images/, margin_figures/, publish/, quizzes/, maintenance/,
        infrastructure/, testing/, mit_press/, socratiQ/, utilities/, docs/
      dependencies/
        requirements.txt, install_packages.R, required_r_packages.R, tl_packages
      figures/                        # style.py, margin/, fonts/  (book plotting style)
      git-hooks/                       # pre-commit, setup.sh
      setup/                            # setup.sh, clean.sh, setup_lua_env.sh
      tests/                             # a second, small test dir co-located with tooling
    tests/                            # book/tests/: pytest suite for content/build invariants
      sim/
    docker/
      linux/Dockerfile, verify_r_packages.R, README.md
      windows/Dockerfile, README.md
    docs/
      BINDER.md, BUILD.md, CONTRIBUTING.md, DEVELOPMENT.md,
      VOLUME_STRUCTURE.md, LEGO_CELLS.md, PART_KEY_VALIDATION.md,
      CONTAINER_BUILDS.md, MOBILE_QA.md, CODE_OF_CONDUCT.md, releases/
    socratiQ/README.md              # Placeholder only; NOT the real widget source
    vscode-ext/
      package.json                   # "MLSysBook Workbench"
      src/extension.ts
      src/commands/{buildCommands,debugCommands,precommitCommands,publishCommands,contextMenuCommands}.ts
      src/providers/, src/validation/, src/utils/
  .github/workflows/
    book-validate-dev.yml
    book-build-container.yml         # Reusable: matrix build across {vol1,vol2} x {html,pdf,epub} x {linux,windows}
    book-preview-dev.yml
    book-publish-live.yml
  .pre-commit-config.yaml            # "Single source of truth: every book-* check dispatches through ./book/binder"
```

---

## 1. The Binder CLI: entry point and command dispatch

`book/binder` is a 17-line shim:

```python
#!/usr/bin/env python3
"""MLSysBook CLI v2.0 - Modular Entry Point"""
cli_dir = Path(__file__).parent / "cli"
sys.path.insert(0, str(cli_dir))
from main import main
if __name__ == "__main__":
    main()
```

`book/cli/main.py`'s `MLSysBookCLI` class holds a single dictionary mapping command names to command classes, the dispatch table every subcommand comes from. Deprecated single-letter aliases (`b`, `p`, `l`, `s`, `d`, `h`) are hard-removed and just print a redirect message rather than silently working; old top-level `html`/`pdf`/`epub` commands redirect to `build &lt;format&gt;`.

### 1.1 Command reference

| Command | Backing module | What it does |
|---|---|---|
| `build [html\|pdf\|epub] [chapter(s)] [--vol1\|--vol2\|--all] [--skip-hygiene] [--skip-validate] [--layout]` | `commands/build.py` | The unified build dispatcher. Routes to a full-book, per-volume, or per-chapter build; `--skip-hygiene` bypasses pre-render EPUB hygiene checks, `--skip-validate` bypasses post-render validation, `--layout` chains into the PDF layout planner after a full-volume PDF build. |
| `preview [chapter]` | `commands/preview.py` | Live-reload Quarto dev server, whole book or one chapter. |
| `doctor` | `commands/doctor.py` | Comprehensive local tooling and repo health check. |
| `clean [html\|pdf\|epub\|artifacts]` | `commands/clean.py` | Removes generated build artifacts. |
| `switch &lt;html\|pdf\|epub&gt;` | (maintenance) | Switches the active Quarto build config. |
| `list [--vol1\|--vol2]` | `core/discovery.py` | Lists discovered chapters. |
| `status` | main.py | Prints root/book directories, symlink config status, chapter count. |
| `audit &lt;chapter-pdf\|html&gt; ...` | `commands/audit.py` | Per-chapter build audit ledger. |
| `check &lt;group&gt; [--scope ...]` (alias `validate`) | `commands/validate.py` | Runs the validation suite; dispatches to individual checks in `cli/checks/`. |
| `fix &lt;topic&gt; &lt;action&gt;` (alias `maintain`) | `commands/maintenance.py` | Content maintenance and repair (for example, `fix headers add`, `fix repo-health`). |
| `format &lt;target&gt;` | `commands/formatting.py` | Auto-formatters (for example, tables). |
| `info &lt;stats\|figures\|concepts\|headers\|acronyms&gt;` | `commands/info.py` | Extracts book statistics and metadata. |
| `bib &lt;mechanical\|normalize\|sync&gt;` | `commands/bib.py` | Bibliography management. |
| `render plots [--vol1\|chapter]` | `commands/render.py` | Renders matplotlib plots to a PNG gallery. |
| `newsletter &lt;new\|list\|preview\|publish\|fetch\|status&gt;` | `commands/newsletter.py` | Manages newsletter drafts, publishing to Buttondown. |
| `headings &lt;check\|dry-run\|apply&gt;` | `commands/headings.py` | Heading structure checks and fixes. |
| `layout [--vol1\|--vol2] / layout check &lt;pdf&gt; / layout tables` | `commands/layout.py` (the largest command module) | Builds or reuses a PDF and emits an auto-layout plan flagging pages with excess whitespace; renders table-only audit sheets. |
| `reset &lt;fmt\|all&gt; [--vol1\|--vol2]` | `commands/reset.py` | Resets build YAML configs. |
| `release [--vol1\|--vol2] [--dry-run] [--json]` | `commands/release.py` | Runs the release gate, emits a structured report. |
| `debug &lt;pdf\|html\|epub&gt; --vol1\|--vol2 [--chapter &lt;name&gt;]` | `commands/debug.py` | Bisects a failing build down to the specific chapter and section responsible. |
| `setup` | main.py | Installs local dev dependencies and pre-commit hooks. |
| `hello` / `about` | main.py | Informational commands. |
| `help` | main.py | Full Rich-formatted command reference. |

### 1.2 What `debug` actually does

`debug &lt;format&gt; --vol1|--vol2 [--chapter &lt;name&gt;]` is worth understanding early: rather than a single opaque full-book build failing with a stack trace that could originate anywhere, it isolates the failure to a specific chapter (and, further, a specific section within that chapter), by building incrementally and narrowing down where the build actually breaks. If you hit a build failure whose cause isn't obvious from the error message alone, reach for this before manually bisecting by commenting out chapters.

---

## 2. `book/cli/checks/` and `book/tools/audit/`: the validation suite

Two related but distinct layers exist:

- **`book/cli/checks/`**: individual checker modules invoked by `binder check &lt;group&gt;`, for example `bib_lint.py`, `math_canonical.py`, `currency_style.py`, `mitpress_terms.py`, `percent_prose.py`, and `cli_contract.py` (which guards the CLI's own command surface against accidental breakage), plus `binder_canonical.py`, which specifically enforces that `./book/binder` remains the one canonical entry point for book automation.
- **`book/tools/audit/`**: a larger, separate area of durable audit records and helper scripts, organized into `checks/` (roughly twenty individual prose and style checkers, for example `abbreviation_first_use.py`, `bibliography_hygiene.py`, `duplicate_citation.py`, `footnote_numbering.py`, `source_citations.py`, `unresolved_xref.py`, `table_caption.py`, `math_notation_render.py`), `fmt/` (the numeric-formatting audit and codemod subsystem, including `codemod_fmt.py`, an automated numeric-formatting fixer), `index/` (index-tag placement and cross-reference resolution), and `release/` (release-readiness scanners, for example alt-text coverage and front-matter coverage). This directory also holds baseline/allowlist JSON files, ratchets that record currently-accepted exceptions (for example `epubcheck-baseline.json`) so a legacy issue can be grandfathered in without blocking new work, while still preventing new instances of the same issue.

### 2.1 Pre-commit hooks, one per check group

`.pre-commit-config.yaml`'s own header states the operating principle directly: every `book-check-*` hook dispatches through `./book/binder`, so local and CI validation are never two separately maintained implementations. Representative hooks: `book-check-refs` (cross-references, citations, inline refs), `book-check-footnotes` (definition shape, placement, capitalization), `book-check-figures` (captions, div syntax, alt text, with a grandfathered-exception baseline for existing figures), `book-check-images`, `book-check-tables`, `book-check-listings`, `book-check-prose` (contractions, duplicate words, "above/below" references), `book-check-punctuation` (em dash, slash, "vs.", "e.g.", en dash), `book-check-numbers` (units, percent, binary units, currency style), `book-check-math`, `book-check-notation` (Iron Law symbol consistency), `book-check-index`, `book-check-sources`, `book-check-units`, `book-check-epub`, `book-check-labels-orphans` and `book-check-labels-duplicates` (cross-file label graph checks), and `book-check-cli-contract` (always runs, guards the CLI itself). A small number of heavier hooks are marked manual-only (not run on every commit): a full HTML-build-plus-Playwright math-rendering leak scan (roughly ten minutes), a rendered-currency check, and an artifact-cleanup hook.

Bibliography also has its own auto-fixing formatter stage, distinct from the stricter validator: `bib-apply-mechanical` (`./book/binder bib mechanical --pre-commit`) runs before `book-check-bib`, correcting unambiguous formatting issues automatically so the semantic validator only needs to flag things that actually require a human judgment call.

### 2.2 Running the whole suite

Locally: `pre-commit run --all-files` (the same command the `pre-commit` job in `book-validate-dev.yml` runs in CI). Activate it as your actual git hook once per clone via `git config core.hooksPath book/tools/git-hooks` (the hook script itself just execs `pre-commit run --all-files`).

---

## 3. `book/tests/`: pytest suite

Registered in the repository root `pyproject.toml` (`testpaths = ["tests", "book/tests"]`), with `--cov=book/tools --cov-fail-under=80`. Notable groupings: structural and registry validation (`test_appendix_constants.py`, `test_registry.py`, `test_mlsysim_registry_coverage.py`, `test_no_legacy_constant_refs.py`), cross-reference and label integrity (`test_algorithm_xref_case.py`, `test_footnote_cross_chapter.py`, `test_validate_inline_refs.py`), the numeric-formatting contract (`test_units.py`, `test_lego_prose_units.py`, `test_fmt_prose_contract.py`, `test_currency_style.py`, `test_codemod_fmt.py`, `test_math_canonical.py`), build-output checks (`test_pdf_layout_checks.py`, `test_epub_postprocess.py`), and the CLI contract itself (`test_binder_cli_contract.py`, `test_binder_lego_scope_paths.py`). Note that link checking and figure/caption checking are not implemented as pytest files here; they're implemented as `binder check` scopes and pre-commit hooks instead (section 2 above), so don't go looking for a `test_links.py`.

`book/tools/tests/` is a second, smaller, co-located test directory specifically for the numeric-formatting ("LEGO" prose-units) audit tooling, distinct from `book/tests/`.

---

## 4. Docker build environments

### 4.1 Linux (`book/docker/linux/Dockerfile`)

Built on Ubuntu 22.04, in stages: locale configuration, system dependencies (Cairo, RSVG, XML, image libraries), Inkscape (via PPA), fonts, Ghostscript, a full TeX Live install (pinned installer revision), R 4.5 with packages installed via `book/tools/dependencies/install_packages.R` and verified via `book/docker/linux/verify_r_packages.R`, Python 3 plus pip, Quarto 1.9.27 (installed from a `.deb` fetched from GitHub Releases), Python packages from `book/tools/dependencies/requirements.txt`, then cleanup and a final verification step confirming Quarto, Python, R, and `lualatex` all work. Published to GHCR as `ghcr.io/harvard-edge/cs249r_book/quarto-build`; using it locally cuts setup time from roughly 45 minutes to 5 to 10.

### 4.2 Windows (`book/docker/windows/Dockerfile`)

Windows Server 2022 LTSC, PowerShell 7, Quarto via Scoop, Python 3.13.1, Ghostscript and Inkscape via Chocolatey and Scoop, a TeX Live 2025 snapshot, R 4.5.2. Built via a dedicated, manually or on-schedule triggered infrastructure workflow, not on every push (build time is roughly 45 to 60 minutes, image size 8 to 12 GB). Local build: `docker build -f book/docker/windows/Dockerfile -t mlsysbook-windows .` from the repository root.

---

## 5. CI/CD implementation notes

### 5.1 `book-validate-dev.yml`

Triggers on push to `dev` touching `book/**`, or manually with format/OS/health-check/registry inputs. Job sequence: `pre-commit` (installs and runs `pre-commit run --all-files`, the entire check suite from section 2), an optional `container-health-check`, `update-contributors`, `build-config` (computes which format/OS combinations to build), `build-container` (calls the reusable `book-build-container.yml`), `collect-results`, `epub-validate` (runs `./binder check epub --scope epubcheck` against the `epubcheck-baseline.json` ratchet), `check-links` (Lychee, non-blocking), and `validation-summary`. No deploy happens directly here; success triggers `book-preview-dev.yml` via a `workflow_run` trigger.

### 5.2 `book-build-container.yml`: the reusable matrix build

`workflow_call` (invoked by both the validate and publish workflows) and `workflow_dispatch`. A single `build` job matrixes across up to 12 combinations, {vol1, vol2} times {html, pdf, epub} times {linux, windows}, run inside the Docker environments from section 4, followed by a `collect-outputs` job aggregating results for whichever workflow called it. Because this is the one shared build implementation, a bug fixed here is fixed for both the validate-on-every-push path and the manual production-publish path simultaneously.

### 5.3 `book-preview-dev.yml`

Triggers on completion of the validate workflow, or manually. Downloads the volume 1 and volume 2 HTML/PDF/EPUB artifacts from the triggering validate run, assembles a preview site, flattens the volume URL structure, rewrites URLs for the dev-preview base path, injects a dev-preview announcement banner, and deploys via SSH to a separate dev-preview repository. Deploy target: a GitHub Pages dev-preview site, not `mlsysbook.ai`.

### 5.4 `book-publish-live.yml`: production

Manual only, requiring a typed confirmation, a substantially larger workflow than any other sub-project's publish pipeline in this monorepo. In order: a guard job confirming `book-validate-dev.yml` is green, input validation, pre-flight checks, a version bump, merging the validated `dev` commit into `main`, triggering a fresh production build (calling `book-build-container.yml` again, this time against `main`), tagging the release, downloading and deploying the built artifacts to the `gh-pages` branch, generating release notes (with an AI-assisted drafting step as one part of that process, not the whole thing), creating the GitHub Release, and dedicated cleanup jobs for both failure and timeout cases.

---

## 6. `book/vscode-ext/`: "MLSysBook Workbench"

`package.json` name `mlsysbook-workbench`, description "Build, debug, validate, and publish the ML Systems textbook," activates on `workspaceContains:book/binder`. Its own README states plainly: "This extension treats `./book/binder` as the operational backend. Build and debug commands execute Binder subcommands. Validation actions execute `binder check ...`. Maintenance actions execute `binder fix ...`." Concretely, its command implementations shell out directly, for example `./book/binder fix headers add ...`, `./book/binder check headers --vol1`, `./book/binder check labels --scope orphans --vol1`.

Beyond wrapping the CLI, it adds book-specific editor affordances with no CLI equivalent: syntax highlighting for the book's callout, label, footnote, and inline-Python-variable conventions inside `.qmd` files, a chapter navigator outline view, and cross-reference and label-rename tooling. Source: `src/extension.ts` plus `src/commands/{buildCommands,debugCommands,precommitCommands,publishCommands,contextMenuCommands}.ts`, `src/providers/`, `src/validation/`, `src/utils/`.

---

## 7. "Binder" versus `binder/`: avoiding the naming collision in practice

`book/binder` (a file, the CLI shim described in section 1) and the repository-root `binder/` (a directory holding mybinder.org/Jupyter launch configuration, actually a redirect wrapper for TinyTorch's own separate Binder configuration at `tinytorch/binder/`) are unrelated. If you're searching this monorepo for "binder" and land in the repository-root directory instead of `book/binder`, you're in the wrong place for anything described in this document. The repository's top-level README doesn't mention the book's Binder CLI at all; only `book/README.md` and `book/docs/BINDER.md` do. When writing documentation, commit messages, or code comments that reference "Binder," always disambiguate with the full path (`book/binder` or "the Binder CLI") rather than the bare word, given this collision is real and already a documented source of confusion.

---

## 8. Local development workflow

1. `cd book`.
2. `./binder setup`, first time only, installs local dev dependencies and pre-commit hooks. (Using the Linux Docker image instead is faster if you need a fully reproducible environment, particularly for PDF or EPUB work.)
3. `./binder doctor` to confirm your environment is actually healthy before doing anything else.
4. Edit chapter content under `quarto/contents/vol{1,2}/&lt;chapter&gt;/`.
5. `./binder preview &lt;chapter&gt;` for live-reload HTML preview while editing.
6. Before committing: `pre-commit run --all-files` (or let your configured git hook run it automatically), which runs the full check suite from section 2.
7. For a full local build: `./binder build html` (or `pdf`, or `epub`), scoped with `--vol1`, `--vol2`, `--all`, or a specific chapter name.
8. If a build fails and the error isn't obviously chapter-specific: `./binder debug &lt;format&gt; --vol1|--vol2` to bisect down to the responsible chapter and section rather than guessing.

---

## 9. Common contribution workflows

### Editing an existing chapter

1. Edit the chapter's `.qmd` file under `quarto/contents/vol{1,2}/&lt;chapter&gt;/` directly.
2. `./binder preview &lt;chapter&gt;` for live feedback.
3. Before committing, let pre-commit run (or run it manually), and fix anything it flags. If a check flags something you believe is a false positive on legitimate, pre-existing content rather than your change, check whether it's already covered by one of the baseline/ratchet files in `book/tools/audit/baselines/` before assuming you need to work around it.
4. If your edit affects the chapter's figures, run `./binder check figures` specifically, and `./binder info figures` if you want to see the current figure inventory for context.

### Fixing a validation check false positive or adding a new check

1. Individual checks live in either `book/cli/checks/` (lighter, CLI-native checks) or `book/tools/audit/checks/` (the larger prose/style audit area); figure out which one owns the behavior you're touching before assuming where to make the change.
2. If you're adding a new check group, it needs both the check implementation and a corresponding entry in `.pre-commit-config.yaml` (following the existing `book-check-&lt;group&gt;` naming and dispatch-through-Binder pattern) so it actually runs locally and in CI identically, per the project's own stated single-source-of-truth principle.
3. Add a test under `book/tests/` covering the new check's logic directly, separate from relying on the check catching something in real chapter content.

### Working on the Binder CLI itself

1. Command implementations live in `book/cli/commands/`, one file per command (or command group); support code (`ConfigManager`, `ChapterDiscovery`) lives in `book/cli/core/`.
2. `book-check-cli-contract` (always runs, per section 2.1) guards the CLI's own command and help surface; if you're intentionally changing a command's interface, expect this check to need a matching update, and treat that as a signal you've actually changed the public contract, not just an obstacle to route around.
3. Test your change against both direct invocation (`./binder &lt;command&gt;`) and, if it's something the VS Code extension surfaces, confirm the extension's corresponding command still shells out correctly.

### Working on the VS Code extension

1. Every command should shell out to a real `binder` subcommand; don't reimplement build, check, or fix logic inside the extension itself.
2. For QMD-authoring affordances with no CLI equivalent (syntax highlighting, cross-reference tooling), those live genuinely in the extension's own source, under `src/providers/` and `src/validation/`.

### Adding content to Volume III

Volume III is currently scaffolded (an index page and an outline document) but has no chapter content. If you're starting real chapter work here, follow the same directory shape established by Volumes I and II (`quarto/contents/vol3/&lt;chapter&gt;/`, a `.qmd` plus images plus a concepts manifest), and check `quarto/contents/vol3/OUTLINE.md` first for the currently planned chapter structure before assuming your own organization.
