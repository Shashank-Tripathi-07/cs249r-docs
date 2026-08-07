# MLSys·im: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only MLSys·im's own workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows. Labs' own CI (which builds on the mlsysim wheel) is documented separately at [`../labs/ci-workflows.md`](../labs/ci-workflows.md).*

Standard triad (`mlsysim-validate-dev.yml` runs pytest plus a docs-site build; `mlsysim-preview-dev.yml`; `mlsysim-publish-live.yml`) plus two extras genuinely worth knowing about:

- **`mlsysim-pypi-publish.yml`**: triggered by pushing a `mlsysim-v*` tag, not by anything in the publish-live chain. Runs the full pytest suite across Python 3.10 through 3.13 in parallel, builds a wheel and sdist, publishes to PyPI via Trusted Publishing (OIDC, no stored PyPI token anywhere in the repo), then does a genuine post-publish smoke test: installs the just-published package from real PyPI (with retry for CDN propagation delay), imports it, and runs a CLI smoke check, before creating the GitHub Release and dispatching `mlsysim-publish-live.yml` to refresh the docs site. This is the only publish pipeline in the whole repo that verifies its own artifact after shipping it, rather than trusting the build step succeeded.
- **`mlsysim-update-pdfs.yml`**: same PDF-only-refresh pattern as TinyTorch's equivalent, for the research paper and the four ISCA tutorial slide-deck PDFs.
- **`mlsysim-build-pdfs.yml`**: the reusable workflow both of the above call to actually build the paper and slide PDFs.

See [`system_design.md`](system_design.md) section 8 for how the native-install versus WASM-wheel split (also relevant to `mlsysim-pypi-publish.yml`'s build step) actually works.
