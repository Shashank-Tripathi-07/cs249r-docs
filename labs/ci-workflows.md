# Labs: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Labs workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

`labs-validate-dev.yml` runs notebook static analysis, a Quarto site build, and the WASM smoke test tier (build both wheels, export representative labs, run them in real headless Chromium via Playwright). `labs-preview-dev.yml` and `labs-publish-live.yml` both export every lab notebook to WASM HTML and deploy the result.

This is covered in full depth, including the exact `wasm-smoke-test` job structure, in [`system_design.md`](system_design.md) sections 5 and 6, since the CI pipeline and the runtime boot sequence share the same wheel-build and export steps, they are really one system, not two.

## Real, verified bugs in this pipeline

**Unpinned `marimo` silently broke the WASM smoke test.** The `wasm-smoke-test` job's dependency install step (`pip install build marimo`) pulls the latest `marimo` release with no upper version pin. A `marimo` release was found to have added a hard requirement on `uv` for `html-wasm` export's local-import resolution, and the job never installed `uv`. Because this job only re-runs when a push touches `labs/**` or `mlsysim/**`, the break sat live and undetected on `dev` for weeks between the `marimo` release that introduced it and the next relevant push. The fix is a one-line addition (`pip install build marimo uv`), but the underlying risk, an unpinned upstream dependency with no scheduled health check, remains unless the job either pins `marimo`'s upper bound or the dependency install step gets a periodic, path-independent trigger.

**2026-06-15 (`cbdaf6f5fe`, issue #1859): the browser smoke test never staged the labs helper wheel.** It built and served only the `mlsysim` engine wheel, not the `mlsysbook-labs` helper wheel every lab's Cell 0 also installs via `micropip`. Every lab in the smoke test failed with `BadZipFile: File is not a zip file`, since `micropip` 404'd fetching a wheel that was never copied into place.

**2026-05-29 (`4dbfe7152a`): the smoke test imported a path that no longer existed.** `from mlsysim.core.engine import Engine` was a pre-refactor internal path; a taxonomy refactor had moved `Engine` and re-exported it at the package top level, and nothing but this one CI step still referenced the old location. Pure CI debt, not a real product bug, but it broke every run with `ModuleNotFoundError` until fixed.

**2026-05-17 (`df88ff7a72`), shared with TinyTorch: concurrency grouped only on `github.ref`.** A manual `workflow_dispatch` on the same SHA as a push would cancel the push run via `cancel-in-progress`, showing red on a commit that was actually healthy. Fixed by switching to the `head_ref || run_id` pattern. See the [top-level doc's Pattern A](../ci-workflows.md#pattern-a-concurrency-group-too-coarse-dispatch-collides-with-push) for the two other projects this exact bug independently hit.
