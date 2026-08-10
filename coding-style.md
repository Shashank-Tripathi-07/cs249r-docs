# Coding Style

*There is no single repo-wide coding style. This document exists specifically because that's true and non-obvious, checked directly against every `pyproject.toml`, every `eslint.config.*`, every `tsconfig.json`, and the root `.pre-commit-config.yaml`, not assumed from one project's convention. Three different Python setups, one real TypeScript lint config out of five TS/JS sub-projects, and zero shared formatter across the whole repo. Each sub-project's own `coding-style.md` (linked below) covers its specific setup; this document is the comparison and the one genuinely repo-wide convention that does exist, book prose style.*

## 1. Python: three different setups, not one

| Project | Formatter | Linter | Line length | Type checking |
|---|---|---|---|---|
| Book tooling (root) | `black` + `isort` (profile="black") | none configured | 100 | `mypy`, not `strict = true`, but most strict-mode flags individually enabled (`disallow_untyped_defs`, `disallow_incomplete_defs`, `warn_unreachable`, etc.), target `py39` |
| StaffML vault-cli | `ruff format` (implied by ruff's rule selection) | `ruff`, broad selection (`E`, `F`, `W`, `I`, `B`, `UP`, `N`, `SIM`) with documented per-rule exceptions | 100 | `mypy`, `strict = true`, target `py312` |
| MLSys·im | `ruff format` | `ruff`, deliberately conservative (`E`, `F` only), with an explicit code comment stating the narrower selection is intentional pending a future formatter pass | 120 | none configured |
| Site newsletter CLI | `ruff format` | `ruff` (`E`, `F`, `I`, `W`, `B`, `UP`) | 100 | none configured |
| TinyTorch | none | none | n/a | none |
| Labs | none | none | n/a | none |
| MLPerf EDU | none | none | n/a | none |

Two real facts worth internalizing, not three separate accidents:

- **`mypy --strict` exists in exactly one place**, StaffML's vault-cli, the sub-project with the most schema-integrity requirements (the three-way Pydantic/D1/TypeScript sync covered in [`staffml/system_design.md`](staffml/system_design.md) section 7). Everywhere else, type checking is either partial (root) or absent.
- **TinyTorch's black hook exists in its own `.pre-commit-config.yaml` and is commented out**, not missing, deliberately deferred (`# Uncomment to enable Python linting/formatting`). Labs and MLPerf EDU have no equivalent hook at all, commented or otherwise. If you're writing Python in any of these three, there is no tool that will catch a formatting or style inconsistency before review, this is a real gap, not a documentation oversight.

## 2. TypeScript / JavaScript: one real lint config out of five

| Project | Linter | Type checking | Formatter |
|---|---|---|---|
| StaffML frontend | ESLint (`eslint-config-next` core-web-vitals + typescript, several rules explicitly relaxed, see [`staffml/coding-style.md`](staffml/coding-style.md)) | `tsconfig.json`, `strict: true` | none (no Prettier anywhere in this repo) |
| StaffML vault Worker | none | `tsconfig.json`, `strict: true` | none |
| StaffML AI interviewer Worker | none | `tsconfig.json`, `strict: true` | none |
| SocratiQ | none | none, no `tsconfig.json` exists, plain JavaScript | none |
| design-grammar | none | none, no `tsconfig.json` exists, plain JavaScript | none |

**Prettier is absent from every single project in this repo.** Not configured differently per project, genuinely not present anywhere, no `.prettierrc`, no `prettier.config.*`, no `prettier` key in any `package.json` that was checked. Whatever formatting consistency exists in the TypeScript/JavaScript code is either enforced by ESLint's stylistic rules (StaffML frontend only) or by convention and code review alone (everywhere else).

**`strict: true` in `tsconfig.json`** is the one place real consistency exists: all three of StaffML's TypeScript surfaces (frontend, vault Worker, AI interviewer Worker) enable it. SocratiQ and design-grammar sidestep the question entirely by not using TypeScript.

## 3. The one genuinely repo-wide style: book prose

This is the closest thing to a real, enforced, repo-wide convention, but it applies to `book/` content specifically (not `kits/`, `instructors/`, or `slides/`, none of which are covered by the book's own `validate.py` scopes), and it governs prose, not code. Enforced via `ValidateCommand`'s `Scope` table in `book/cli/commands/validate.py`:

- **No contractions** (`_run_mitpress_contractions`), MIT Press academic register.
- **No "above/below" cross-references** (`_run_mitpress_above_below`), a figure or table can't be referred to by its position on the page, since that position isn't stable across HTML/PDF/EPUB.
- **Percent-sign spacing**, enforced separately and differently for prose, captions, and tables (`_run_percent_spacing`, `_run_mitpress_percent_in_captions`, `_run_mitpress_percent_in_tables`, `_run_mitpress_percent_in_prose`), not one rule, four context-specific ones.
- **"Unblended prose"** (`_run_unblended_prose`) and **heading case** (`_run_mitpress_heading_case`) checks round out the MIT Press house style enforcement.

The project's own CONTRIBUTING guidance states the intent directly: "academic textbook register, active voice, quantitative claims, no blog-post informality." Unlike the code-side tables above, this actually runs as a blocking pre-commit check for anyone touching `book/`.

## 4. Practical implication for a contributor

If you're writing Python in TinyTorch, Labs, or MLPerf EDU: match the surrounding file's existing style by eye, nothing will catch a drift automatically, and don't assume root's `black` config applies, it's never wired into those projects' own checks. If you're writing Python in vault-cli specifically, `mypy --strict` will catch far more than anywhere else in the repo, budget review time accordingly. If you're writing TypeScript anywhere outside the StaffML frontend, ESLint isn't running, `tsc`'s `strict: true` is your only safety net. If you're touching `book/` prose, the contraction/above-below/percent-sign rules are real pre-commit blockers, not suggestions, everywhere else in the repo, equivalent prose gets no such check at all.
