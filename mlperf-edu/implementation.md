# MLPerf EDU: Implementation Reference

> **Status: as-built, contributor-facing.** MLPerf EDU is a live, already-implemented benchmark specification, CLI, and evidence pipeline, with an honestly-tracked partial portfolio (see the design doc's "Known issues"). This document is your map for reading and modifying the real source: file paths and representative code pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| The CLI or harness (`src/mlperf/`) | Python 3.10 or newer, and `uv` (the project's primary supported package manager). `uv sync --locked --extra dev` from `mlperf-edu/`. |
| Running an actual workload | The above, plus enough local disk and memory for that workload's dataset and model; check `INSTALL.md` and the workload's own entry in `registry/suites/` before attempting a large one. |
| The research paper | A LaTeX distribution. |
| The documentation site | Quarto. |
| The conformance/parity checks | The `parity` extra (`ai-edge-litert`), installed via `uv sync --extra parity`. |

## Repository layout

```
cs249r_book/
  mlperf-edu/
    pyproject.toml               # name=mlperf-edu; [project.scripts] mlperf / mlperf-edu -> mlperf.edu_cli:main
    README.md, NORTH_STAR.md, PROPOSAL.md, DESIGN_PHILOSOPHY.md, PUBLIC_RULES.md
    INSTALL.md, LOCAL_EXECUTION_PLAN.md, READINESS.md, INDEPENDENT_AUDIT.md
    QUALITY_TARGET_REVIEW.md, DATASET_RELEASE_REVIEW.md
    datasets.yaml                  # Dataset catalog: license/release status per dataset
    src/
      mlperf/                       # The real implementation package
        edu_cli.py                   # The actual CLI (argparse), ~2500+ lines
        registry.py                   # Workload class, loaders, validators
        harness.py                     # ScenarioConfig, HarnessSample/Response/RunResult, run_harness()
        contracts.py                    # Report/promotion contract evaluation
        assets.py, assignment.py, experiment.py, fingerprint.py,
        manifest.py, power.py, roofline.py, sut.py, loadgen.py
        reference/
          cloud/, timeseries/patchtst/, tiny/    # Per-workload reference implementations
        runners/
          vision.py, text.py, graph.py, timeseries.py, recommendation.py,
          reinforcement.py, retrieval.py, tiny.py, nanogpt.py,
          code_generation.py, function_calling.py, functional_setup.py,
          image_generation.py, common.py
      mlperf_edu/                    # Thin compatibility mirror
        cli.py                        # 5 lines: from mlperf.edu_cli import main
        harness.py, loadgen.py, power.py, registry.py, __main__.py
    registry/
      suites.yaml, selection-ledger.yaml
      suites/{graph,language,recommendation,reinforcement,timeseries,tiny,vision}/
    bench/
      measure_peaks.py               # Host FLOPS/bandwidth measurement (not the benchmark engine)
    examples/
      01-health-check/ .. 05-assignment-package/
      02-inference-tradeoff/plan.yaml, 03-training-tradeoff/plan.yaml
      05-assignment-package/assignment.yaml
      lab1_optimization.py, lab2_inference_sut.py, lab3_arch_comparison.py  # standalone, non-canonical
    conformance_results/           # Ad hoc conformance/parity audit JSON
    provisional_results/            # Draft v0.1 evidence snapshot (not the strict public index)
    reference_results/               # Future home of the strict, promoted public index
    paper/
      paper.tex, refs.bib, generated_registry.tex
      generate_registry_snapshot.py, check_paper_pdf.py, evidence_snapshot.json
    tests/                            # 34 pytest files
  .github/workflows/
    mlperf-edu-validate-dev.yml       # Fast, blocking PR/dev gate
    mlperf-edu-release-validation.yml  # Slow, evidence-bearing full benchmark run (5h limit)
    mlperf-edu-preview-dev.yml
    mlperf-edu-publish-live.yml        # Docs-only publish; no package, no benchmark release
```

---

## 1. The CLI (`src/mlperf/edu_cli.py`)

### 1.1 Entry points

`pyproject.toml`:

```toml
[project.scripts]
mlperf = "mlperf.edu_cli:main"
mlperf-edu = "mlperf.edu_cli:main"
```

Both console-script names resolve to the same `main()`. `src/mlperf_edu/cli.py` is a five-line compatibility shim (`from mlperf.edu_cli import main; raise SystemExit(main())`); the real implementation is entirely in `src/mlperf/edu_cli.py`.

### 1.2 Command reference

Built with `argparse`, subparsers defined in `build_parser()`. The parser's own epilog documents two audiences:

| Command | Audience | What it does |
|---|---|---|
| `doctor` | user | Checks local environment/registry health for a workload, suite, or collection. |
| `init` | user | Prepares local caches for a profile; can smoke-test setup. |
| `health` | user | Runs every registered `min`-profile path across the whole suite, verifies provenance manifests, writes a JSON/CSV/HTML suite report. |
| `fetch` | user | Downloads and verifies assets for selected workloads. |
| `run` | user | Runs a workload, suite, or profile. Supports `--plan`/`--reference-plan` for instructor-governed research plans, `--mode` (train/inference), `--phase` (full/prefill/decode), `--power` telemetry, `--dry-run`. |
| `verify` | user | Verifies a `.provd.json` provenance manifest. |
| `report` | user | Prints or exports a report summary (summary/json/csv/html), with an optional `--baseline` comparison. |
| `package` | user | Packages a verified submission into a portable zip. |
| `list` | user | Lists workloads, suites, profiles, variants, or a matrix view. |
| `show` | user | Shows detail for one workload. |
| `info` | user | Shows detail on a suite, profile, workload, model, dataset, or run. |
| `cache` | user | Lists or verifies locally cached assets. |
| `grade` | instructor/maintainer | Grades a submissions directory, manifest, or package against an assignment YAML. |
| `validate` | instructor/maintainer | Runs bundled validation presets (`smoke`, `coverage`, `max`, `pro`, `release`), executing workloads and grading artifacts. |
| `audit` | instructor/maintainer | Audits registry and public-result metadata (source, license, dataset, model, quality) without running benchmarks. `--policy public` fails on unresolved endorsement warnings. |

### 1.3 Documented, unimplemented CLI surface

`LOCAL_EXECUTION_PLAN.md` explicitly labels these as design targets, not current behavior: `doctor --local`, `run --resume`, `fetch --collection all`. If you're implementing one of these, that document already has the design intent written down; read it before designing your own approach from scratch.

---

## 2. Core modules

### 2.1 `registry.py`

Defines the `Workload` class and the machinery that loads and validates workload definitions from `registry/suites/`: `load_registry()`, `load_registry_directory()`, `load_native_workload_directory()`, `validate_registry()`, plus contract-checking helpers `public_contract_issues()`, `quality_target_satisfied()`, and `select_workloads()`.

### 2.2 `harness.py`

Defines the scenario and result data model: `ScenarioConfig`, `HarnessSample`, `HarnessResponse`, `HarnessRunResult`, and the three supported scenarios (`SCENARIOS` tuple): offline, single-stream, and server. `run_harness()` executes a run; `harness_metrics()` and `percentile()` compute the resulting statistics.

### 2.3 `contracts.py`

`evaluate_report_contract()` and `evaluate_promotion_contract()` check a run's report against, respectively, the basic reporting contract and the stricter Five-Run Promotion Protocol requirements described in the design doc; `aggregate_contract_issues()` rolls up issues across a set of runs.

### 2.4 Per-domain runners (`runners/`)

One file per workload domain: `vision.py`, `text.py`, `graph.py`, `timeseries.py`, `recommendation.py`, `reinforcement.py`, `retrieval.py`, `tiny.py`, `nanogpt.py`, `code_generation.py`, `function_calling.py`, `functional_setup.py`, `image_generation.py`, plus shared logic in `common.py`. `reference/` holds the actual reference model/eval implementations these runners drive (`cloud/`, `timeseries/patchtst/`, `tiny/`).

---

## 3. `bench/measure_peaks.py`: not the benchmark engine

This is a small, standalone utility, not part of the harness. It measures the *host machine's* peak FLOPS (best-of-5 2048x2048 fp32 matmul) and peak memory bandwidth (best-of-5 streaming 256MB tensor clone), computes a roofline ridge point from the two, and caches the result to `~/.mlperf-edu/machine_caps_&lt;hardware-fingerprint&gt;.json`. Its own docstring says it replaces hardcoded reference-machine defaults previously baked into `src/mlperf/roofline.py`. If you're looking for the actual benchmark execution engine, it's in `src/mlperf/harness.py` and the `runners/`, not here.

---

## 4. The registry: `registry/suites.yaml` and `datasets.yaml`

### 4.1 Workload registry

`registry/suites.yaml` and the per-domain files under `registry/suites/{graph,language,recommendation,reinforcement,timeseries,tiny,vision}/` define the actual fourteen-workload portfolio. `registry/selection-ledger.yaml` tracks the rationale for which workloads were selected.

### 4.2 Dataset catalog (`datasets.yaml`)

One entry per dataset, each with `display_name`, `description`, `uri`, `estimated_size_mb`, `split`, `license`, `license_status`, and `public_release_status`. A real entry:

```yaml
cifar10:
  display_name: "CIFAR-10"
  description: >
    Pinned CIFAR-10 test split selected by the official MLPerf Tiny
    200-sample ResNet8 accuracy index.
  uri: "https://huggingface.co/datasets/uoft-cs/cifar10/tree/&lt;commit&gt;"
  estimated_size_mb: 23.9
  split: mlperf_tiny_200_sample_accuracy_set
  license_status: source-citation-no-license
  public_release_status: needs-release-decision
```

`public_release_status` values range from clean (`public-ok-fetch-only`, `public-ok-bundled`) to blocked (`needs-release-decision`, `external-terms-acceptance-required`, `mlcommons-review-required`). Check this field before assuming a dataset can simply be bundled or redistributed; several currently can't.

---

## 5. Provenance and evidence artifacts

### 5.1 Provisional results (`provisional_results/`)

The draft v0.1 evidence snapshot, covering all workloads that have at least a functional local path, bound to one specific git commit. `provisional_results/index.json` lists each case with an `evidence_class` (`five-run-verified` or `single-run-provisional`), whether it's `eligible_for_promotion`, and its `result_role`. A real per-case file (for example, an anomaly-detection inference case) records five execution artifacts, each SHA-256-hashed, an aggregate measurement block (mean, median, standard deviation across the five runs), the `quality.gate` block (metric name, target, direction, and whether all runs must pass), a `repeatability.coefficient_of_variation` value checked against the 5% limit, and `comparison_fingerprints` confirmed identical across all five runs. This is the literal, on-disk mechanism behind the "quality-gated" and "verification" claims in the design doc.

The project is explicit that this directory is **not** an MLCommons-verified result and **not** yet review-eligible; it's a draft snapshot, not the final promoted index.

### 5.2 Conformance results (`conformance_results/`)

Ad hoc conformance-audit JSON, distinct from the provisional evidence snapshot. Includes a real PyTorch-versus-TFLite parity audit comparing prediction outputs on the image-classification workload (accuracy matched at 0.87 on both backends, maximum absolute probability error around 1.5e-5, bound to a specific git commit and dataset/model digests), an example of the project's cross-backend verification tooling in practice.

### 5.3 The future strict index (`reference_results/`)

Referenced in `README.md` and `PUBLIC_RULES.md` as the eventual home of the strict, five-run-promoted, public evidence index, distinct from the draft snapshot in `provisional_results/`. As of this document it exists as a directory but is not yet populated with the strict, review-gated index it's meant to hold.

---

## 6. Public rules and quality targets: where the numbers come from

`QUALITY_TARGET_REVIEW.md` documents, per workload, which of three target kinds its quality gate is: an **inherited acceptance gate** (the upstream benchmark already defines the pass bar), a **published reference reproduction** (the target is one specific published point result), or a **published mean with tolerance** (a target drawn from a published mean and standard deviation). If you're adding a new workload or reviewing an existing gate, this document tells you which of the three justification patterns to use and why, rather than letting a new gate be picked arbitrarily.

`PUBLIC_RULES.md` is the concrete rulebook: the Five-Run Promotion Protocol's exact requirements, the power and interruption policy (AC power, Low Power Mode disabled, any sleep or power-mode change invalidates the attempt), the causal-lineage rule for multi-phase inference workloads (full, prefill, and decode phases must all trace back to one committed training run's checkpoint and provenance digests), the full list of required disclosure fields for any published result, and an explicit list of prohibited claims (labeling a result "official MLPerf," comparing across undisclosed differing hardware fingerprints, presenting a functional-only `min`-profile run as a quality result, reporting timing from a run that failed its quality gate, or claiming distributed or datacenter relevance).

---

## 7. Examples (`examples/`)

A numbered classroom sequence, per `examples/README.md`:

| Example | Question it answers | Real command shown |
|---|---|---|
| `01-health-check/` | Is the install ready? | `uv run mlperf doctor`, then `uv run mlperf health --output-dir submissions/01-health` |
| `02-inference-tradeoff/` | Effect of a controlled batch-size change | `plan.yaml`, `schema: mlperf-edu-experiment-plan/0.3`, compares batch size 16 versus 64 on image classification |
| `03-training-tradeoff/` | Did a training change give an acceptable checkpoint? | `plan.yaml`, tracks training-to-inference lineage |
| `04-result-comparison/` | Which comparisons are valid? | Produces a compatibility-checked HTML report |
| `05-assignment-package/` | Can a result be verified and graded elsewhere? | `assignment.yaml`, `schema: mlperf-edu-assignment/0.1`, requires one `min`-profile, non-quality-gated run |

`examples/README.md` explicitly flags three legacy `lab*.py` scripts (`lab1_optimization.py`, `lab2_inference_sut.py`, `lab3_arch_comparison.py`) as standalone teaching experiments that do **not** emit canonical benchmark artifacts; don't present their output as a registered MLPerf EDU result.

---

## 8. Testing (`tests/`)

34 pytest files. Notable groupings: `test_edu_cli.py` and `test_harness.py` exercise the CLI and harness directly; `test_registry.py` and `test_taxonomy.py` validate the workload registry; `test_assignment.py`, `test_contracts.py`, `test_manifest.py`, `test_fingerprint.py`, and `test_power.py` cover the provenance and contract machinery; `test_reference_sweep.py`, `test_reference_source_lock.py`, `test_check_reference_claims.py`, `test_sync_verified_baselines.py`, `test_import_reference_evidence.py`, and `test_import_provisional_reference_results.py` cover the evidence-promotion pipeline specifically; and each runner has its own dedicated test file (`test_code_generation.py`, `test_recommendation.py`, `test_reinforcement.py`, and similar). Run with `uv run pytest` from `mlperf-edu/`; `pyproject.toml` sets `testpaths=["tests"]` and `pythonpath=[".", "src"]`.

---

## 9. CI/CD implementation notes

### 9.1 `mlperf-edu-validate-dev.yml`: the fast, blocking gate

Runs on every pull request and push to `dev` touching `mlperf-edu/**`. Its own header comment is explicit: "Actual max/release execution lives in `mlperf-edu-release-validation.yml` and is never represented by a dry run." Jobs include: `tests-and-portability` (pytest plus style checks), `python-compatibility` (a clean-wheel install matrix across supported Python versions), `generated-contracts` (checks that registry, taxonomy, review-packet, and doc generation haven't drifted from source), `smoke-and-labs` (actually runs `mlperf validate smoke` and the example lab entry points), `wheel-smoke` (a clean wheel install plus a packaged-registry check), `site-render` (Quarto build plus an internal link check), `paper-build` (builds and verifies the paper PDF), and `external-links`.

### 9.2 `mlperf-edu-release-validation.yml`: the slow, evidence-bearing run

Its own header comment: "This workflow executes benchmark work. It is the evidence-bearing counterpart to validate-dev and has a five-hour hard limit. A dry run is never reported as max or release validation." Triggered manually (with a `preset` input, `release`/`max`/`pro`) or on a weekly schedule. Runs the full pytest suite and registry/taxonomy/reference-claim checks as a pre-flight, then executes `mlperf validate &lt;preset&gt;`; for `release`/`max` presets it also runs the full causal-language-modeling workload across all three inference phases (full, prefill, decode) and grades the result. It always finishes by running `mlperf audit --policy public` and verifying the evidence boundary. Produces no deploy target itself; it's consumed as a required gate by the publish workflow.

### 9.3 `mlperf-edu-publish-live.yml`: documentation only

Its own header comment is explicit: "Publishes only the documentation preview. It does not publish a Python package, create a benchmark release, or imply MLCommons endorsement." Gated on both `mlperf-edu-validate-dev.yml` and `mlperf-edu-release-validation.yml` being recently green (within a 7-day window). Builds the Quarto site and deploys it to this repository's own `gh-pages` branch under `mlperf-edu/`, published at `mlsysbook.ai/mlperf-edu/`.

### 9.4 `mlperf-edu-preview-dev.yml`

Triggers on push to `dev` touching `mlperf-edu/**`, or manually. Calls the validate workflow as a gate, then builds the Quarto site and deploys it to an external dev-preview repository via SSH.

---

## 10. Local development workflow

1. `cd mlperf-edu && uv sync --locked --extra dev`.
2. `uv run mlperf doctor`, then `uv run mlperf list profiles`, then `uv run mlperf validate smoke --output-dir submissions/install-smoke`, to confirm your environment is actually working before touching anything.
3. `uv run pytest` for the test suite.
4. Before opening a PR, run the release-checklist sequence documented in `INSTALL.md`: pytest, `tools/export_flat_registry.py --check`, `tools/sync_verified_baselines.py --check` (if present), `mlperf audit --policy public` (expected to currently return a nonzero exit code while workloads remain experimental, per `INSTALL.md`, don't be alarmed by that specific failure), and `mlperf validate smoke`.
5. If you're touching the docs site: `quarto render site` from `mlperf-edu/`, plus the site-layout check script referenced in `INSTALL.md`.

---

## 11. Common contribution workflows

### Bringing a blocked workload (DLRM or MiniGo) to local execution

1. Read `LOCAL_EXECUTION_PLAN.md`'s dedicated workstream for the workload you're targeting first; both have detailed, staged implementation plans already written, don't start from scratch.
2. Confirm you understand the specific resource constraint blocking it (a checkpoint too large for typical local memory for the recommendation workload, or a legacy TensorFlow 1/CUDA runtime dependency for the reinforcement-learning workload) before proposing an approach.
3. Follow the plan's staged acceptance gate and commit-sequence guidance rather than attempting the whole workstream in one change.

### Implementing a documented-but-missing CLI feature

1. `doctor --local`, `run --resume`, and `fetch --collection all` are explicitly called out in `LOCAL_EXECUTION_PLAN.md` as planned but not implemented. Read that document's "Asset and CLI Workstream" section for the intended design before implementing.
2. Add the flag to the relevant subparser in `edu_cli.py`, implement the behavior, and add a test in `tests/test_edu_cli.py`.

### Adding a new workload

1. Confirm an authoritative upstream task, model, dataset, and evaluator actually exist for what you want to add; per the project's core design philosophy, an invented substitute is never acceptable, even temporarily.
2. Add the workload definition under the appropriate domain in `registry/suites/`, and a dataset entry in `datasets.yaml` if it introduces a new dataset, including an honest `license_status`/`public_release_status`, don't assume `public-ok-bundled` without actually checking the license.
3. Write a runner under `src/mlperf/runners/` (or extend an existing domain file if it fits naturally).
4. Determine which of the three quality-target justification patterns from `QUALITY_TARGET_REVIEW.md` applies, and document your target the same way existing entries do.
5. Add tests, and expect the workload to start at the "functional integration" stage, not "promoted"; don't claim quality conformance or promotion status until the actual staged evidence has been produced and reviewed.

### Reviewing or updating a quality target

1. Read `QUALITY_TARGET_REVIEW.md`'s existing entry for the workload, understand which of the three target kinds it's classified as and why.
2. Any change to a numeric gate should go through the same review-checklist questions the document already lists for reviewers, don't just edit the number.
