# design-grammar: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers design-grammar's CI situation; read the top-level doc first for the general validate/preview/publish pattern most projects follow, which this one does not.*

design-grammar has no workflow file of its own. Confirmed by grepping the full workflow directory rather than inferring from absence alone: it is referenced only inside `book-validate-dev.yml` and `auto-label.yml`. Its content is validated as part of the book's own pipeline rather than standalone, there is no independent build or deploy step to point to. See [`../book/ci-workflows.md`](../book/ci-workflows.md) for the pipeline that actually covers it.
