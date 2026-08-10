# Stats

*A quick-reference cheat sheet of hard numbers for every sub-project: content counts, dependency counts, CI counts, whatever a contributor would otherwise have to go re-derive from source every time. Every number here was counted directly against the repo (file counts, YAML entries, manifest contents), not estimated, and is dated so staleness is visible rather than silent. If a number looks off, it probably drifted since the date below, not a guess.*

*Counted against: local clone of `dev`, and the live GitHub API, both as of 2026-08-10.*

## Repo-wide

| Stat | Value |
|---|---|
| Stars | 27,862 |
| Forks | 3,507 |
| Merged PRs (all-time) | 1,187 |
| Distinct contributors | 88 |
| Sub-projects | 11 |
| GitHub Actions workflows | 67 |
| Total resolved dependencies (direct + transitive) | 1,629 |
| ...of which npm | 1,188 |
| ...of which PyPI | 415 |
| ...of which GitHub Actions | 25 |
| ...of which git submodule/other | 1 |
| Manifest files tracked for dependencies | 117 |
| Pre-commit checks (root `.pre-commit-config.yaml`) | ~60 |

See [`pr-history.md`](pr-history.md) for the full PR breakdown and [`dependency-map.md`](dependency-map.md)/[`packages.md`](packages.md) for the full dependency breakdown, this table just indexes into those.

## Book

| Stat | Value |
|---|---|
| Volumes | 2 |
| Volume I chapters | 16 |
| Volume II chapters | 17 |
| Total chapters | 33 |
| Output formats | 3 (HTML, PDF, EPUB) |
| Direct Python dependencies (Binder CLI, root `pyproject.toml`) | 14 |
| Validation check groups (`ValidateCommand.GROUPS`) | 7 (`refs`, `labels`, `headers`, `bib`, `footnotes`, `figures`, `prose`) |
| PDF Lua filters (`filters.yml`) | includes `fallacy-pitfall.lua` (added 2026-08-10, see [`system_design.md`](book/system_design.md)) |

## TinyTorch

| Stat | Value |
|---|---|
| Modules | 20 (`01_tensor` through `20_capstone`) |
| Direct Python dependencies | 5 (`numpy`, `rich`, `PyYAML`, `certifi`, `pytest`) + 6 dev-only (`nbdev`, `jupytext`, `nbformat`, `jupyter`, `jupyterlab`, `ipykernel`) |
| CLI | `tito` |
| Milestone tracking table | `MILESTONE_SCRIPTS`, canonical milestone-to-module map |

## MLSys·im

| Stat | Value |
|---|---|
| Typed registries | 9 (`datasets`, `hardware`, `infrastructure`, `literature`, `models`, `ops`, `platforms`, `reference_stats`, `systems`) |
| Direct Python dependencies | 6 (`pint`, `pydantic`, `numpy`, `typer`, `rich`, `pyyaml`) + 5 optional (`scipy`, `ortools`, `plotly`/`matplotlib`, `mcp`, `pandas`) |
| Powers | Labs (as the simulation engine) |
| License | Apache-2.0 (relicensed at v0.1.0, PR #1521) |

## Labs

| Stat | Value |
|---|---|
| Total labs | 34 (17 Volume I + 17 Volume II) |
| Test tiers | 5 (`test_static.py`, `test_engine.py`, `test_widget.py`, `test_protocol.py`, `browser_smoke.py` + `test_wasm_persistence.py`) |
| Direct Python dependencies | 4 (`marimo`, `plotly`, `numpy`, `pytest`) |
| Runtime | marimo notebooks, exported to WASM via Pyodide for the browser |
| Persistence | `DesignLedger`, native JSON (`~/.mlsys/ledger.json`) or WASM (IndexedDB via JS bridge), see [`labs/system_design.md`](labs/system_design.md) section 7 for the 2026-08-10 save-path fix |

## Kits

| Stat | Value |
|---|---|
| Board families | 3 (Arduino, Raspberry Pi, Seeed) |
| Arduino targets | 1 (Nicla Vision) |
| Raspberry Pi project types | 4 (image classification, LLM, object detection, VLM) |
| Seeed boards | 2 (Grove Vision AI V2, XIAO ESP32S3) |
| Dependency manifest | none, content-and-Quarto-config only |

## StaffML

| Stat | Value |
|---|---|
| Questions in corpus | 10,711 |
| Tracks | 5 (`cloud`, `edge`, `global`, `mobile`, `tinyml`) |
| CI workflows | 7 (the most of any sub-project) |
| Frontend direct dependencies | 5 (`next`, `react`/`react-dom`, `sigma`/`graphology`/`@react-sigma/core`, `katex`, `js-quantities`) + 3 dev-only (`vitest`, `@playwright/test`, `js-yaml`) |
| vault-cli direct dependencies | 5 (`typer`, `pydantic`, `pyyaml`, `click`, `rich`); LinkML is documentation-only, not an actual codegen dependency |
| Cloudflare Workers | 2 (vault Worker, AI interviewer Worker), both zero runtime npm dependencies |
| Shared types package | 1 (`staffml-vault-types`), zero dependencies of any kind |
| Service worker cache sections | 2, disjoint by design (`staffml-vault-` and `staffml-app-`, the latter added 2026-08-10 for PWA support, see [`system_design.md`](staffml/system_design.md) section 8) |

## MLPerf EDU

| Stat | Value |
|---|---|
| Workloads | 14 |
| Domains | 7 (graph, language, recommendation, reinforcement, timeseries, tiny, vision) |
| Workloads per domain | graph 1, language 5, recommendation 1, reinforcement 1, timeseries 1, tiny 3, vision 2 |
| Difficulty profiles | 3 (`min`, `max`, `pro`, where `pro` reuses the `max` runner) |
| Known unfixed CI gap | missing `matplotlib` test dependency, confirmed still open as of 2026-08-10, see [`ci-workflows.md`](mlperf-edu/ci-workflows.md) |

## design-grammar

| Stat | Value |
|---|---|
| Primitives (`grammar.yml`) | 90 |
| Abstraction layers | 8 |
| Information-processing roles | 5 (Represent, Compute, Communicate, Control, Measure) |
| Rewrite-rule constraints (`rewrite-rules.yml`) | 8 (capacity, bandwidth, latency, utilization, fragmentation, energy, reliability, cost) |
| Direct dependencies | 1 (`js-yaml`) |
| CI validation | none, `npm run validate` exists but nothing in CI runs it |

## Slides

| Stat | Value |
|---|---|
| Total decks | 35 |
| Volume I decks | 17 (16 chapters + 1 course-overview) |
| Volume II decks | 18 (17 chapters + 1 course-overview) |
| Build chain | SVG to PDF to PPTX |
| Compile engine | `pdflatex`, not `xelatex` |

## Instructors

| Stat | Value |
|---|---|
| Custom code files | 0 |
| Content type | pure Quarto, plus one `check-links.sh` shell script |

## Site

| Stat | Value |
|---|---|
| Mini-games | 14 |
| Newsletter CLI direct dependencies | 3 (`rich`, `requests`, `python-frontmatter`) + 2 dev-only (`pytest`, `ruff`) |
| Covers | home, about, community, newsletter, mini-games |

---

*If you're updating one of these numbers after a real change lands (a new lab, a new workload, a new module), update this file in the same pass, this doc is only useful if it stays current, a stale cheat sheet is worse than no cheat sheet.*
