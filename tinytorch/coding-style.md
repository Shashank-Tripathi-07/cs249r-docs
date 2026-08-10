# TinyTorch: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

There is no enforced Python style in TinyTorch. This is worth stating plainly rather than leaving implicit: `tinytorch/pyproject.toml` has no `[tool.ruff]`, `[tool.black]`, or `[tool.mypy]` section, and the root `.pre-commit-config.yaml`'s hooks don't reach into `tinytorch/`.

TinyTorch does have its own `.pre-commit-config.yaml` (`cd tinytorch && pre-commit run --config .pre-commit-config.yaml --all-files`, not wired into the default `pre-commit install`), but its Python formatting hook is present and deliberately disabled:

```yaml
# Uncomment to enable Python linting/formatting
# - repo: https://github.com/psf/black
#   rev: 24.4.2
#   hooks:
#     - id: black
#       name: "Format Python code"
#       files: ^(src/|tinytorch/|tools/).*\.py$
```

That file's own two active hooks are content checks, not code style: `collapse_blank_lines.py` (markdown/Python, collapses 2+ blank lines to one) and `validate_cli_docs.py` (checks that documented `tito` commands match the real CLI).

**Practical implication**: match the surrounding module's existing style by eye. `black` at the commented-out settings above (which would match root's `line-length = 100` convention if ever enabled) is the closest thing to an implied target, but nothing currently checks for it. If you're adding a new module under `src/`, look at an adjacent module (`06_autograd`, `09_convolutions`) for the prevailing convention rather than assuming a formatter will normalize it later.
