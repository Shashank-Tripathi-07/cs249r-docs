# MLSys·im: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

Configured in `mlsysim/pyproject.toml`:

- **Formatter/linter**: `ruff`, line length **120** (the longest of any Python sub-project in this repo, everywhere else uses 100), target `py310`.
- **Rule selection is deliberately conservative**: `select = ["E", "F"]` only, style and pyflakes, nothing else. The config file states the reasoning directly in a comment: *"Conservative defaults. Stick to E/F (style + pyflakes); leave W (whitespace nits) to a future formatter pass if the project decides to enforce it."* This is an explicit, acknowledged deferral, not an oversight, if you're wondering why a whitespace inconsistency didn't get flagged, this is why.
- **Type checking**: none configured, no `[tool.mypy]` section.
- **Excludes**: `build`, `dist`, `docs/_site`, `.venv`, `venv`.

This is enforced via CI (the `mlsysim-check-registry-gates` check referenced in [`ci-workflows.md`](ci-workflows.md)'s Pattern C, where a missing `pytest` in `pre-commit`'s fallback dependency list once broke this specific check), so unlike TinyTorch and Labs, `ruff` here is a real, currently-active gate, just a narrow one.
