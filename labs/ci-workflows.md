# Labs: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Labs workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

`labs-validate-dev.yml` runs notebook static analysis, a Quarto site build, and the WASM smoke test tier (build both wheels, export representative labs, run them in real headless Chromium via Playwright). `labs-preview-dev.yml` and `labs-publish-live.yml` both export every lab notebook to WASM HTML and deploy the result.

This is covered in full depth, including the exact `wasm-smoke-test` job structure, in [`system_design.md`](system_design.md) sections 5 and 6, since the CI pipeline and the runtime boot sequence share the same wheel-build and export steps, they are really one system, not two.

## A real, verified bug in `labs-validate-dev.yml`

The `wasm-smoke-test` job's dependency install step (`pip install build marimo`) pulls the latest `marimo` release with no upper version pin. A `marimo` release was found to have added a hard requirement on `uv` for `html-wasm` export's local-import resolution, and the job never installed `uv`. Because this job only re-runs when a push touches `labs/**` or `mlsysim/**`, the break sat live and undetected on `dev` for weeks between the `marimo` release that introduced it and the next relevant push. The fix is a one-line addition (`pip install build marimo uv`), but the underlying risk, an unpinned upstream dependency with no scheduled health check, remains unless the job either pins `marimo`'s upper bound or the dependency install step gets a periodic, path-independent trigger.
