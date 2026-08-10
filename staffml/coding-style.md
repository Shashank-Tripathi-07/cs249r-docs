# StaffML: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md). StaffML has the most real, active style enforcement of any sub-project, split across four independently-configured surfaces: the Next.js frontend, two Cloudflare Workers, and the Python vault-cli.*

## Frontend (`interviews/staffml/`)

- **Linter**: ESLint via `eslint.config.mjs`, built on `eslint-config-next`'s `core-web-vitals` and `typescript` rule sets, not a from-scratch config.
- **Rules explicitly relaxed** from the Next.js defaults, worth knowing before assuming a rule is active: `@next/next/no-sync-scripts` off, `@typescript-eslint/no-explicit-any` downgraded from error to `warn`, and four `react-hooks/*` rules turned off entirely (`immutability`, `preserve-manual-memoization`, `purity`, `set-state-in-effect`), plus `react/no-unescaped-entities` off.
- **Type checking**: `tsconfig.json`, `strict: true`.
- **Formatter**: none, no Prettier anywhere in this project or the wider repo.
- **This lint config was silently broken for an unknown period** until PR #1981 (2026-08-10), a prior Dependabot bump pinned `eslint` past what `eslint-config-next`'s nested `eslint-plugin-react` peer range supports, and `staffml-validate-dev.yml` has never run a lint step, so CI never caught it. See [`ci-workflows.md`](../ci-workflows.md#pattern-e-a-check-that-ci-never-actually-runs-so-drift-accumulates-silently) Pattern E. If lint is currently crashing for you locally, check the `eslint` version against that PR's fix before assuming your own code is the problem.

## Cloudflare Workers (`interviews/staffml-vault-worker/`, `interviews/staffml/worker/`)

Both Workers have `tsconfig.json` with `strict: true`, but **no ESLint config of any kind**, confirmed, neither directory has an `.eslintrc*` or `eslint.config.*`. Type checking is your only automated check writing Worker code; there's no linter to catch anything `tsc` wouldn't already flag.

## vault-cli (`interviews/vault-cli/`)

The most rigorously configured Python in this repo:

- **Linter/formatter**: `ruff`, line length 100, target `py312`, broad rule selection: `E`, `F`, `W`, `I`, `B`, `UP`, `N`, `SIM`.
- **Documented, deliberate exceptions**, each with a reason in the config itself: `E501` (line length, left to the formatter), `B008` (function-call-in-default-argument, explicitly noted as "THE Typer idiom", `typer.Option(...)` at parameter-default position, the linter doesn't know that's intentional), `UP042` (kept because this project's `(str, Enum)` mixins differ semantically from Python 3.12's native `StrEnum`).
- **Per-file ignores**, also reasoned in comments: `scripts/*.py` allows `E402` (intentional `sys.path` insertion before import, so scripts work standalone or as modules), `validator.py` allows `N806` (the `WHITE`/`GRAY`/`BLACK` cycle-detection color constants are conventional, not a real naming violation), two files allow `SIM118` (iterating a `sqlite3.Row` triggers a false positive from that rule).
- **Type checking**: `mypy`, `strict = true`, target `py312`, the only place in this entire repo `mypy --strict` is actually configured.
- **Enforced in CI**: mirrored via `ruff-pre-commit` in the root `.pre-commit-config.yaml`, scoped specifically to `interviews/vault-cli/.*\.py$`, and again in `staffml-validate-vault.yml`.

If you're contributing to vault-cli specifically, expect the strictest review bar of anywhere in this repo, both automated (ruff's broad selection, mypy strict) and by design (this is the schema-integrity-critical path documented in [`system_design.md`](system_design.md) section 7).
