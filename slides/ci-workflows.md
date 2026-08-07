# Slides: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Slides workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

| Workflow | What it does |
|---|---|
| `slides-validate-dev.yml` | SVG well-formedness check, Quarto portal build, LaTeX frame begin/end matching check. |
| `slides-build-pdfs.yml` | Compiles all 35 Beamer decks via xelatex plus Inkscape, converts to PPTX for presenter mode. |
| `slides-preview-dev.yml` | Portal HTML only, no PDF compile, deployed to staging. |
| `slides-publish-live.yml` | Full PDF build plus a GitHub Release with ZIP archives, plus the portal deploy. |
