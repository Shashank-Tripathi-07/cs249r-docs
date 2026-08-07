# Instructors: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Instructors workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

Standard triad, nothing unusual in its shape:

| Workflow | What it does |
|---|---|
| `instructors-validate-dev.yml` | Checks every image reference exists on disk, Quarto HTML build, non-blocking link check. |
| `instructors-preview-dev.yml` | Quarto render, rewrite URLs for the dev base path, deploy via SSH. |
| `instructors-publish-live.yml` | Quarto render, page-count sanity check, deploy to `gh-pages`. |
