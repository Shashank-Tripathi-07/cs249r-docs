# Book: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md). This file covers the Binder CLI's Python style and the book's own prose style, the closest thing to a genuinely enforced house style anywhere in this repo.*

## Python (the Binder CLI, `book/cli/`)

Configured in the root `pyproject.toml`, since the Binder CLI lives at the repo root:

- **Formatter**: `black`, line length 100, targets `py39` through `py312`.
- **Import sorting**: `isort`, `profile = "black"`, line length 100, `multi_line_output = 3`, trailing commas forced.
- **Type checking**: `mypy`, not full `strict = true`, but most of what strict mode implies is individually turned on: `disallow_untyped_defs`, `disallow_incomplete_defs`, `disallow_untyped_decorators`, `check_untyped_defs`, `warn_unreachable`, `strict_equality`, `no_implicit_optional`. Target `python_version = "3.9"`.
- **Linter**: no `ruff`/`flake8` configured. `black` and `mypy` are the only two tools that will actually stop a commit over Python style here.

## Prose (`book/quarto/contents/**/*.qmd`)

This is real, enforced, and unusual for this repo, most sub-projects have nothing equivalent. Every rule below is a `Scope` registered in `ValidateCommand` (`book/cli/commands/validate.py`) and runs as a blocking pre-commit check, not a suggestion:

- **No contractions** (`_run_mitpress_contractions`).
- **No "above/below" cross-references** to figures or tables (`_run_mitpress_above_below`), position on a page isn't stable across HTML, PDF, and EPUB output.
- **Percent-sign spacing**, four separate context-specific rules: general prose, captions, tables, and word-before-percent, each checked independently (`_run_percent_spacing`, `_run_mitpress_percent_in_captions`, `_run_mitpress_percent_in_tables`, `_run_mitpress_percent_in_prose`).
- **"Unblended prose"** (`_run_unblended_prose`) and **heading case** (`_run_mitpress_heading_case`) round out the MIT Press house style enforcement.

The project's own contributing guidance states the intent plainly: academic textbook register, active voice, quantitative claims over qualitative ones, no blog-post informality.

**Scope note**: these prose checks apply to `book/` content specifically. `kits/`, `instructors/`, and `slides/` are not covered by `ValidateCommand`'s scopes, even though they're also Quarto content, contributors there aren't held to the same prose rules, this isn't a gap so much as those projects simply not having adopted the book's specific house style.
