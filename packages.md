# Packages: full dependency reference

*This document is the complete, alphabetical reference for every package resolved anywhere in this repository's dependency graph, one row per package, with which sub-project actually pulls it in and through which direct dependency. Use it the way you'd use it: a Dependabot PR arrives for some package you don't recognize, ctrl+F it here, and you know immediately which project it affects and why it's in the tree at all. For the smaller, curated view (just direct dependencies, plus the cross-project sharing analysis), see [`dependency-map.md`](dependency-map.md), this document is the exhaustive companion to that one, not a replacement for it.*

## Methodology, and an honest limit

Source: the same GitHub dependency-graph SBOM used in `dependency-map.md` (1,629 resolved packages, `spdxVersion 2.3`), plus the graph's own `DEPENDS_ON` relationships (2,585 edges). Attribution works by tracing forward from each project's real, directly-declared dependencies (read from the actual `pyproject.toml`/`package.json` files, not inferred) through the dependency graph to every package that direct dependency pulls in, transitively.

**1,094 of 1,629 packages (67%) got a real, traced attribution this way.** The remaining third didn't, and it's worth being precise about why rather than pretending the list is complete: GitHub's own graph export doesn't always preserve a "who required this" edge for platform-specific optional binaries (for example, `@cloudflare/workerd-darwin-64`, one of five OS-specific binaries Wrangler pulls in, shows up in the graph as a direct child of the whole repository rather than of `wrangler` itself), and a small number of deep devDependency chains (bundler and linter internals several layers down) weren't fully traceable from the direct dependencies catalogued. An untraced package is not necessarily unused, it means the graph itself doesn't tell us cleanly who pulls it in, not that nobody does. If you hit one of these on a Dependabot PR, the fastest real answer is `npm ls <package>` or `pip show <package>` inside the specific project directory the PR's path points at.

Each table is sorted alphabetically and is long by design, click to expand, then use your browser's find-in-page, it searches inside collapsed sections.


## Python packages (PyPI), 239 unique

<details>
<summary>Click to expand the full PyPI table</summary>

| Package | Version | Used by | Introduced via |
|---|---|---|---|
| `absl-py` |  | not traced, see note | - |
| `adjustbox` |  | not traced, see note | - |
| `ai-edge-litert` | 2.1.6 | MLPerf EDU | ai-edge-litert |
| `aiohappyeyeballs` | 2.7.1 | MLPerf EDU | torch-geometric |
| `aiohttp` | 3.14.1 | MLPerf EDU | torch-geometric |
| `aiosignal` | 1.4.0 | MLPerf EDU | torch-geometric |
| `annotated-doc` | 0.0.4 | MLPerf EDU, MLSys-im, StaffML vault-cli | sentence-transformers, transformers, typer |
| `anyio` | 4.14.1 | Labs, MLPerf EDU | marimo, sentence-transformers, transformers |
| `async-timeout` | 5.0.1 | MLPerf EDU | torch-geometric |
| `attrs` | 26.1.0 | MLPerf EDU | torch-geometric |
| `audioop-lts` | 0.2.2 | MLPerf EDU | librosa |
| `audioread` | 3.1.0 | MLPerf EDU | librosa |
| `backports-strenum` | 1.3.1 | MLPerf EDU | ai-edge-litert |
| `backports-tarfile` | 1.2.0 | MLPerf EDU | twine |
| `bandit` |  | not traced, see note | - |
| `betterbib` |  | not traced, see note | - |
| `bibtexparser` |  | not traced, see note | - |
| `black` | 26.5.1 | MLPerf EDU | black |
| `booktabs` |  | not traced, see note | - |
| `build` | 1.5.1 | MLPerf EDU | build |
| `caption` |  | not traced, see note | - |
| `certifi` | 2026.6.17 | Book tooling (root pyproject.toml), MLPerf EDU, Site newsletter CLI, TinyTorch | certifi, librosa, ogb, remotezip, requests, sentence-transformers, torch-geometric, transformers, twine |
| `cffi` | 2.1.0 | Labs, MLPerf EDU | librosa, marimo, twine |
| `charset-normalizer` | 3.4.9 | Book tooling (root pyproject.toml), MLPerf EDU, Site newsletter CLI | librosa, ogb, remotezip, requests, torch-geometric, twine |
| `click` | 8.4.2 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, StaffML vault-cli | black, click, marimo, sentence-transformers, transformers |
| `collectbox` |  | not traced, see note | - |
| `colorama` | 0.4.6 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, MLSys-im, Site newsletter CLI, StaffML vault-cli, TinyTorch | ai-edge-litert, black, build, click, marimo, ogb, pytest, sentence-transformers, torch-geometric, transformers, typer |
| `cryptography` | 49.0.0 | MLPerf EDU | twine |
| `cuda-bindings` | 13.3.1 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `cuda-pathfinder` | 1.5.6 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `cuda-toolkit` | 13.0.3.0 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `decorator` | 5.3.1 | MLPerf EDU | librosa |
| `docutils` | 0.23 | Labs, MLPerf EDU | marimo, twine |
| `enumitem` |  | not traced, see note | - |
| `environ` |  | not traced, see note | - |
| `epubcheck` |  | not traced, see note | - |
| `etoolbox` |  | not traced, see note | - |
| `exceptiongroup` | 1.3.1 | Labs, MLPerf EDU, Site newsletter CLI, TinyTorch | marimo, pytest, sentence-transformers, transformers |
| `fancyhdr` |  | not traced, see note | - |
| `filelock` | 3.29.7 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision, transformers |
| `flake8` | 7.3.0 | MLPerf EDU | flake8 |
| `flatbuffers` | 25.12.19 | MLPerf EDU | ai-edge-litert, tflite |
| `fontawesome5` |  | not traced, see note | - |
| `fontspec` |  | not traced, see note | - |
| `frozenlist` | 1.8.0 | MLPerf EDU | torch-geometric |
| `fsspec` | 2026.6.0 | MLPerf EDU | ogb, sentence-transformers, torch, torch-geometric, torchvision, transformers |
| `ghostscript` |  | not traced, see note | - |
| `google-analytics-data` |  | not traced, see note | - |
| `gradio` |  | not traced, see note | - |
| `greenlet` | 3.5.3 | MLPerf EDU, Release smoke tests | playwright |
| `groq` |  | not traced, see note | - |
| `h11` | 0.16.0 | Labs, MLPerf EDU | marimo, sentence-transformers, transformers |
| `hf-xet` | 1.5.1 | MLPerf EDU | sentence-transformers, transformers |
| `httpcore` | 1.0.9 | MLPerf EDU | sentence-transformers, transformers |
| `httpx` | 0.28.1 | MLPerf EDU | sentence-transformers, transformers |
| `huggingface-hub` | 1.23.0 | MLPerf EDU | sentence-transformers, transformers |
| `hyperref` |  | not traced, see note | - |
| `id` | 1.6.1 | MLPerf EDU | twine |
| `idna` | 3.18 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, Site newsletter CLI | librosa, marimo, ogb, remotezip, requests, sentence-transformers, torch-geometric, transformers, twine |
| `importlib-metadata` | 9.0.0 | MLPerf EDU | build, twine |
| `iniconfig` | 2.3.0 | Labs, MLPerf EDU, Site newsletter CLI, TinyTorch | pytest |
| `ipykernel` |  | TinyTorch | ipykernel |
| `ipywidgets` |  | not traced, see note | - |
| `isort` |  | not traced, see note | - |
| `itsdangerous` | 2.2.0 | Labs, MLPerf EDU | marimo |
| `jaraco-classes` | 3.4.0 | MLPerf EDU | twine |
| `jaraco-context` | 6.1.2 | MLPerf EDU | twine |
| `jaraco-functools` | 4.5.0 | MLPerf EDU | twine |
| `jedi` | 0.19.2 | Labs, MLPerf EDU | marimo |
| `jeepney` | 0.9.0 | MLPerf EDU | twine |
| `jinja2` | 3.1.6 | MLPerf EDU | ogb, sentence-transformers, torch, torch-geometric, torchvision |
| `joblib` | 1.5.3 | MLPerf EDU | librosa, ogb, scikit-learn, sentence-transformers |
| `jsonschema` |  | Book tooling (root pyproject.toml) | jsonschema |
| `jupyter` |  | Book tooling (root pyproject.toml), TinyTorch | jupyter |
| `jupyter-book` |  | not traced, see note | - |
| `jupyterlab` |  | TinyTorch | jupyterlab |
| `jupyterlab-quarto` |  | Book tooling (root pyproject.toml) | jupyterlab-quarto |
| `jupytext` |  | TinyTorch | jupytext |
| `keyring` | 25.7.0 | MLPerf EDU | twine |
| `koma-script` |  | not traced, see note | - |
| `lazy-loader` | 0.5 | MLPerf EDU | librosa |
| `librosa` | 0.11.0 | MLPerf EDU | librosa |
| `librsvg2-bin` |  | not traced, see note | - |
| `littleutils` | 0.2.4 | MLPerf EDU | ogb |
| `llvmlite` | 0.48.0 | MLPerf EDU | librosa |
| `loro` | 1.13.1 | Labs, MLPerf EDU | marimo |
| `lxml` |  | not traced, see note | - |
| `marimo` | 0.23.13 | Labs, MLPerf EDU | marimo |
| `markdown` | 3.10.2 | Labs, MLPerf EDU | marimo |
| `markdown-it-py` | 4.2.0 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im, Site newsletter CLI, StaffML vault-cli, TinyTorch | rich, sentence-transformers, transformers, twine, typer |
| `markupsafe` | 3.0.3 | MLPerf EDU | ogb, sentence-transformers, torch, torch-geometric, torchvision |
| `matplotlib` |  | MLSys-im | matplotlib |
| `mccabe` | 0.7.0 | MLPerf EDU | flake8 |
| `mdurl` | 0.1.2 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im, Site newsletter CLI, StaffML vault-cli, TinyTorch | rich, sentence-transformers, transformers, twine, typer |
| `microtype` |  | not traced, see note | - |
| `mlperf-edu` | 0.1.0 | not traced, see note | - |
| `more-itertools` | 11.1.0 | MLPerf EDU | twine |
| `mpmath` | 1.3.0 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `msgpack` | 1.2.1 | MLPerf EDU | librosa |
| `msgspec` | 0.21.1 | Labs, MLPerf EDU | marimo |
| `multidict` | 6.7.1 | MLPerf EDU | torch-geometric |
| `mypy` |  | not traced, see note | - |
| `mypy-extensions` | 1.1.0 | MLPerf EDU | black |
| `narwhals` | 2.23.0 | Labs, MLPerf EDU, MLSys-im | librosa, marimo, ogb, plotly, scikit-learn, sentence-transformers |
| `nbclient` |  | not traced, see note | - |
| `nbdev` |  | TinyTorch | nbdev |
| `nbformat` |  | TinyTorch | nbformat |
| `networkx` | 3.4.2 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `newunicodechar` |  | not traced, see note | - |
| `nh3` | 0.3.6 | MLPerf EDU | twine |
| `nltk` |  | not traced, see note | - |
| `numba` | 0.66.0 | MLPerf EDU | librosa |
| `numpy` | 2.2.6 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, MLSys-im, TinyTorch | ai-edge-litert, librosa, numpy, ogb, pandas, scikit-learn, scipy, sentence-transformers, tflite, torch-geometric, torchvision, transformers |
| `nvidia-cublas` | 13.1.1.3 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cuda-cupti` | 13.0.85 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cuda-nvrtc` | 13.0.88 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cuda-runtime` | 13.0.96 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cudnn-cu13` | 9.20.0.48 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cufft` | 12.0.0.61 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cufile` | 1.15.1.6 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-curand` | 10.4.0.35 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cusolver` | 12.0.4.66 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cusparse` | 12.6.3.3 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-cusparselt-cu13` | 0.8.1 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-nccl-cu13` | 2.29.7 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-nvjitlink` | 13.3.33 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-nvshmem-cu13` | 3.4.5 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `nvidia-nvtx` | 13.0.85 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `ogb` | 1.3.6 | MLPerf EDU | ogb |
| `ollama` |  | not traced, see note | - |
| `openai` |  | not traced, see note | - |
| `ortools` |  | MLSys-im | ortools |
| `outdated` | 0.2.2 | MLPerf EDU | ogb |
| `packaging` | 26.2 | Labs, MLPerf EDU, MLSys-im, Site newsletter CLI, TinyTorch | black, build, librosa, marimo, plotly, pytest, sentence-transformers, transformers, twine |
| `pandas` | 2.3.3 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im | ogb, pandas |
| `parso` | 0.8.7 | Labs, MLPerf EDU | marimo |
| `pathspec` | 1.1.1 | MLPerf EDU | black |
| `pgf` |  | not traced, see note | - |
| `pillow` | 12.3.0 | Book tooling (root pyproject.toml), MLPerf EDU | pillow, torchvision |
| `pint` |  | MLSys-im | pint |
| `platformdirs` | 4.10.0 | MLPerf EDU | black, librosa, platformdirs |
| `playwright` | 1.58.0 | MLPerf EDU, Release smoke tests | playwright |
| `plotly` | 6.9.0 | Labs, MLPerf EDU, MLSys-im | plotly |
| `pluggy` | 1.6.0 | Labs, MLPerf EDU, Site newsletter CLI, TinyTorch | pytest |
| `pooch` | 1.9.0 | MLPerf EDU | librosa |
| `pre-commit` |  | not traced, see note | - |
| `propcache` | 0.5.2 | MLPerf EDU | torch-geometric |
| `protobuf` | 7.35.1 | MLPerf EDU | ai-edge-litert |
| `psutil` | 7.2.2 | Labs, MLPerf EDU | marimo, torch-geometric |
| `pyarrow` | 25.0.0 | MLPerf EDU | pyarrow |
| `pybtex` |  | Book tooling (root pyproject.toml) | pybtex |
| `pycodestyle` | 2.14.0 | MLPerf EDU | flake8 |
| `pycparser` | 3.0 | Labs, MLPerf EDU | librosa, marimo, twine |
| `pydantic` | >= 2.7 | MLSys-im, StaffML vault-cli | pydantic |
| `pyee` | 13.0.1 | MLPerf EDU, Release smoke tests | playwright |
| `pyflakes` | 3.4.0 | MLPerf EDU | flake8 |
| `pygithub` |  | not traced, see note | - |
| `pygments` | 2.20.0 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, MLSys-im, Site newsletter CLI, StaffML vault-cli, TinyTorch | marimo, pytest, rich, sentence-transformers, transformers, twine, typer |
| `pylint` |  | not traced, see note | - |
| `pymdown-extensions` | 10.21.3 | Labs, MLPerf EDU | marimo |
| `pypandoc` |  | Book tooling (root pyproject.toml) | pypandoc |
| `pyparsing` | 3.3.2 | MLPerf EDU | torch-geometric |
| `pypdf` | 6.14.2 | not traced, see note | - |
| `pyproject-hooks` | 1.2.0 | MLPerf EDU | build |
| `pyright` |  | not traced, see note | - |
| `pytest` | 9.1.1 | Labs, MLPerf EDU, Site newsletter CLI, TinyTorch | pytest |
| `pytest-asyncio` |  | not traced, see note | - |
| `pytest-cov` |  | Site newsletter CLI | pytest-cov |
| `pytest-mock` |  | not traced, see note | - |
| `python-dateutil` | 2.9.0.post0 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im | ogb, pandas |
| `python-frontmatter` | >= 1.1.0 | Site newsletter CLI | python-frontmatter |
| `python-multipart` | 0.0.32 | Labs, MLPerf EDU | marimo |
| `pytokens` | 0.4.1 | MLPerf EDU | black |
| `pytz` | 2026.2 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im | ogb, pandas |
| `pywin32-ctypes` | 0.2.3 | MLPerf EDU | twine |
| `pyyaml` | 6.0.3 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, MLSys-im, StaffML vault-cli, TinyTorch | marimo, pyyaml, sentence-transformers, transformers |
| `pyzmq` | 27.1.0 | Labs, MLPerf EDU | marimo |
| `readme-renderer` | 45.0 | MLPerf EDU | twine |
| `regex` | 2026.6.28 | MLPerf EDU | sentence-transformers, transformers |
| `remotezip` | 0.12.3 | MLPerf EDU | remotezip |
| `requests` | 2.34.2 | Book tooling (root pyproject.toml), MLPerf EDU, Site newsletter CLI | librosa, ogb, remotezip, requests, torch-geometric, twine |
| `requests-toolbelt` | 1.0.0 | MLPerf EDU | twine |
| `rfc3986` | 2.0.0 | MLPerf EDU | twine |
| `rich` | 15.0.0 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im, Site newsletter CLI, StaffML vault-cli, TinyTorch | rich, sentence-transformers, transformers, twine, typer |
| `ruff` | 0.15.21 | MLPerf EDU, Site newsletter CLI | ruff |
| `safetensors` | 0.8.0 | MLPerf EDU | sentence-transformers, transformers |
| `safety` |  | not traced, see note | - |
| `scikit-learn` | 1.7.2 | MLPerf EDU | librosa, ogb, scikit-learn, sentence-transformers |
| `scipy` | 1.15.3 | MLPerf EDU, MLSys-im | librosa, ogb, scikit-learn, scipy, sentence-transformers |
| `seaborn` |  | not traced, see note | - |
| `secretstorage` | 3.5.0 | MLPerf EDU | twine |
| `sentence-transformers` | 5.5.1 | MLPerf EDU | sentence-transformers |
| `setuptools` | 83.0.0 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `sgf` | 0.5 | MLPerf EDU | sgf |
| `shellingham` | 1.5.4 | MLPerf EDU, MLSys-im, StaffML vault-cli | sentence-transformers, transformers, typer |
| `six` | 1.17.0 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im | ogb, pandas |
| `soundfile` | 0.14.0 | MLPerf EDU | librosa |
| `soxr` | 1.1.0 | MLPerf EDU | librosa |
| `sphinx` |  | not traced, see note | - |
| `sphinx-rtd-theme` |  | not traced, see note | - |
| `sphinxcontrib-mermaid` |  | not traced, see note | - |
| `standard-aifc` | 3.13.0 | MLPerf EDU | librosa |
| `standard-chunk` | 3.13.0 | MLPerf EDU | librosa |
| `standard-sunau` | 3.13.0 | MLPerf EDU | librosa |
| `starlette` | 1.3.1 | Labs, MLPerf EDU | marimo |
| `svg` |  | not traced, see note | - |
| `sympy` | 1.14.0 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `tcolorbox` |  | not traced, see note | - |
| `tex-gyre` |  | not traced, see note | - |
| `tflite` | 2.18.0 | MLPerf EDU | tflite |
| `threadpoolctl` | 3.6.0 | MLPerf EDU | librosa, ogb, scikit-learn, sentence-transformers |
| `titlecase` |  | Book tooling (root pyproject.toml) | titlecase |
| `titlesec` |  | not traced, see note | - |
| `tokenizers` | 0.22.2 | MLPerf EDU | sentence-transformers, transformers |
| `tomli` | 2.4.1 | Labs, MLPerf EDU, Site newsletter CLI, TinyTorch | black, build, pytest |
| `tomlkit` | 0.15.0 | Labs, MLPerf EDU | marimo |
| `torch` | 2.13.0 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `torch-geometric` | 2.8.0 | MLPerf EDU | torch-geometric |
| `torchaudio` | 2.11.0 | MLPerf EDU | torchaudio |
| `torchvision` | 0.28.0 | MLPerf EDU | torchvision |
| `tqdm` | 4.68.4 | MLPerf EDU | ai-edge-litert, ogb, sentence-transformers, torch-geometric, transformers |
| `transformers` | 5.13.0 | MLPerf EDU | sentence-transformers, transformers |
| `transparent` |  | not traced, see note | - |
| `trimspaces` |  | not traced, see note | - |
| `triton` | 3.7.1 | MLPerf EDU | ogb, sentence-transformers, torch, torchvision |
| `twine` | 6.2.0 | MLPerf EDU | twine |
| `typer` | 0.26.8 | MLPerf EDU, MLSys-im, StaffML vault-cli | sentence-transformers, transformers, typer |
| `typing-extensions` | 4.16.0 | Book tooling (root pyproject.toml), Labs, MLPerf EDU, Release smoke tests, Site newsletter CLI, TinyTorch | ai-edge-litert, black, librosa, marimo, ogb, playwright, pytest, sentence-transformers, torch, torch-geometric, torchvision, transformers, twine, typing-extensions |
| `tzdata` | 2026.3 | Book tooling (root pyproject.toml), MLPerf EDU, MLSys-im | ogb, pandas |
| `urllib3` | 2.7.0 | Book tooling (root pyproject.toml), MLPerf EDU, Site newsletter CLI | librosa, ogb, remotezip, requests, torch-geometric, twine |
| `uvicorn` | 0.51.0 | Labs, MLPerf EDU | marimo |
| `websockets` | 16.1 | Labs, MLPerf EDU | marimo |
| `wheel` |  | not traced, see note | - |
| `xcolor` |  | not traced, see note | - |
| `xetex` |  | not traced, see note | - |
| `xxhash` | 3.8.1 | MLPerf EDU | torch-geometric |
| `yamllint` |  | not traced, see note | - |
| `yarl` | 1.24.2 | MLPerf EDU | torch-geometric |
| `zipp` | 4.1.0 | MLPerf EDU | build, twine |
</details>

## JavaScript/TypeScript packages (npm), 855 unique

<details>
<summary>Click to expand the full npm table</summary>

| Package | Version | Used by | Introduced via |
|---|---|---|---|
| `@adobe/css-tools` | 4.4.4 | StaffML frontend | @testing-library/jest-dom |
| `@alloc/quick-lru` | 5.2.0 | StaffML frontend | @tailwindcss/postcss |
| `@antfu/install-pkg` | 1.1.0 | SocratiQ | mermaid |
| `@asamuzakjp/css-color` | 5.1.11 | StaffML frontend | jsdom |
| `@asamuzakjp/dom-selector` | 7.1.1 | StaffML frontend | jsdom |
| `@asamuzakjp/generational-cache` | 1.0.1 | StaffML frontend | jsdom |
| `@asamuzakjp/nwsapi` | 2.3.9 | StaffML frontend | jsdom |
| `@babel/code-frame` | 7.29.7 | Root Node tooling, StaffML frontend | eslint-config-next, puppeteer |
| `@babel/compat-data` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/core` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/generator` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-compilation-targets` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-globals` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-module-imports` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-module-transforms` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-string-parser` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helper-validator-identifier` | 7.29.7 | Root Node tooling, StaffML frontend | eslint-config-next, puppeteer |
| `@babel/helper-validator-option` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/helpers` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/parser` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/runtime` | 7.29.2 | SocratiQ, StaffML frontend | @testing-library/react, jspdf |
| `@babel/template` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/traverse` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@babel/types` | 7.29.7 | StaffML frontend | eslint-config-next |
| `@braintree/sanitize-url` | 7.1.1 | SocratiQ | mermaid |
| `@bramus/specificity` | 2.4.2 | StaffML frontend | jsdom |
| `@chevrotain/types` | 11.1.2 | SocratiQ | mermaid |
| `@cloudflare/kv-asset-handler` | 0.5.0 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@cloudflare/unenv-preset` | 2.16.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@cloudflare/workerd-darwin-64` | 1.20260617.1 | not traced, see note | - |
| `@cloudflare/workerd-darwin-arm64` | 1.20260617.1 | not traced, see note | - |
| `@cloudflare/workerd-linux-64` | 1.20260617.1 | not traced, see note | - |
| `@cloudflare/workerd-linux-arm64` | 1.20260617.1 | not traced, see note | - |
| `@cloudflare/workerd-windows-64` | 1.20260617.1 | not traced, see note | - |
| `@cloudflare/workers-types` | 4.20260620.1 | StaffML AI interviewer Worker, StaffML vault Worker | @cloudflare/workers-types |
| `@codemirror/autocomplete` | 6.18.4 | SocratiQ | ink-mde |
| `@codemirror/commands` | 6.7.1 | SocratiQ | ink-mde |
| `@codemirror/lang-angular` | 0.1.3 | SocratiQ | ink-mde |
| `@codemirror/lang-cpp` | 6.0.2 | SocratiQ | ink-mde |
| `@codemirror/lang-css` | 6.3.1 | SocratiQ | ink-mde |
| `@codemirror/lang-go` | 6.0.1 | SocratiQ | ink-mde |
| `@codemirror/lang-html` | 6.4.9 | SocratiQ | ink-mde |
| `@codemirror/lang-java` | 6.0.1 | SocratiQ | ink-mde |
| `@codemirror/lang-javascript` | 6.2.2 | SocratiQ | ink-mde |
| `@codemirror/lang-json` | 6.0.1 | SocratiQ | ink-mde |
| `@codemirror/lang-less` | 6.0.2 | SocratiQ | ink-mde |
| `@codemirror/lang-liquid` | 6.2.2 | SocratiQ | ink-mde |
| `@codemirror/lang-markdown` | 6.3.1 | SocratiQ | ink-mde |
| `@codemirror/lang-php` | 6.0.1 | SocratiQ | ink-mde |
| `@codemirror/lang-python` | 6.1.6 | SocratiQ | ink-mde |
| `@codemirror/lang-rust` | 6.0.1 | SocratiQ | ink-mde |
| `@codemirror/lang-sass` | 6.0.2 | SocratiQ | ink-mde |
| `@codemirror/lang-sql` | 6.8.0 | SocratiQ | ink-mde |
| `@codemirror/lang-vue` | 0.1.3 | SocratiQ | ink-mde |
| `@codemirror/lang-wast` | 6.0.2 | SocratiQ | ink-mde |
| `@codemirror/lang-xml` | 6.1.0 | SocratiQ | ink-mde |
| `@codemirror/lang-yaml` | 6.1.2 | SocratiQ | ink-mde |
| `@codemirror/language` | 6.10.8 | SocratiQ | ink-mde |
| `@codemirror/language-data` | 6.5.1 | SocratiQ | ink-mde |
| `@codemirror/legacy-modes` | 6.4.2 | SocratiQ | ink-mde |
| `@codemirror/lint` | 6.8.4 | SocratiQ | ink-mde |
| `@codemirror/search` | 6.5.8 | SocratiQ | ink-mde |
| `@codemirror/state` | 6.5.0 | SocratiQ | ink-mde |
| `@codemirror/view` | 6.36.1 | SocratiQ | ink-mde |
| `@cspotcode/source-map-support` | 0.8.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@csstools/color-helpers` | 6.0.2 | StaffML frontend | jsdom |
| `@csstools/css-calc` | 3.2.0 | StaffML frontend | jsdom |
| `@csstools/css-color-parser` | 4.1.0 | StaffML frontend | jsdom |
| `@csstools/css-parser-algorithms` | 4.0.0 | StaffML frontend | jsdom |
| `@csstools/css-syntax-patches-for-csstree` | 1.1.3 | StaffML frontend | jsdom |
| `@csstools/css-tokenizer` | 4.0.0 | StaffML frontend | jsdom |
| `@emnapi/core` | 1.10.0 | not traced, see note | - |
| `@emnapi/runtime` | 1.11.1 | not traced, see note | - |
| `@emnapi/wasi-threads` | 1.2.1 | not traced, see note | - |
| `@esbuild/aix-ppc64` | 0.28.1 | not traced, see note | - |
| `@esbuild/android-arm` | 0.28.1 | not traced, see note | - |
| `@esbuild/android-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/android-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/darwin-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/darwin-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/freebsd-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/freebsd-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-arm` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-ia32` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-loong64` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-mips64el` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-ppc64` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-riscv64` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-s390x` | 0.28.1 | not traced, see note | - |
| `@esbuild/linux-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/netbsd-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/netbsd-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/openbsd-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/openbsd-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/openharmony-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/sunos-x64` | 0.28.1 | not traced, see note | - |
| `@esbuild/win32-arm64` | 0.28.1 | not traced, see note | - |
| `@esbuild/win32-ia32` | 0.28.1 | not traced, see note | - |
| `@esbuild/win32-x64` | 0.28.1 | not traced, see note | - |
| `@eslint-community/eslint-utils` | 4.9.1 | StaffML frontend | eslint, eslint-config-next |
| `@eslint-community/regexpp` | 4.12.2 | StaffML frontend | eslint, eslint-config-next |
| `@eslint/config-array` | 0.23.5 | StaffML frontend | eslint |
| `@eslint/config-helpers` | 0.6.0 | StaffML frontend | eslint |
| `@eslint/core` | 1.2.1 | StaffML frontend | eslint |
| `@eslint/object-schema` | 3.0.5 | StaffML frontend | eslint |
| `@eslint/plugin-kit` | 0.7.1 | StaffML frontend | eslint |
| `@exodus/bytes` | 1.15.0 | StaffML frontend | jsdom |
| `@humanfs/core` | 0.19.2 | StaffML frontend | eslint |
| `@humanfs/node` | 0.16.8 | StaffML frontend | eslint |
| `@humanfs/types` | 0.15.0 | StaffML frontend | eslint |
| `@humanwhocodes/module-importer` | 1.0.1 | StaffML frontend | eslint |
| `@humanwhocodes/retry` | 0.4.3 | StaffML frontend | eslint |
| `@iconify/types` | 2.0.0 | SocratiQ | mermaid |
| `@iconify/utils` | 3.1.0 | SocratiQ | mermaid |
| `@img/colour` | 1.1.0 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@img/sharp-darwin-arm64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-darwin-x64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-libvips-darwin-arm64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-darwin-x64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-arm` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-arm64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-ppc64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-riscv64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-s390x` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linux-x64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linuxmusl-arm64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-libvips-linuxmusl-x64` | 1.2.4 | not traced, see note | - |
| `@img/sharp-linux-arm` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linux-arm64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linux-ppc64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linux-riscv64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linux-s390x` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linux-x64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linuxmusl-arm64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-linuxmusl-x64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-wasm32` | 0.34.5 | not traced, see note | - |
| `@img/sharp-win32-arm64` | 0.34.5 | not traced, see note | - |
| `@img/sharp-win32-ia32` | 0.34.5 | not traced, see note | - |
| `@img/sharp-win32-x64` | 0.34.5 | not traced, see note | - |
| `@jridgewell/gen-mapping` | 0.3.13 | StaffML frontend | @tailwindcss/postcss, eslint-config-next |
| `@jridgewell/remapping` | 2.3.5 | StaffML frontend | @tailwindcss/postcss, eslint-config-next |
| `@jridgewell/resolve-uri` | 3.1.2 | StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, eslint-config-next, wrangler |
| `@jridgewell/sourcemap-codec` | 1.5.5 | StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, eslint-config-next, vitest, wrangler |
| `@jridgewell/trace-mapping` | 0.3.9 | StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, eslint-config-next, wrangler |
| `@kurkle/color` | 0.3.4 | SocratiQ | chart.js |
| `@leeoniya/ufuzzy` | 1.0.17 | SocratiQ | @leeoniya/ufuzzy |
| `@lezer/common` | 1.2.3 | SocratiQ | ink-mde |
| `@lezer/cpp` | 1.1.2 | SocratiQ | ink-mde |
| `@lezer/css` | 1.1.9 | SocratiQ | ink-mde |
| `@lezer/go` | 1.0.0 | SocratiQ | ink-mde |
| `@lezer/highlight` | 1.2.1 | SocratiQ | ink-mde |
| `@lezer/html` | 1.3.10 | SocratiQ | ink-mde |
| `@lezer/java` | 1.1.3 | SocratiQ | ink-mde |
| `@lezer/javascript` | 1.4.21 | SocratiQ | ink-mde |
| `@lezer/json` | 1.0.2 | SocratiQ | ink-mde |
| `@lezer/lr` | 1.4.2 | SocratiQ | ink-mde |
| `@lezer/markdown` | 1.3.2 | SocratiQ | ink-mde |
| `@lezer/php` | 1.0.2 | SocratiQ | ink-mde |
| `@lezer/python` | 1.1.15 | SocratiQ | ink-mde |
| `@lezer/rust` | 1.0.2 | SocratiQ | ink-mde |
| `@lezer/sass` | 1.0.7 | SocratiQ | ink-mde |
| `@lezer/xml` | 1.0.6 | SocratiQ | ink-mde |
| `@lezer/yaml` | 1.0.3 | SocratiQ | ink-mde |
| `@marijn/find-cluster-break` | 1.0.2 | SocratiQ | ink-mde |
| `@mermaid-js/parser` | 1.1.1 | SocratiQ | mermaid |
| `@napi-rs/wasm-runtime` | 1.1.5 | not traced, see note | - |
| `@next/env` | 16.2.11 | StaffML frontend | next |
| `@next/eslint-plugin-next` | 16.2.6 | StaffML frontend | eslint-config-next |
| `@next/swc-darwin-arm64` | 16.2.11 | not traced, see note | - |
| `@next/swc-darwin-x64` | 16.2.11 | not traced, see note | - |
| `@next/swc-linux-arm64-gnu` | 16.2.11 | not traced, see note | - |
| `@next/swc-linux-arm64-musl` | 16.2.11 | not traced, see note | - |
| `@next/swc-linux-x64-gnu` | 16.2.11 | not traced, see note | - |
| `@next/swc-linux-x64-musl` | 16.2.11 | not traced, see note | - |
| `@next/swc-win32-arm64-msvc` | 16.2.11 | not traced, see note | - |
| `@next/swc-win32-x64-msvc` | 16.2.11 | not traced, see note | - |
| `@nodelib/fs.scandir` | 2.1.5 | StaffML frontend | eslint-config-next |
| `@nodelib/fs.stat` | 2.0.5 | StaffML frontend | eslint-config-next |
| `@nodelib/fs.walk` | 1.2.8 | StaffML frontend | eslint-config-next |
| `@nolyfill/is-core-module` | 1.0.39 | StaffML frontend | eslint-config-next |
| `@oxc-project/types` | 0.133.0 | SocratiQ, StaffML frontend, StaffML vault Worker | vite, vitest |
| `@playwright/test` | 1.58.2 | StaffML frontend, TinyTorch community dashboard tests | @playwright/test |
| `@poppinss/colors` | 4.1.6 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@poppinss/dumper` | 0.6.5 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@poppinss/exception` | 1.2.3 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@puppeteer/browsers` | 2.13.0 | Root Node tooling | puppeteer |
| `@react-sigma/core` | 5.0.6 | StaffML frontend | @react-sigma/core |
| `@replit/codemirror-vim` | 6.2.1 | SocratiQ | ink-mde |
| `@rolldown/binding-android-arm64` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-darwin-arm64` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-darwin-x64` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-freebsd-x64` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-arm-gnueabihf` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-arm64-gnu` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-arm64-musl` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-ppc64-gnu` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-s390x-gnu` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-x64-gnu` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-linux-x64-musl` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-openharmony-arm64` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-wasm32-wasi` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-win32-arm64-msvc` | 1.0.3 | not traced, see note | - |
| `@rolldown/binding-win32-x64-msvc` | 1.0.3 | not traced, see note | - |
| `@rolldown/pluginutils` | 1.0.1 | SocratiQ, StaffML frontend, StaffML vault Worker | @vitejs/plugin-react, vite, vitest |
| `@rtsao/scc` | 1.1.0 | StaffML frontend | eslint-config-next |
| `@sindresorhus/is` | 7.2.0 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@speed-highlight/core` | 1.2.17 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `@standard-schema/spec` | 1.1.0 | StaffML frontend, StaffML vault Worker | vitest |
| `@swc/helpers` | 0.5.15 | StaffML frontend | next |
| `@tailwindcss/node` | 4.2.4 | StaffML frontend | @tailwindcss/postcss |
| `@tailwindcss/oxide` | 4.2.4 | StaffML frontend | @tailwindcss/postcss |
| `@tailwindcss/oxide-android-arm64` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-darwin-arm64` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-darwin-x64` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-freebsd-x64` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-linux-arm-gnueabihf` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-linux-arm64-gnu` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-linux-arm64-musl` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-linux-x64-gnu` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-linux-x64-musl` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-wasm32-wasi` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-win32-arm64-msvc` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/oxide-win32-x64-msvc` | 4.2.4 | not traced, see note | - |
| `@tailwindcss/postcss` | 4.2.4 | StaffML frontend | @tailwindcss/postcss |
| `@testing-library/dom` | 10.4.1 | not traced, see note | - |
| `@testing-library/jest-dom` | 6.9.1 | StaffML frontend | @testing-library/jest-dom |
| `@testing-library/react` | 16.3.2 | StaffML frontend | @testing-library/react |
| `@tootallnate/quickjs-emscripten` | 0.23.0 | Root Node tooling | puppeteer |
| `@tybys/wasm-util` | 0.10.2 | not traced, see note | - |
| `@types/aria-query` | 5.0.4 | not traced, see note | - |
| `@types/chai` | 5.2.3 | StaffML frontend, StaffML vault Worker | vitest |
| `@types/d3` | 7.4.3 | SocratiQ | mermaid |
| `@types/d3-array` | 3.2.1 | SocratiQ | mermaid |
| `@types/d3-axis` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-brush` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-chord` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-color` | 3.1.3 | SocratiQ | mermaid |
| `@types/d3-contour` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-delaunay` | 6.0.4 | SocratiQ | mermaid |
| `@types/d3-dispatch` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-drag` | 3.0.7 | SocratiQ | mermaid |
| `@types/d3-dsv` | 3.0.7 | SocratiQ | mermaid |
| `@types/d3-ease` | 3.0.2 | SocratiQ | mermaid |
| `@types/d3-fetch` | 3.0.7 | SocratiQ | mermaid |
| `@types/d3-force` | 3.0.10 | SocratiQ | mermaid |
| `@types/d3-format` | 3.0.4 | SocratiQ | mermaid |
| `@types/d3-geo` | 3.1.0 | SocratiQ | mermaid |
| `@types/d3-hierarchy` | 3.1.7 | SocratiQ | mermaid |
| `@types/d3-interpolate` | 3.0.4 | SocratiQ | mermaid |
| `@types/d3-path` | 3.1.0 | SocratiQ | mermaid |
| `@types/d3-polygon` | 3.0.2 | SocratiQ | mermaid |
| `@types/d3-quadtree` | 3.0.6 | SocratiQ | mermaid |
| `@types/d3-random` | 3.0.3 | SocratiQ | mermaid |
| `@types/d3-scale` | 4.0.8 | SocratiQ | mermaid |
| `@types/d3-scale-chromatic` | 3.1.0 | SocratiQ | mermaid |
| `@types/d3-selection` | 3.0.11 | SocratiQ | mermaid |
| `@types/d3-shape` | 3.1.6 | SocratiQ | mermaid |
| `@types/d3-time` | 3.0.4 | SocratiQ | mermaid |
| `@types/d3-time-format` | 4.0.3 | SocratiQ | mermaid |
| `@types/d3-timer` | 3.0.2 | SocratiQ | mermaid |
| `@types/d3-transition` | 3.0.9 | SocratiQ | mermaid |
| `@types/d3-zoom` | 3.0.8 | SocratiQ | mermaid |
| `@types/deep-eql` | 4.0.2 | StaffML frontend, StaffML vault Worker | vitest |
| `@types/esrecurse` | 4.3.1 | StaffML frontend | eslint |
| `@types/estree` | 1.0.8 | StaffML frontend, StaffML vault Worker | eslint, vitest |
| `@types/geojson` | 7946.0.15 | SocratiQ | mermaid |
| `@types/js-quantities` | 1.6.6 | not traced, see note | - |
| `@types/js-yaml` | 4.0.9 | not traced, see note | - |
| `@types/json-schema` | 7.0.15 | StaffML frontend | eslint |
| `@types/json5` | 0.0.29 | StaffML frontend | eslint-config-next |
| `@types/katex` | 0.16.8 | not traced, see note | - |
| `@types/node` | 25.8.0 | not traced, see note | - |
| `@types/pako` | 2.0.4 | SocratiQ | jspdf |
| `@types/raf` | 3.4.3 | not traced, see note | - |
| `@types/react` | 19.2.14 | not traced, see note | - |
| `@types/react-dom` | 19.2.3 | not traced, see note | - |
| `@types/trusted-types` | 2.0.7 | not traced, see note | - |
| `@types/vscode` | 1.120.0 | not traced, see note | - |
| `@types/yauzl` | 2.10.3 | not traced, see note | - |
| `@typescript-eslint/eslint-plugin` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/parser` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/project-service` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/scope-manager` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/tsconfig-utils` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/type-utils` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/types` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/typescript-estree` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/utils` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@typescript-eslint/visitor-keys` | 8.59.0 | StaffML frontend | eslint-config-next |
| `@unrs/resolver-binding-android-arm-eabi` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-android-arm64` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-darwin-arm64` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-darwin-x64` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-freebsd-x64` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-arm-gnueabihf` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-arm-musleabihf` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-arm64-gnu` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-arm64-musl` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-ppc64-gnu` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-riscv64-gnu` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-riscv64-musl` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-s390x-gnu` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-x64-gnu` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-linux-x64-musl` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-wasm32-wasi` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-win32-arm64-msvc` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-win32-ia32-msvc` | 1.11.1 | not traced, see note | - |
| `@unrs/resolver-binding-win32-x64-msvc` | 1.11.1 | not traced, see note | - |
| `@upsetjs/venn.js` | 2.0.0 | SocratiQ | mermaid |
| `@vitejs/plugin-react` | 6.0.2 | StaffML frontend | @vitejs/plugin-react |
| `@vitest/expect` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/mocker` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/pretty-format` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/runner` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/snapshot` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/spy` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `@vitest/utils` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `acorn` | 8.16.0 | SocratiQ, StaffML frontend | eslint, mermaid |
| `acorn-jsx` | 5.3.2 | StaffML frontend | eslint |
| `agent-base` | 7.1.4 | Root Node tooling | puppeteer |
| `ajv` | 6.15.0 | StaffML frontend | eslint |
| `ansi-regex` | 5.0.1 | Root Node tooling | puppeteer |
| `ansi-styles` | 5.2.0 | Root Node tooling | puppeteer |
| `argparse` | 2.0.1 | Root Node tooling, SocratiQ, StaffML frontend, design-grammar | js-yaml, markdown-it, puppeteer |
| `aria-query` | 5.3.0 | StaffML frontend | @testing-library/jest-dom, eslint-config-next |
| `array-buffer-byte-length` | 1.0.2 | StaffML frontend | eslint-config-next |
| `array-includes` | 3.1.9 | StaffML frontend | eslint-config-next |
| `array.prototype.findlast` | 1.2.5 | StaffML frontend | eslint-config-next |
| `array.prototype.findlastindex` | 1.2.6 | StaffML frontend | eslint-config-next |
| `array.prototype.flat` | 1.3.3 | StaffML frontend | eslint-config-next |
| `array.prototype.flatmap` | 1.3.3 | StaffML frontend | eslint-config-next |
| `array.prototype.tosorted` | 1.1.4 | StaffML frontend | eslint-config-next |
| `arraybuffer.prototype.slice` | 1.0.4 | StaffML frontend | eslint-config-next |
| `assertion-error` | 2.0.1 | StaffML frontend, StaffML vault Worker | vitest |
| `ast-types` | 0.13.4 | Root Node tooling | puppeteer |
| `ast-types-flow` | 0.0.8 | StaffML frontend | eslint-config-next |
| `async-function` | 1.0.0 | StaffML frontend | eslint-config-next |
| `autoprefixer` | 10.5.0 | StaffML frontend | autoprefixer |
| `available-typed-arrays` | 1.0.7 | StaffML frontend | eslint-config-next |
| `axe-core` | 4.11.3 | StaffML frontend | eslint-config-next |
| `axobject-query` | 4.1.0 | StaffML frontend | eslint-config-next |
| `b4a` | 1.8.0 | Root Node tooling | puppeteer |
| `balanced-match` | 1.0.2 | StaffML frontend | eslint, eslint-config-next |
| `bare-events` | 2.8.2 | Root Node tooling | puppeteer |
| `bare-fs` | 4.7.1 | Root Node tooling | puppeteer |
| `bare-os` | 3.9.0 | Root Node tooling | puppeteer |
| `bare-path` | 3.0.0 | Root Node tooling | puppeteer |
| `bare-stream` | 2.13.0 | Root Node tooling | puppeteer |
| `bare-url` | 2.4.2 | Root Node tooling | puppeteer |
| `base64-arraybuffer` | 1.0.2 | not traced, see note | - |
| `baseline-browser-mapping` | 2.10.29 | StaffML frontend | autoprefixer, eslint-config-next, next |
| `basic-ftp` | 5.3.1 | Root Node tooling | puppeteer |
| `bidi-js` | 1.0.3 | StaffML frontend | jsdom |
| `blake3-wasm` | 2.1.5 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `boarding.js` | 3.6.0 | SocratiQ | boarding.js |
| `brace-expansion` | 1.1.15 | StaffML frontend | eslint, eslint-config-next |
| `braces` | 3.0.3 | SocratiQ, StaffML frontend | eslint-config-next, vite-plugin-singlefile |
| `browserslist` | 4.28.2 | StaffML frontend | autoprefixer, eslint-config-next |
| `buffer-crc32` | 0.2.13 | Root Node tooling | puppeteer |
| `call-bind` | 1.0.9 | StaffML frontend | eslint-config-next |
| `call-bind-apply-helpers` | 1.0.2 | StaffML frontend | eslint-config-next |
| `call-bound` | 1.0.4 | StaffML frontend | eslint-config-next |
| `callsites` | 3.1.0 | Root Node tooling | puppeteer |
| `caniuse-lite` | 1.0.30001792 | StaffML frontend | autoprefixer, eslint-config-next, next |
| `canvg` | 3.0.11 | not traced, see note | - |
| `chai` | 6.2.2 | StaffML frontend, StaffML vault Worker | vitest |
| `chart.js` | 4.4.7 | SocratiQ | chart.js |
| `chokidar` | 4.0.3 | SocratiQ | chokidar |
| `chromium-bidi` | 14.0.0 | Root Node tooling | puppeteer |
| `client-only` | 0.0.1 | StaffML frontend | next |
| `cliui` | 8.0.1 | Root Node tooling | puppeteer |
| `clsx` | 2.1.1 | StaffML frontend | clsx |
| `color-convert` | 2.0.1 | Root Node tooling | puppeteer |
| `color-name` | 1.1.4 | Root Node tooling | puppeteer |
| `commander` | 8.3.0 | SocratiQ, StaffML frontend | d3, ink-mde, katex, mermaid |
| `compromise` | 14.14.3 | SocratiQ | compromise |
| `concat-map` | 0.0.1 | StaffML frontend | eslint-config-next |
| `confbox` | 0.1.8 | SocratiQ | mermaid |
| `convert-source-map` | 2.0.0 | StaffML frontend, StaffML vault Worker | eslint-config-next, vitest |
| `cookie` | 1.1.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `core-js` | 3.49.0 | not traced, see note | - |
| `cose-base` | 2.2.0 | SocratiQ | mermaid |
| `cosmiconfig` | 9.0.1 | Root Node tooling | puppeteer |
| `crelt` | 1.0.6 | SocratiQ | ink-mde |
| `cross-spawn` | 7.0.6 | StaffML frontend | eslint |
| `crypto-js` | 4.2.0 | SocratiQ | crypto-js |
| `css-line-break` | 2.1.0 | not traced, see note | - |
| `css-tree` | 3.2.1 | StaffML frontend | jsdom |
| `css.escape` | 1.5.1 | StaffML frontend | @testing-library/jest-dom |
| `csstype` | 3.1.3 | SocratiQ | ink-mde |
| `cytoscape` | 3.33.2 | SocratiQ | mermaid |
| `cytoscape-cose-bilkent` | 4.1.0 | SocratiQ | mermaid |
| `cytoscape-fcose` | 2.2.0 | SocratiQ | mermaid |
| `d3` | 7.9.0 | SocratiQ | d3, mermaid |
| `d3-array` | 2.12.1 | SocratiQ | d3, mermaid |
| `d3-axis` | 3.0.0 | SocratiQ | d3, mermaid |
| `d3-brush` | 3.0.0 | SocratiQ | d3, mermaid |
| `d3-chord` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-color` | 3.1.0 | SocratiQ | d3, mermaid |
| `d3-contour` | 4.0.2 | SocratiQ | d3, mermaid |
| `d3-delaunay` | 6.0.4 | SocratiQ | d3, mermaid |
| `d3-dispatch` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-drag` | 3.0.0 | SocratiQ | d3, mermaid |
| `d3-dsv` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-ease` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-fetch` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-force` | 3.0.0 | SocratiQ | d3, mermaid |
| `d3-format` | 3.1.0 | SocratiQ | d3, mermaid |
| `d3-geo` | 3.1.1 | SocratiQ | d3, mermaid |
| `d3-hierarchy` | 3.1.2 | SocratiQ | d3, mermaid |
| `d3-interpolate` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-path` | 1.0.9 | SocratiQ | d3, mermaid |
| `d3-polygon` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-quadtree` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-random` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-sankey` | 0.12.3 | SocratiQ | mermaid |
| `d3-scale` | 4.0.2 | SocratiQ | d3, mermaid |
| `d3-scale-chromatic` | 3.1.0 | SocratiQ | d3, mermaid |
| `d3-selection` | 3.0.0 | SocratiQ | d3, mermaid |
| `d3-shape` | 1.3.7 | SocratiQ | d3, mermaid |
| `d3-time` | 3.1.0 | SocratiQ | d3, mermaid |
| `d3-time-format` | 4.1.0 | SocratiQ | d3, mermaid |
| `d3-timer` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-transition` | 3.0.1 | SocratiQ | d3, mermaid |
| `d3-zoom` | 3.0.0 | SocratiQ | d3, mermaid |
| `dagre-d3-es` | 7.0.14 | SocratiQ | mermaid |
| `damerau-levenshtein` | 1.0.8 | StaffML frontend | eslint-config-next |
| `data-uri-to-buffer` | 6.0.2 | Root Node tooling | puppeteer |
| `data-urls` | 7.0.0 | StaffML frontend | jsdom |
| `data-view-buffer` | 1.0.2 | StaffML frontend | eslint-config-next |
| `data-view-byte-length` | 1.0.2 | StaffML frontend | eslint-config-next |
| `data-view-byte-offset` | 1.0.1 | StaffML frontend | eslint-config-next |
| `dayjs` | 1.11.20 | SocratiQ | mermaid |
| `debug` | 4.4.3 | Root Node tooling, StaffML frontend | eslint, eslint-config-next, puppeteer |
| `decimal.js` | 10.6.0 | StaffML frontend | jsdom |
| `deep-is` | 0.1.4 | StaffML frontend | eslint |
| `define-data-property` | 1.1.4 | StaffML frontend | eslint-config-next |
| `define-properties` | 1.2.1 | StaffML frontend | eslint-config-next |
| `degenerator` | 5.0.1 | Root Node tooling | puppeteer |
| `delaunator` | 5.0.1 | SocratiQ | d3, mermaid |
| `dequal` | 2.0.3 | StaffML frontend | @testing-library/jest-dom |
| `detect-libc` | 2.1.2 | SocratiQ, StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, vite, vitest, wrangler |
| `devtools-protocol` | 0.0.1595872 | Root Node tooling | puppeteer |
| `doctrine` | 2.1.0 | StaffML frontend | eslint-config-next |
| `dom-accessibility-api` | 0.6.3 | StaffML frontend | @testing-library/jest-dom |
| `dompurify` | 3.4.11 | SocratiQ | dompurify, mermaid |
| `dunder-proto` | 1.0.1 | StaffML frontend | eslint-config-next |
| `efrt` | 2.7.0 | SocratiQ | compromise |
| `electron-to-chromium` | 1.5.353 | StaffML frontend | autoprefixer, eslint-config-next |
| `emoji-regex` | 9.2.2 | Root Node tooling, StaffML frontend | eslint-config-next, puppeteer |
| `end-of-stream` | 1.4.5 | Root Node tooling | puppeteer |
| `enhanced-resolve` | 5.21.0 | StaffML frontend | @tailwindcss/postcss |
| `entities` | 4.5.0 | SocratiQ, StaffML frontend | jsdom, markdown-it |
| `env-paths` | 2.2.1 | Root Node tooling | puppeteer |
| `error-ex` | 1.3.4 | Root Node tooling | puppeteer |
| `error-stack-parser-es` | 1.0.5 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `es-abstract` | 1.24.2 | StaffML frontend | eslint-config-next |
| `es-define-property` | 1.0.1 | StaffML frontend | eslint-config-next |
| `es-errors` | 1.3.0 | StaffML frontend | eslint-config-next |
| `es-iterator-helpers` | 1.3.2 | StaffML frontend | eslint-config-next |
| `es-module-lexer` | 2.0.0 | StaffML frontend, StaffML vault Worker | vitest |
| `es-object-atoms` | 1.1.1 | StaffML frontend | eslint-config-next |
| `es-set-tostringtag` | 2.1.0 | StaffML frontend | eslint-config-next |
| `es-shim-unscopables` | 1.1.0 | StaffML frontend | eslint-config-next |
| `es-to-primitive` | 1.3.0 | StaffML frontend | eslint-config-next |
| `es-toolkit` | 1.46.1 | SocratiQ | mermaid |
| `esbuild` | 0.28.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `escalade` | 3.2.0 | Root Node tooling, StaffML frontend | autoprefixer, eslint-config-next, puppeteer |
| `escape-string-regexp` | 4.0.0 | StaffML frontend | eslint |
| `escodegen` | 2.1.0 | Root Node tooling | puppeteer |
| `eslint` | 10.4.0 | StaffML frontend | eslint |
| `eslint-config-next` | 16.2.6 | StaffML frontend | eslint-config-next |
| `eslint-import-resolver-node` | 0.3.10 | StaffML frontend | eslint-config-next |
| `eslint-import-resolver-typescript` | 3.10.1 | StaffML frontend | eslint-config-next |
| `eslint-module-utils` | 2.12.1 | StaffML frontend | eslint-config-next |
| `eslint-plugin-import` | 2.32.0 | StaffML frontend | eslint-config-next |
| `eslint-plugin-jsx-a11y` | 6.10.2 | StaffML frontend | eslint-config-next |
| `eslint-plugin-react` | 7.37.5 | StaffML frontend | eslint-config-next |
| `eslint-plugin-react-hooks` | 7.1.1 | StaffML frontend | eslint-config-next |
| `eslint-scope` | 9.1.2 | StaffML frontend | eslint |
| `eslint-visitor-keys` | 3.4.3 | StaffML frontend | eslint, eslint-config-next |
| `espree` | 11.2.0 | StaffML frontend | eslint |
| `esprima` | 4.0.1 | Root Node tooling | puppeteer |
| `esquery` | 1.7.0 | StaffML frontend | eslint |
| `esrecurse` | 4.3.0 | StaffML frontend | eslint |
| `estraverse` | 5.3.0 | Root Node tooling, StaffML frontend | eslint, eslint-config-next, puppeteer |
| `estree-walker` | 3.0.3 | StaffML frontend, StaffML vault Worker | vitest |
| `esutils` | 2.0.3 | Root Node tooling, StaffML frontend | eslint, eslint-config-next, puppeteer |
| `events` | 3.3.0 | StaffML frontend | graphology, sigma |
| `events-universal` | 1.0.1 | Root Node tooling | puppeteer |
| `expect-type` | 1.3.0 | StaffML frontend, StaffML vault Worker | vitest |
| `extract-zip` | 2.0.1 | Root Node tooling | puppeteer |
| `fast-deep-equal` | 3.1.3 | StaffML frontend | eslint |
| `fast-fifo` | 1.3.2 | Root Node tooling | puppeteer |
| `fast-glob` | 3.3.1 | StaffML frontend | eslint-config-next |
| `fast-json-stable-stringify` | 2.1.0 | StaffML frontend | eslint |
| `fast-levenshtein` | 2.0.6 | StaffML frontend | eslint |
| `fast-png` | 6.4.0 | SocratiQ | jspdf |
| `fastq` | 1.20.1 | StaffML frontend | eslint-config-next |
| `fd-slicer` | 1.1.0 | Root Node tooling | puppeteer |
| `fdir` | 6.5.0 | SocratiQ, StaffML frontend, StaffML vault Worker | eslint-config-next, vite, vitest |
| `fflate` | 0.8.2 | SocratiQ | jspdf |
| `file-entry-cache` | 8.0.0 | StaffML frontend | eslint |
| `fill-range` | 7.1.1 | SocratiQ, StaffML frontend | eslint-config-next, vite-plugin-singlefile |
| `find-up` | 5.0.0 | StaffML frontend | eslint |
| `flat-cache` | 4.0.1 | StaffML frontend | eslint |
| `flatted` | 3.4.2 | StaffML frontend | eslint |
| `for-each` | 0.3.5 | StaffML frontend | eslint-config-next |
| `fraction.js` | 5.3.4 | StaffML frontend | autoprefixer |
| `framer-motion` | 12.38.0 | StaffML frontend | framer-motion |
| `fsevents` | 2.3.3 | not traced, see note | - |
| `function-bind` | 1.1.2 | StaffML frontend | eslint-config-next |
| `function.prototype.name` | 1.1.8 | StaffML frontend | eslint-config-next |
| `functions-have-names` | 1.2.3 | StaffML frontend | eslint-config-next |
| `generator-function` | 2.0.1 | StaffML frontend | eslint-config-next |
| `gensync` | 1.0.0-beta.2 | StaffML frontend | eslint-config-next |
| `get-caller-file` | 2.0.5 | Root Node tooling | puppeteer |
| `get-intrinsic` | 1.3.0 | StaffML frontend | eslint-config-next |
| `get-proto` | 1.0.1 | StaffML frontend | eslint-config-next |
| `get-stream` | 5.2.0 | Root Node tooling | puppeteer |
| `get-symbol-description` | 1.1.0 | StaffML frontend | eslint-config-next |
| `get-tsconfig` | 4.14.0 | StaffML frontend | eslint-config-next |
| `get-uri` | 6.0.5 | Root Node tooling | puppeteer |
| `glob-parent` | 6.0.2 | StaffML frontend | eslint, eslint-config-next |
| `globals` | 16.4.0 | StaffML frontend | eslint-config-next |
| `globalthis` | 1.0.4 | StaffML frontend | eslint-config-next |
| `gopd` | 1.2.0 | StaffML frontend | eslint-config-next |
| `graceful-fs` | 4.2.11 | StaffML frontend | @tailwindcss/postcss |
| `grad-school` | 0.0.5 | SocratiQ | compromise |
| `graphology` | 0.26.0 | StaffML frontend | graphology |
| `graphology-layout-forceatlas2` | 0.10.1 | StaffML frontend | graphology-layout-forceatlas2 |
| `graphology-types` | 0.24.8 | not traced, see note | - |
| `graphology-utils` | 2.5.2 | StaffML frontend | graphology-layout-forceatlas2, sigma |
| `hachure-fill` | 0.5.2 | SocratiQ | mermaid |
| `has-bigints` | 1.1.0 | StaffML frontend | eslint-config-next |
| `has-property-descriptors` | 1.0.2 | StaffML frontend | eslint-config-next |
| `has-proto` | 1.2.0 | StaffML frontend | eslint-config-next |
| `has-symbols` | 1.1.0 | StaffML frontend | eslint-config-next |
| `has-tostringtag` | 1.0.2 | StaffML frontend | eslint-config-next |
| `hasown` | 2.0.2 | StaffML frontend | eslint-config-next |
| `hermes-estree` | 0.25.1 | StaffML frontend | eslint-config-next |
| `hermes-parser` | 0.25.1 | StaffML frontend | eslint-config-next |
| `html-encoding-sniffer` | 6.0.0 | StaffML frontend | jsdom |
| `html2canvas` | 1.4.1 | not traced, see note | - |
| `http-proxy-agent` | 7.0.2 | Root Node tooling | puppeteer |
| `https-proxy-agent` | 7.0.6 | Root Node tooling | puppeteer |
| `iconv-lite` | 0.6.3 | SocratiQ | d3, mermaid |
| `idb` | 8.0.1 | SocratiQ | idb |
| `ignore` | 7.0.5 | StaffML frontend | eslint, eslint-config-next |
| `import-fresh` | 3.3.1 | Root Node tooling | puppeteer |
| `imurmurhash` | 0.1.4 | StaffML frontend | eslint |
| `indent-string` | 4.0.0 | StaffML frontend | @testing-library/jest-dom |
| `ink-mde` | 0.34.0 | SocratiQ | ink-mde |
| `internal-slot` | 1.1.0 | StaffML frontend | eslint-config-next |
| `internmap` | 2.0.3 | SocratiQ | d3, mermaid |
| `iobuffer` | 5.4.0 | SocratiQ | jspdf |
| `ip-address` | 10.2.0 | Root Node tooling | puppeteer |
| `is-array-buffer` | 3.0.5 | StaffML frontend | eslint-config-next |
| `is-arrayish` | 0.2.1 | Root Node tooling | puppeteer |
| `is-async-function` | 2.1.1 | StaffML frontend | eslint-config-next |
| `is-bigint` | 1.1.0 | StaffML frontend | eslint-config-next |
| `is-boolean-object` | 1.2.2 | StaffML frontend | eslint-config-next |
| `is-bun-module` | 2.0.0 | StaffML frontend | eslint-config-next |
| `is-callable` | 1.2.7 | StaffML frontend | eslint-config-next |
| `is-core-module` | 2.16.1 | StaffML frontend | eslint-config-next |
| `is-data-view` | 1.0.2 | StaffML frontend | eslint-config-next |
| `is-date-object` | 1.1.0 | StaffML frontend | eslint-config-next |
| `is-extglob` | 2.1.1 | StaffML frontend | eslint, eslint-config-next |
| `is-finalizationregistry` | 1.1.1 | StaffML frontend | eslint-config-next |
| `is-fullwidth-code-point` | 3.0.0 | Root Node tooling | puppeteer |
| `is-generator-function` | 1.1.2 | StaffML frontend | eslint-config-next |
| `is-glob` | 4.0.3 | StaffML frontend | eslint, eslint-config-next |
| `is-map` | 2.0.3 | StaffML frontend | eslint-config-next |
| `is-negative-zero` | 2.0.3 | StaffML frontend | eslint-config-next |
| `is-number` | 7.0.0 | SocratiQ, StaffML frontend | eslint-config-next, vite-plugin-singlefile |
| `is-number-object` | 1.1.1 | StaffML frontend | eslint-config-next |
| `is-potential-custom-element-name` | 1.0.1 | StaffML frontend | jsdom |
| `is-regex` | 1.2.1 | StaffML frontend | eslint-config-next |
| `is-set` | 2.0.3 | StaffML frontend | eslint-config-next |
| `is-shared-array-buffer` | 1.0.4 | StaffML frontend | eslint-config-next |
| `is-string` | 1.1.1 | StaffML frontend | eslint-config-next |
| `is-symbol` | 1.1.1 | StaffML frontend | eslint-config-next |
| `is-typed-array` | 1.1.15 | StaffML frontend | eslint-config-next |
| `is-weakmap` | 2.0.2 | StaffML frontend | eslint-config-next |
| `is-weakref` | 1.1.1 | StaffML frontend | eslint-config-next |
| `is-weakset` | 2.0.4 | StaffML frontend | eslint-config-next |
| `isarray` | 2.0.5 | StaffML frontend | eslint-config-next |
| `isexe` | 2.0.0 | StaffML frontend | eslint |
| `iterator.prototype` | 1.1.5 | StaffML frontend | eslint-config-next |
| `jiti` | 2.6.1 | StaffML frontend | @tailwindcss/postcss |
| `js-quantities` | 1.8.0 | StaffML frontend | js-quantities |
| `js-tokens` | 4.0.0 | Root Node tooling, StaffML frontend | eslint-config-next, puppeteer |
| `js-yaml` | 4.2.0 | Root Node tooling, StaffML frontend, design-grammar | js-yaml, puppeteer |
| `jsdom` | 29.1.1 | StaffML frontend | jsdom |
| `jsesc` | 3.1.0 | StaffML frontend | eslint-config-next |
| `json-buffer` | 3.0.1 | StaffML frontend | eslint |
| `json-parse-even-better-errors` | 2.3.1 | Root Node tooling | puppeteer |
| `json-schema-traverse` | 0.4.1 | StaffML frontend | eslint |
| `json-stable-stringify-without-jsonify` | 1.0.1 | StaffML frontend | eslint |
| `json5` | 1.0.2 | StaffML frontend | eslint-config-next |
| `jsonrepair` | 3.11.2 | SocratiQ | jsonrepair |
| `jspdf` | 4.2.1 | SocratiQ | jspdf |
| `jspdf-autotable` | 5.0.7 | SocratiQ | jspdf-autotable |
| `jsx-ast-utils` | 3.3.5 | StaffML frontend | eslint-config-next |
| `katex` | 0.16.45 | SocratiQ, StaffML frontend | ink-mde, katex, mermaid |
| `keyv` | 4.5.4 | StaffML frontend | eslint |
| `khroma` | 2.1.0 | SocratiQ | mermaid |
| `kleur` | 4.1.5 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `language-subtag-registry` | 0.3.23 | StaffML frontend | eslint-config-next |
| `language-tags` | 1.0.9 | StaffML frontend | eslint-config-next |
| `layout-base` | 2.0.1 | SocratiQ | mermaid |
| `levn` | 0.4.1 | StaffML frontend | eslint |
| `lightningcss` | 1.32.0 | SocratiQ, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, vite, vitest |
| `lightningcss-android-arm64` | 1.32.0 | not traced, see note | - |
| `lightningcss-darwin-arm64` | 1.32.0 | not traced, see note | - |
| `lightningcss-darwin-x64` | 1.32.0 | not traced, see note | - |
| `lightningcss-freebsd-x64` | 1.32.0 | not traced, see note | - |
| `lightningcss-linux-arm-gnueabihf` | 1.32.0 | not traced, see note | - |
| `lightningcss-linux-arm64-gnu` | 1.32.0 | not traced, see note | - |
| `lightningcss-linux-arm64-musl` | 1.32.0 | not traced, see note | - |
| `lightningcss-linux-x64-gnu` | 1.32.0 | not traced, see note | - |
| `lightningcss-linux-x64-musl` | 1.32.0 | not traced, see note | - |
| `lightningcss-win32-arm64-msvc` | 1.32.0 | not traced, see note | - |
| `lightningcss-win32-x64-msvc` | 1.32.0 | not traced, see note | - |
| `lines-and-columns` | 1.2.4 | Root Node tooling | puppeteer |
| `linkify-it` | 5.0.1 | SocratiQ | markdown-it |
| `locate-path` | 6.0.0 | StaffML frontend | eslint |
| `lodash-es` | 4.18.1 | SocratiQ | mermaid |
| `loose-envify` | 1.4.0 | StaffML frontend | eslint-config-next |
| `lru-cache` | 11.3.5 | Root Node tooling, StaffML frontend | eslint-config-next, jsdom, puppeteer |
| `lucide-react` | 1.16.0 | StaffML frontend | lucide-react |
| `lz-string` | 1.5.0 | not traced, see note | - |
| `magic-string` | 0.30.21 | StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, vitest |
| `markdown-it` | 14.2.0 | SocratiQ | markdown-it |
| `markdown-it-container` | 4.0.0 | SocratiQ | markdown-it-container |
| `marked` | 16.4.2 | SocratiQ | mermaid |
| `math-intrinsics` | 1.1.0 | StaffML frontend | eslint-config-next |
| `mdn-data` | 2.27.1 | StaffML frontend | jsdom |
| `mdurl` | 2.0.0 | SocratiQ | markdown-it |
| `merge2` | 1.4.1 | StaffML frontend | eslint-config-next |
| `mermaid` | 11.15.0 | SocratiQ | mermaid |
| `micromatch` | 4.0.8 | SocratiQ, StaffML frontend | eslint-config-next, vite-plugin-singlefile |
| `min-indent` | 1.0.1 | StaffML frontend | @testing-library/jest-dom |
| `miniflare` | 4.20260617.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `minimatch` | 3.1.5 | StaffML frontend | eslint, eslint-config-next |
| `minimist` | 1.2.8 | StaffML frontend | eslint-config-next |
| `mitt` | 3.0.1 | Root Node tooling | puppeteer |
| `mlly` | 1.8.2 | SocratiQ | mermaid |
| `motion-dom` | 12.38.0 | StaffML frontend | framer-motion |
| `motion-utils` | 12.36.0 | StaffML frontend | framer-motion |
| `ms` | 2.1.3 | Root Node tooling, StaffML frontend | eslint, eslint-config-next, puppeteer |
| `nanoid` | 3.3.16 | SocratiQ, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, next, postcss, vite, vitest |
| `napi-postinstall` | 0.3.4 | StaffML frontend | eslint-config-next |
| `natural-compare` | 1.4.0 | StaffML frontend | eslint, eslint-config-next |
| `netmask` | 2.1.1 | Root Node tooling | puppeteer |
| `next` | 16.2.11 | StaffML frontend | next |
| `node-exports-info` | 1.6.0 | StaffML frontend | eslint-config-next |
| `node-releases` | 2.0.36 | StaffML frontend | autoprefixer, eslint-config-next |
| `object-assign` | 4.1.1 | StaffML frontend | eslint-config-next |
| `object-inspect` | 1.13.4 | StaffML frontend | eslint-config-next |
| `object-keys` | 1.1.1 | StaffML frontend | eslint-config-next |
| `object.assign` | 4.1.7 | StaffML frontend | eslint-config-next |
| `object.entries` | 1.1.9 | StaffML frontend | eslint-config-next |
| `object.fromentries` | 2.0.8 | StaffML frontend | eslint-config-next |
| `object.groupby` | 1.0.3 | StaffML frontend | eslint-config-next |
| `object.values` | 1.2.1 | StaffML frontend | eslint-config-next |
| `obug` | 2.1.1 | StaffML frontend, StaffML vault Worker | vitest |
| `once` | 1.4.0 | Root Node tooling | puppeteer |
| `optionator` | 0.9.4 | StaffML frontend | eslint |
| `own-keys` | 1.0.1 | StaffML frontend | eslint-config-next |
| `p-limit` | 3.1.0 | StaffML frontend | eslint |
| `p-locate` | 5.0.0 | StaffML frontend | eslint |
| `pac-proxy-agent` | 7.2.0 | Root Node tooling | puppeteer |
| `pac-resolver` | 7.0.1 | Root Node tooling | puppeteer |
| `package-manager-detector` | 1.6.0 | SocratiQ | mermaid |
| `pako` | 2.1.0 | SocratiQ | jspdf |
| `parent-module` | 1.0.1 | Root Node tooling | puppeteer |
| `parse-json` | 5.2.0 | Root Node tooling | puppeteer |
| `parse5` | 8.0.1 | StaffML frontend | jsdom |
| `path-data-parser` | 0.1.0 | SocratiQ | mermaid |
| `path-exists` | 4.0.0 | StaffML frontend | eslint |
| `path-key` | 3.1.1 | StaffML frontend | eslint |
| `path-parse` | 1.0.7 | StaffML frontend | eslint-config-next |
| `path-to-regexp` | 6.3.0 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `pathe` | 2.0.3 | SocratiQ, StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | mermaid, vitest, wrangler |
| `pend` | 1.2.0 | Root Node tooling | puppeteer |
| `performance-now` | 2.1.0 | not traced, see note | - |
| `picocolors` | 1.1.1 | Root Node tooling, SocratiQ, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, @testing-library/jest-dom, autoprefixer, eslint-config-next, next, postcss, puppeteer, vite, vitest |
| `picomatch` | 2.3.2 | SocratiQ, StaffML frontend, StaffML vault Worker | eslint-config-next, vite, vite-plugin-singlefile, vitest |
| `pkg-types` | 1.3.1 | SocratiQ | mermaid |
| `playwright` | 1.58.2 | MLPerf EDU, Release smoke tests, StaffML frontend, TinyTorch community dashboard tests | @playwright/test, playwright |
| `playwright-core` | 1.58.2 | MLPerf EDU, Release smoke tests, StaffML frontend, TinyTorch community dashboard tests | @playwright/test, playwright |
| `points-on-curve` | 0.2.0 | SocratiQ | mermaid |
| `points-on-path` | 0.2.1 | SocratiQ | mermaid |
| `possible-typed-array-names` | 1.1.0 | StaffML frontend | eslint-config-next |
| `postcss` | 8.5.25 | SocratiQ, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, next, postcss, vite, vitest |
| `postcss-value-parser` | 4.2.0 | StaffML frontend | autoprefixer |
| `prelude-ls` | 1.2.1 | StaffML frontend | eslint |
| `pretty-format` | 27.5.1 | not traced, see note | - |
| `progress` | 2.0.3 | Root Node tooling | puppeteer |
| `prop-types` | 15.8.1 | StaffML frontend | eslint-config-next |
| `proxy-agent` | 6.5.0 | Root Node tooling | puppeteer |
| `proxy-from-env` | 1.1.0 | Root Node tooling | puppeteer |
| `pump` | 3.0.4 | Root Node tooling | puppeteer |
| `punycode` | 2.3.1 | StaffML frontend | eslint, jsdom |
| `punycode.js` | 2.3.1 | SocratiQ | markdown-it |
| `puppeteer` | 24.42.0 | Root Node tooling | puppeteer |
| `puppeteer-core` | 24.42.0 | Root Node tooling | puppeteer |
| `queue-microtask` | 1.2.3 | StaffML frontend | eslint-config-next |
| `raf` | 3.4.1 | not traced, see note | - |
| `react` | 19.2.6 | StaffML frontend | react |
| `react-dom` | 19.2.6 | StaffML frontend | react-dom |
| `react-is` | 17.0.2 | StaffML frontend | eslint-config-next |
| `react-medium-image-zoom` | 5.4.5 | StaffML frontend | react-medium-image-zoom |
| `readdirp` | 4.1.2 | SocratiQ | chokidar |
| `redent` | 3.0.0 | StaffML frontend | @testing-library/jest-dom |
| `reflect.getprototypeof` | 1.0.10 | StaffML frontend | eslint-config-next |
| `regenerator-runtime` | 0.13.11 | not traced, see note | - |
| `regexp.prototype.flags` | 1.5.4 | StaffML frontend | eslint-config-next |
| `require-directory` | 2.1.1 | Root Node tooling | puppeteer |
| `require-from-string` | 2.0.2 | StaffML frontend | jsdom |
| `resolve` | 2.0.0-next.6 | StaffML frontend | eslint-config-next |
| `resolve-from` | 4.0.0 | Root Node tooling | puppeteer |
| `resolve-pkg-maps` | 1.0.0 | StaffML frontend | eslint-config-next |
| `reusify` | 1.1.0 | StaffML frontend | eslint-config-next |
| `rgbcolor` | 1.0.1 | not traced, see note | - |
| `robust-predicates` | 3.0.2 | SocratiQ | d3, mermaid |
| `rolldown` | 1.0.3 | SocratiQ, StaffML frontend, StaffML vault Worker | vite, vitest |
| `roughjs` | 4.6.6 | SocratiQ | mermaid |
| `run-parallel` | 1.2.0 | StaffML frontend | eslint-config-next |
| `rw` | 1.3.3 | SocratiQ | d3, mermaid |
| `safe-array-concat` | 1.1.4 | StaffML frontend | eslint-config-next |
| `safe-push-apply` | 1.0.0 | StaffML frontend | eslint-config-next |
| `safe-regex-test` | 1.1.0 | StaffML frontend | eslint-config-next |
| `safer-buffer` | 2.1.2 | SocratiQ | d3, mermaid |
| `saxes` | 6.0.0 | StaffML frontend | jsdom |
| `scheduler` | 0.27.0 | StaffML frontend | react-dom |
| `semver` | 7.8.5 | Root Node tooling, StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | eslint-config-next, puppeteer, wrangler |
| `seroval` | 1.5.6 | SocratiQ | ink-mde |
| `seroval-plugins` | 1.5.2 | SocratiQ | ink-mde |
| `set-function-length` | 1.2.2 | StaffML frontend | eslint-config-next |
| `set-function-name` | 2.0.2 | StaffML frontend | eslint-config-next |
| `set-proto` | 1.0.0 | StaffML frontend | eslint-config-next |
| `sharp` | 0.34.5 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `shebang-command` | 2.0.0 | StaffML frontend | eslint |
| `shebang-regex` | 3.0.0 | StaffML frontend | eslint |
| `side-channel` | 1.1.0 | StaffML frontend | eslint-config-next |
| `side-channel-list` | 1.0.1 | StaffML frontend | eslint-config-next |
| `side-channel-map` | 1.0.1 | StaffML frontend | eslint-config-next |
| `side-channel-weakmap` | 1.0.2 | StaffML frontend | eslint-config-next |
| `siginfo` | 2.0.0 | StaffML frontend, StaffML vault Worker | vitest |
| `sigma` | 3.0.3 | StaffML frontend | sigma |
| `smart-buffer` | 4.2.0 | Root Node tooling | puppeteer |
| `socks` | 2.8.7 | Root Node tooling | puppeteer |
| `socks-proxy-agent` | 8.0.5 | Root Node tooling | puppeteer |
| `solid-js` | 1.9.12 | SocratiQ | ink-mde |
| `source-map` | 0.6.1 | not traced, see note | - |
| `source-map-js` | 1.2.1 | SocratiQ, StaffML frontend, StaffML vault Worker | @tailwindcss/postcss, jsdom, next, postcss, vite, vitest |
| `stable-hash` | 0.0.5 | StaffML frontend | eslint-config-next |
| `stackback` | 0.0.2 | StaffML frontend, StaffML vault Worker | vitest |
| `stackblur-canvas` | 2.7.0 | not traced, see note | - |
| `std-env` | 4.0.0 | StaffML frontend, StaffML vault Worker | vitest |
| `stop-iteration-iterator` | 1.1.0 | StaffML frontend | eslint-config-next |
| `streamx` | 2.25.0 | Root Node tooling | puppeteer |
| `string-width` | 4.2.3 | Root Node tooling | puppeteer |
| `string.prototype.includes` | 2.0.1 | StaffML frontend | eslint-config-next |
| `string.prototype.matchall` | 4.0.12 | StaffML frontend | eslint-config-next |
| `string.prototype.repeat` | 1.0.0 | StaffML frontend | eslint-config-next |
| `string.prototype.trim` | 1.2.10 | StaffML frontend | eslint-config-next |
| `string.prototype.trimend` | 1.0.9 | StaffML frontend | eslint-config-next |
| `string.prototype.trimstart` | 1.0.8 | StaffML frontend | eslint-config-next |
| `strip-ansi` | 6.0.1 | Root Node tooling | puppeteer |
| `strip-bom` | 3.0.0 | StaffML frontend | eslint-config-next |
| `strip-indent` | 3.0.0 | StaffML frontend | @testing-library/jest-dom |
| `style-mod` | 4.1.2 | SocratiQ | ink-mde |
| `styled-jsx` | 5.1.6 | StaffML frontend | next |
| `stylis` | 4.3.6 | SocratiQ | mermaid |
| `suffix-thumb` | 5.0.2 | SocratiQ | compromise |
| `supports-color` | 10.2.2 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `supports-preserve-symlinks-flag` | 1.0.0 | StaffML frontend | eslint-config-next |
| `svg-pathdata` | 6.0.3 | not traced, see note | - |
| `symbol-tree` | 3.2.4 | StaffML frontend | jsdom |
| `tailwindcss` | 4.3.0 | StaffML frontend | @tailwindcss/postcss, tailwindcss |
| `tapable` | 2.3.3 | StaffML frontend | @tailwindcss/postcss |
| `tar-fs` | 3.1.2 | Root Node tooling | puppeteer |
| `tar-stream` | 3.1.8 | Root Node tooling | puppeteer |
| `teex` | 1.0.1 | Root Node tooling | puppeteer |
| `text-decoder` | 1.2.7 | Root Node tooling | puppeteer |
| `text-segmentation` | 1.0.3 | not traced, see note | - |
| `tinybench` | 2.9.0 | StaffML frontend, StaffML vault Worker | vitest |
| `tinyexec` | 1.1.1 | SocratiQ, StaffML frontend, StaffML vault Worker | mermaid, vitest |
| `tinyglobby` | 0.2.17 | SocratiQ, StaffML frontend, StaffML vault Worker | eslint-config-next, vite, vitest |
| `tinyrainbow` | 3.1.0 | StaffML frontend, StaffML vault Worker | vitest |
| `tldts` | 7.0.28 | StaffML frontend | jsdom |
| `tldts-core` | 7.0.28 | StaffML frontend | jsdom |
| `to-regex-range` | 5.0.1 | SocratiQ, StaffML frontend | eslint-config-next, vite-plugin-singlefile |
| `tough-cookie` | 6.0.1 | StaffML frontend | jsdom |
| `tr46` | 6.0.0 | StaffML frontend | jsdom |
| `ts-api-utils` | 2.5.0 | StaffML frontend | eslint-config-next |
| `ts-dedent` | 2.2.0 | SocratiQ | mermaid |
| `tsconfig-paths` | 3.15.0 | StaffML frontend | eslint-config-next |
| `tslib` | 2.8.1 | Root Node tooling, StaffML frontend | framer-motion, next, puppeteer |
| `type-check` | 0.4.0 | StaffML frontend | eslint |
| `typed-array-buffer` | 1.0.3 | StaffML frontend | eslint-config-next |
| `typed-array-byte-length` | 1.0.3 | StaffML frontend | eslint-config-next |
| `typed-array-byte-offset` | 1.0.4 | StaffML frontend | eslint-config-next |
| `typed-array-length` | 1.0.7 | StaffML frontend | eslint-config-next |
| `typed-query-selector` | 2.12.2 | Root Node tooling | puppeteer |
| `typescript` | 6.0.3 | StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | typescript |
| `typescript-eslint` | 8.59.0 | StaffML frontend | eslint-config-next |
| `uc.micro` | 2.1.0 | SocratiQ | markdown-it |
| `ufo` | 1.6.3 | SocratiQ | mermaid |
| `unbox-primitive` | 1.1.0 | StaffML frontend | eslint-config-next |
| `undici` | 7.28.0 | StaffML AI interviewer Worker, StaffML frontend, StaffML vault Worker | jsdom, wrangler |
| `undici-types` | 7.24.6 | not traced, see note | - |
| `unenv` | 2.0.0-rc.24 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `unrs-resolver` | 1.11.1 | StaffML frontend | eslint-config-next |
| `update-browserslist-db` | 1.2.3 | StaffML frontend | autoprefixer, eslint-config-next |
| `uri-js` | 4.4.1 | StaffML frontend | eslint |
| `utrie` | 1.0.2 | not traced, see note | - |
| `uuid` | 14.0.0 | SocratiQ | mermaid |
| `vite` | 8.0.16 | SocratiQ, StaffML frontend, StaffML vault Worker | vite, vitest |
| `vite-plugin-singlefile` | 2.3.3 | SocratiQ | vite-plugin-singlefile |
| `vitest` | 4.1.7 | StaffML frontend, StaffML vault Worker | vitest |
| `w3c-keyname` | 2.2.8 | SocratiQ | ink-mde |
| `w3c-xmlserializer` | 5.0.0 | StaffML frontend | jsdom |
| `webdriver-bidi-protocol` | 0.4.1 | Root Node tooling | puppeteer |
| `webidl-conversions` | 8.0.1 | StaffML frontend | jsdom |
| `whatwg-mimetype` | 5.0.0 | StaffML frontend | jsdom |
| `whatwg-url` | 16.0.1 | StaffML frontend | jsdom |
| `which` | 2.0.2 | StaffML frontend | eslint |
| `which-boxed-primitive` | 1.1.1 | StaffML frontend | eslint-config-next |
| `which-builtin-type` | 1.2.1 | StaffML frontend | eslint-config-next |
| `which-collection` | 1.0.2 | StaffML frontend | eslint-config-next |
| `which-typed-array` | 1.1.20 | StaffML frontend | eslint-config-next |
| `why-is-node-running` | 2.3.0 | StaffML frontend, StaffML vault Worker | vitest |
| `word-wrap` | 1.2.5 | StaffML frontend | eslint |
| `workerd` | 1.20260617.1 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `wrangler` | 4.103.0 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `wrap-ansi` | 7.0.0 | Root Node tooling | puppeteer |
| `wrappy` | 1.0.2 | Root Node tooling | puppeteer |
| `ws` | 8.21.0 | Root Node tooling, StaffML AI interviewer Worker, StaffML vault Worker | puppeteer, wrangler |
| `xml-name-validator` | 5.0.0 | StaffML frontend | jsdom |
| `xmlchars` | 2.2.0 | StaffML frontend | jsdom |
| `y18n` | 5.0.8 | Root Node tooling | puppeteer |
| `yallist` | 3.1.1 | StaffML frontend | eslint-config-next |
| `yargs` | 17.7.2 | Root Node tooling | puppeteer |
| `yargs-parser` | 21.1.1 | Root Node tooling | puppeteer |
| `yauzl` | 2.10.0 | Root Node tooling | puppeteer |
| `yocto-queue` | 0.1.0 | StaffML frontend | eslint |
| `youch` | 4.1.0-beta.10 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `youch-core` | 0.3.3 | StaffML AI interviewer Worker, StaffML vault Worker | wrangler |
| `zod` | 4.3.6 | Root Node tooling, StaffML frontend | eslint-config-next, puppeteer |
| `zod-validation-error` | 4.0.2 | StaffML frontend | eslint-config-next |
</details>

## GitHub Actions, 22 unique

Attributed differently from the two tables above, directly grepped against every real workflow file for a matching `uses:` line, not traced through the dependency graph, so every row here is a confirmed, not inferred, usage.

<details>
<summary>Click to expand the full GitHub Actions table</summary>

| Action | Version | Used in workflow(s) |
|---|---|---|
| `actions/cache` | 4.*.* | not directly matched, likely called from a reusable workflow |
| `actions/checkout` | 6.*.* | `all-contributors-add.yml`, `all-contributors-auto-credit.yml`, `book-build-container.yml`, `book-preview-dev.yml`, `book-publish-live.yml`, `book-validate-dev.yml`, `ci-sanity.yml`, `codespell.yml`, `infra-build-sitemap.yml`, `infra-cleanup-caches.yml`, `infra-cloudflare-redirects.yml`, `infra-container-linux.yml`, `infra-container-windows.yml`, `infra-health-check.yml`, `infra-link-check.yml`, `infra-link-rot-nightly.yml`, `infra-visual-smoke.yml`, `instructors-preview-dev.yml`, `instructors-publish-live.yml`, `instructors-validate-dev.yml`, `kits-build-pdfs.yml`, `kits-preview-dev.yml`, `kits-publish-live.yml`, `kits-validate-dev.yml`, `labs-preview-dev.yml`, `labs-publish-live.yml`, `labs-validate-dev.yml`, `mlperf-edu-preview-dev.yml`, `mlperf-edu-publish-live.yml`, `mlperf-edu-release-validation.yml`, `mlperf-edu-validate-dev.yml`, `mlsysim-build-pdfs.yml`, `mlsysim-preview-dev.yml`, `mlsysim-publish-live.yml`, `mlsysim-pypi-publish.yml`, `mlsysim-update-pdfs.yml`, `mlsysim-validate-dev.yml`, `site-preview-dev.yml`, `site-publish-live.yml`, `site-refresh-stats.yml`, `site-validate-dev.yml`, `slides-build-pdfs.yml`, `slides-preview-dev.yml`, `slides-publish-live.yml`, `slides-validate-dev.yml`, `socratiq-bundle-drift.yml`, `staffml-audit-corpus-monthly.yml`, `staffml-auto-pr.yml`, `staffml-chain-rebuild.yml`, `staffml-preview-dev.yml`, `staffml-publish-live.yml`, `staffml-update-paper.yml`, `staffml-validate-dev.yml`, `staffml-validate-vault.yml`, `sync-newsletter.yml`, `tinytorch-build-pdfs.yml`, `tinytorch-preview-dev.yml`, `tinytorch-publish-live.yml`, `tinytorch-update-pdfs.yml`, `tinytorch-validate-dev.yml`, `update-contributors.yml`, `_release-prepare.yml` |
| `actions/download-artifact` | 8.*.* | `book-preview-dev.yml`, `book-publish-live.yml`, `book-validate-dev.yml`, `infra-visual-smoke.yml`, `kits-preview-dev.yml`, `kits-publish-live.yml`, `mlsysim-preview-dev.yml`, `mlsysim-publish-live.yml`, `mlsysim-pypi-publish.yml`, `mlsysim-update-pdfs.yml`, `slides-publish-live.yml`, `staffml-preview-dev.yml`, `staffml-update-paper.yml`, `tinytorch-preview-dev.yml`, `tinytorch-publish-live.yml`, `tinytorch-update-pdfs.yml` |
| `actions/github-script` | 9.*.* | `all-contributors-add.yml`, `all-contributors-auto-credit.yml`, `auto-label.yml`, `book-build-container.yml`, `staffml-welcome.yml` |
| `actions/setup-java` | 5.*.* | `book-validate-dev.yml` |
| `actions/setup-node` | 6.*.* | `labs-validate-dev.yml`, `socratiq-bundle-drift.yml`, `staffml-preview-dev.yml`, `staffml-publish-live.yml`, `staffml-validate-dev.yml`, `staffml-validate-vault.yml`, `tinytorch-build-pdfs.yml`, `tinytorch-update-pdfs.yml` |
| `actions/setup-python` | 6.*.* | `all-contributors-add.yml`, `all-contributors-auto-credit.yml`, `book-publish-live.yml`, `book-validate-dev.yml`, `ci-sanity.yml`, `codespell.yml`, `infra-build-sitemap.yml`, `infra-cleanup-caches.yml`, `infra-visual-smoke.yml`, `instructors-publish-live.yml`, `kits-publish-live.yml`, `labs-preview-dev.yml`, `labs-publish-live.yml`, `labs-validate-dev.yml`, `mlsysim-preview-dev.yml`, `mlsysim-publish-live.yml`, `mlsysim-pypi-publish.yml`, `mlsysim-validate-dev.yml`, `site-preview-dev.yml`, `site-publish-live.yml`, `site-refresh-stats.yml`, `site-validate-dev.yml`, `slides-build-pdfs.yml`, `slides-publish-live.yml`, `staffml-audit-corpus-monthly.yml`, `staffml-auto-pr.yml`, `staffml-chain-rebuild.yml`, `staffml-preview-dev.yml`, `staffml-publish-live.yml`, `staffml-update-paper.yml`, `staffml-validate-dev.yml`, `staffml-validate-vault.yml`, `sync-newsletter.yml`, `tinytorch-publish-live.yml`, `tinytorch-validate-dev.yml`, `update-contributors.yml`, `_release-prepare.yml` |
| `actions/upload-artifact` | 7.*.* | `book-build-container.yml`, `book-publish-live.yml`, `book-validate-dev.yml`, `infra-health-check.yml`, `infra-visual-smoke.yml`, `kits-build-pdfs.yml`, `labs-validate-dev.yml`, `mlperf-edu-release-validation.yml`, `mlperf-edu-validate-dev.yml`, `mlsysim-build-pdfs.yml`, `mlsysim-pypi-publish.yml`, `slides-build-pdfs.yml`, `slides-publish-live.yml`, `staffml-audit-corpus-monthly.yml`, `staffml-preview-dev.yml`, `staffml-update-paper.yml`, `staffml-validate-vault.yml`, `tinytorch-build-pdfs.yml`, `tinytorch-preview-dev.yml`, `tinytorch-update-pdfs.yml` |
| `ai-action/ollama-action` | 2.*.* | `all-contributors-add.yml`, `all-contributors-auto-credit.yml`, `auto-label.yml` |
| `astral-sh/setup-uv` | 08807647e7069bb48b6ef5acd8ec9567f424441b | `mlperf-edu-preview-dev.yml`, `mlperf-edu-publish-live.yml`, `mlperf-edu-release-validation.yml`, `mlperf-edu-validate-dev.yml` |
| `docker/build-push-action` | 7.*.* | `infra-container-linux.yml` |
| `docker/login-action` | 4.*.* | `book-build-container.yml`, `infra-container-linux.yml`, `infra-container-windows.yml`, `infra-health-check.yml` |
| `docker/metadata-action` | 6.*.* | `infra-container-linux.yml`, `infra-container-windows.yml` |
| `docker/setup-buildx-action` | 4.*.* | `infra-container-linux.yml`, `infra-container-windows.yml` |
| `lycheeverse/lychee-action` | 2.8.0 | `infra-link-check.yml` |
| `peaceiris/actions-gh-pages` | 4.*.* | `instructors-publish-live.yml`, `kits-publish-live.yml`, `labs-publish-live.yml`, `mlsysim-publish-live.yml`, `slides-publish-live.yml`, `staffml-publish-live.yml` |
| `pypa/gh-action-pypi-publish` | release/v1 | `mlsysim-pypi-publish.yml` |
| `quarto-dev/quarto-actions/setup` | 2.*.* | `book-publish-live.yml`, `instructors-preview-dev.yml`, `instructors-publish-live.yml`, `instructors-validate-dev.yml`, `kits-preview-dev.yml`, `kits-publish-live.yml`, `kits-validate-dev.yml`, `labs-preview-dev.yml`, `labs-publish-live.yml`, `labs-validate-dev.yml`, `mlperf-edu-preview-dev.yml`, `mlperf-edu-publish-live.yml`, `mlperf-edu-validate-dev.yml`, `mlsysim-preview-dev.yml`, `mlsysim-publish-live.yml`, `mlsysim-validate-dev.yml`, `site-preview-dev.yml`, `site-publish-live.yml`, `site-validate-dev.yml`, `slides-preview-dev.yml`, `slides-publish-live.yml`, `slides-validate-dev.yml`, `tinytorch-build-pdfs.yml`, `tinytorch-preview-dev.yml`, `tinytorch-publish-live.yml`, `tinytorch-update-pdfs.yml` |
| `r-lib/actions/setup-r` | 2.*.* | not directly matched, likely called from a reusable workflow |
| `softprops/action-gh-release` | 3.*.* | `mlsysim-pypi-publish.yml`, `slides-publish-live.yml` |
| `xu-cheng/latex-action` | 4.*.* | `staffml-update-paper.yml`, `tinytorch-build-pdfs.yml`, `tinytorch-update-pdfs.yml` |
| `zauguin/install-texlive` | 4.*.* | not directly matched, likely called from a reusable workflow |
</details>
