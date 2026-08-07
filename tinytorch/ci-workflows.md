# TinyTorch: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only TinyTorch's own workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

| Workflow | What it does |
|---|---|
| `tinytorch-validate-dev.yml` | A 7-stage progressive suite: inline build from `src/`, unit tests, integration tests, CLI tests (three of these run in parallel), then end-to-end tests gated on all three, then a Docker-based fresh-install simulation, then a destructive full user-journey test reserved for explicit `all`/`user-journey` runs. Matrixed across Ubuntu and Windows (Windows runs everything through Git Bash for cross-platform shell compatibility). |
| `tinytorch-build-pdfs.yml` | Reusable: builds the Lab Guide (Quarto to XeLaTeX, reusing the same `.qmd` chapter sources the website uses) and the Research Paper (LuaLaTeX) independently. |
| `tinytorch-preview-dev.yml` | Gated on Validate succeeding via `workflow_run`, deploys the guide, paper, and slide downloads to staging. |
| `tinytorch-publish-live.yml` | Full semantic-versioning release: computes the next version from `tinytorch-v*` tags, runs the full validate suite as a preflight, bumps the version across six files, merges dev into main, builds PDFs, deploys, tags, drafts a GitHub Release. Supports a `site_only` mode that skips version bump, tag, and PDFs entirely for a pure content refresh. |
| `tinytorch-update-pdfs.yml` | Rebuilds and redeploys just the PDFs, no site rebuild, no version bump, for when only PDF content changed. |

See [`system_design.md`](system_design.md) for how `tito module complete` and the export pipeline relate to what CI actually checks, `tinytorch-validate-dev.yml`'s stages mirror the same test tiers a contributor runs locally via `tito dev test`.

## A real, verified bug in this pipeline

**2026-05-17 (`df88ff7a72`), shared with Labs: concurrency grouped only on `github.ref`.** A manual `workflow_dispatch`, or a `workflow_call` from `tinytorch-publish-live.yml`'s preflight step, landing on the same SHA as a push would cancel the push run via `cancel-in-progress`, showing red on a commit that was actually healthy. Fixed by switching to the `head_ref || run_id` pattern already used by kits, mlsysim, site, slides, and instructors. See the [top-level doc's Pattern A](../ci-workflows.md#pattern-a-concurrency-group-too-coarse-dispatch-collides-with-push) for the two other projects this exact bug independently hit, at different times.
