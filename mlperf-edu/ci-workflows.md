# MLPerf EDU: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the MLPerf EDU workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

The odd one out in naming convention (no emoji prefix, slightly different structure) and in scope: its own header comments are explicit that this publishes documentation only, not a benchmark result, a Python package, or anything implying MLCommons endorsement.

| Workflow | What it does |
|---|---|
| `mlperf-edu-validate-dev.yml` | Fast, blocking smoke validation, the workload entry points and the smoke path, not a full benchmark run. |
| `mlperf-edu-release-validation.yml` | The real, evidence-bearing benchmark execution, with a five-hour hard timeout, run weekly on schedule or manually with a `release`/`max`/`pro` preset. Its header comment is explicit that a dry run is never reported as a real max or release validation, a direct statement that this project cares about not letting a smoke test masquerade as a real benchmark result. |
| `mlperf-edu-preview-dev.yml`, `mlperf-edu-publish-live.yml` | The usual preview/publish pair, deploying the documentation preview only. |

## A recurring, non-code CI failure worth knowing about

Multiple Dependabot PRs against `mlperf-edu/uv.lock` were traced back to two pre-existing bugs in `mlperf-edu-validate-dev.yml`'s own test suite, unrelated to whatever dependency was being bumped: a workload-registry count assertion hardcoded to 9 when the registry actually has 14 entries, and a missing `matplotlib` test dependency causing `ModuleNotFoundError` in `test_paper_figures.py`. If this project's CI is red on an otherwise-unrelated PR, check these two first before assuming the change under review broke something.
