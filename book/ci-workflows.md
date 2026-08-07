# Book: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the book's own workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

| Workflow | Trigger | What it does |
|---|---|---|
| `book-validate-dev.yml` | push to dev, PR, dispatch | Pre-commit hooks, then a full container-based build matrix (see below). |
| `book-build-container.yml` | `workflow_call`, dispatch | Reusable matrix build: up to 12 parallel jobs (2 volumes x 3 formats x 2 OS) using pre-built Docker containers so contributors and CI never pay the 30 to 45 minute native dependency-install cost. |
| `book-preview-dev.yml` | `workflow_run` after Validate succeeds, dispatch | Downloads the validated artifacts, assembles the staging site, deploys via SSH. |
| `book-publish-live.yml` | dispatch only, requires "PUBLISH" | Merges dev into main, rebuilds via the container matrix, deploys to `gh-pages`, tags a release, generates AI-assisted release notes, reverts the merge automatically on failure or timeout. |

design-grammar is referenced inside `book-validate-dev.yml` rather than having any workflow file of its own, see the [design-grammar CI notes](../design-grammar/ci-workflows.md).

Two repo-wide infra workflows are directly relevant to the book specifically: `infra-container-linux.yml` and `infra-container-windows.yml` build the Docker images `book-build-container.yml` depends on, and `infra-health-check.yml` validates those same images daily. See the [full infra section](../ci-workflows.md#4-repo-wide-and-infrastructure-workflows) for details.
