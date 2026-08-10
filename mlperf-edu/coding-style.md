# MLPerf EDU: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

No enforced Python style. Checked directly against `mlperf-edu/pyproject.toml`: no `[tool.ruff]`, no `[tool.black]`, no `[tool.mypy]`, the only `[tool.*]` sections present are `[tool.setuptools.packages.find]`, `[tool.setuptools.package-data]`, and `[tool.pytest.ini_options]`. Testing configuration exists; style enforcement does not.

This matters more here than it might in a purely academic codebase, this is the sub-project responsible for grading integrity and anti-cheat logic (see [`system_design.md`](system_design.md) and the provenance-manifest system it describes). A missing linter doesn't cause the silent-failure bugs documented across the `mlperf-edu` fix series (the anti-cheat bypass, the vacuous seed check, the wrongly-skipped quality-target check), those were logic bugs a linter wouldn't have caught anyway, but it does mean nothing else is catching the smaller stuff either.

Also relevant here, not a style issue but adjacent to it: this project's CI has a known, currently unfixed gap, a missing `matplotlib` test dependency that fails `Tests, Audit, and Package Portability` on any PR regardless of what it touches. See [`ci-workflows.md`](ci-workflows.md) and the [top-level troubleshooting doc](../troubleshooting.md).
