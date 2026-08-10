# Site: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md). Site splits into two genuinely different codebases with different style situations, the newsletter CLI and everything else.*

## Newsletter CLI (`site/newsletter/`)

Configured in `site/newsletter/pyproject.toml`:

- **Formatter/linter**: `ruff`, line length 100, target `py310`, rule selection `E`, `F`, `I`, `W`, `B`, `UP`.
- **Type checking**: none configured.

## Everything else (`site/scripts/`, mini-games, main site JS)

No style configuration at all. `site/scripts/*.py` (`build_stats.py`, `fingerprint_assets.py`, `inject_stats.py`) are **not** covered by the newsletter's `pyproject.toml`, that file is scoped to `site/newsletter/` only, and no separate manifest exists for `site/scripts/`. The 14 mini-games (see [`stats.md`](../stats.md)) and the rest of the site's JavaScript have no ESLint config, no `tsconfig.json`, no Prettier, confirmed directly, none of these files exist anywhere under `site/`.

Match the existing conventions of whichever script or game you're editing; this is one of several sub-projects in this repo (alongside TinyTorch, Labs, MLPerf EDU, SocratiQ, design-grammar, Slides) with no enforced code style of any kind.
