# Kits: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Kits workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

Standard triad plus one reusable PDF builder:

| Workflow | What it does |
|---|---|
| `kits-validate-dev.yml` | Checks every image reference exists on disk, builds the Quarto site, and calls `kits-build-pdfs.yml` to confirm the PDF actually compiles. |
| `kits-build-pdfs.yml` | Reusable: Quarto render to a titlepage PDF in a container, Ghostscript compression, uploaded as an artifact. Never runs standalone in the deploy path. |
| `kits-preview-dev.yml` | Builds the PDF, builds the site with the PDF injected, deploys to staging via SSH. |
| `kits-publish-live.yml` | Same build, deployed to `mlsysbook.ai/kits/`. |
