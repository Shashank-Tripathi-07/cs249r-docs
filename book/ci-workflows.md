# Book: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the book's own workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

| Workflow | Trigger | What it does |
|---|---|---|
| `book-validate-dev.yml` | push to dev, PR, dispatch | Pre-commit hooks, then a full container-based build matrix (see below). |
| `book-build-container.yml` | `workflow_call`, dispatch | Reusable matrix build: up to 12 parallel jobs (2 volumes x 3 formats x 2 OS) using pre-built Docker containers so contributors and CI never pay the 30 to 45 minute native dependency-install cost. |
| `book-preview-dev.yml` | `workflow_run` after Validate succeeds, dispatch | Downloads the validated artifacts, assembles the staging site, deploys via SSH. |
| `book-publish-live.yml` | dispatch only, requires "PUBLISH" | Merges dev into main, rebuilds via the container matrix, deploys to `gh-pages`, tags a release, generates AI-assisted release notes, reverts the merge automatically on failure or timeout. |

design-grammar has no workflow file of its own, and, corrected from an earlier version of this doc, `book-validate-dev.yml` does not actually validate it either, the only appearance of "design-grammar" in that file is a stale re-trigger comment, not a real path filter or check. See the [design-grammar CI notes](../design-grammar/ci-workflows.md) for the full, corrected finding.

Two repo-wide infra workflows are directly relevant to the book specifically: `infra-container-linux.yml` and `infra-container-windows.yml` build the Docker images `book-build-container.yml` depends on, and `infra-health-check.yml` validates those same images daily. See the [full infra section](../ci-workflows.md#4-repo-wide-and-infrastructure-workflows) for details.

## Real, verified bugs in this pipeline

**2026-05-01 (`2b1f7ef1ed`): concurrency grouped only on `github.ref`.** `book-validate-${{ github.ref }}` as the group key meant a manual `workflow_dispatch` landing on the same SHA as a push collided with it, and the README badge ended up showing the cancelled run's red status even though the underlying build had actually passed. Fixed by folding `${{ github.event_name }}` into the group key so dispatch and push runs never share a slot. The same bug independently hit two other projects, TinyTorch/Labs and StaffML, at different times, see the [top-level doc's Pattern A](../ci-workflows.md#pattern-a-concurrency-group-too-coarse-dispatch-collides-with-push).

**2026-06-20 to 2026-06-23: a terse, multi-commit release-pipeline incident.** `02996dcb1c` (Linux container TeX Live preflight), `855803df04` (a wrong Cloudflare purge token), `52906be2d8` (the publish chain continuing after a failed deploy), and `0b98b740eb` (TinyTorch PDFs wiped on a site-only deploy) landed within three days of each other. The commit messages are too terse to reconstruct the full incident from git log alone, but the clustering itself is worth knowing before touching `infra-cloudflare-purge.yml` or any `publish-live.yml`'s site-only path, that's exactly the code these commits were patching under pressure.
