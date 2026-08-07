# StaffML: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the StaffML workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

This project has the most workflows of any single sub-project, seven in total, because its surface area (a Next.js frontend, a content pipeline, two Cloudflare Workers, and a research paper) is genuinely wider than a single Quarto site.

| Workflow | What it does |
|---|---|
| `staffml-validate-dev.yml` | `tsc` plus unit tests, a Next.js static build, vault/corpus smoke checks, and a Playwright E2E pass. |
| `staffml-validate-vault.yml` | The data-layer counterpart: ruff, mypy, and pytest on `vault-cli`; `vault check --strict` (9 structural invariants); a release-hash equivalence check; `vault codegen --check` (the hash-drift guard covered in [`system_design.md`](system_design.md) section 7); a registry append-only check; and `vitest` on the vault Worker. |
| `staffml-preview-dev.yml` | Runs both validate workflows and its own build job in parallel, deploys only once all three succeed. |
| `staffml-publish-live.yml` | Full production deploy, including a `/interviews/` redirect kept for backward compatibility with the pre-rename URL. |
| `staffml-auto-pr.yml` | When an issue is labeled `action: auto-pr`, generates a flashcard from the issue body and opens a PR against dev automatically. |
| `staffml-chain-rebuild.yml` | Opt-in (dispatch only, cron intentionally disabled until it proves stable), regenerates question-chain groupings via Gemini and opens a PR with the delta rather than mutating `chains.json` directly. |
| `staffml-audit-corpus-monthly.yml` | Scheduled Gemini-driven quality audit of the full published corpus, roughly 315 LLM calls per run. As of this document, its own header comment states the schedule cannot actually fire yet, the runner has no `gemini` CLI installed, so only a manual dispatch would currently succeed, and that would fail too until the auth path is wired. This is a real, currently-inert workflow, not a hypothetical one. |
| `staffml-update-paper.yml` | Rebuilds the StaffML research paper PDF from LaTeX and the current corpus stats. |
| `staffml-welcome.yml` | Posts a welcome comment on a contributor's first PR touching `interviews/`. |

## Real, verified bugs in this pipeline

This project has the most documented CI incidents of any sub-project, five real dated fixes, which tracks with it having the most workflows and the widest surface area.

**2026-05-04 (`2a61ece3f5`): the concurrency-group collision.** `staffml-validate-vault.yml`'s concurrency group silently collided with `staffml-validate-dev.yml`'s. Both are called via `workflow_call` from the same parent, `staffml-preview-dev.yml`, and both resolved their concurrency group key from `${{ github.workflow }}`, which evaluates to the *caller's* workflow name inside a `workflow_call`, not the callee's own name. That meant both vault-validate and dev-validate produced the identical group key whenever invoked from the same parent run, and GitHub's `cancel-in-progress` behavior silently killed whichever queued a few seconds later, turning the README badge red on every single push despite every real check having actually passed. Fixed with a literal, workflow-identifying string plus `${{ github.head_ref || github.run_id }}` instead of `${{ github.workflow }}`. Two other projects independently hit the same bug class later, see the [top-level doc's Pattern A](../ci-workflows.md#pattern-a-concurrency-group-too-coarse-dispatch-collides-with-push).

**2026-05-01 (`6ddb82a71b`): deploy could ship despite a failed validation.** `staffml-preview-dev.yml` and its validate workflows were both independently push-triggered on `dev`, running in parallel with no gate between them, so a deploy could ship on a SHA that Validate (Dev) or Validate (Vault) had actually failed on. Restructured into the current shape (`validate-dev` + `validate-vault` + `build`, all as `needs:` for `deploy`), reflected in the table above.

**2026-06-16 (`b4f74be474`): the badge could go stale even after the underlying bug was fixed.** `staffml-preview-dev.yml`'s path filter (the one that drives the README badge) didn't include `interviews/vault-cli/**`, even though its own called `validate-vault` job lints vault-cli. A real fix (PR #1879) landed and `validate-vault` went green, but `preview-dev` never re-ran since nothing in its own trigger paths had changed, so the badge stayed stuck on the prior failing run indefinitely.

**2026-05-01 (`39336c4d9c`): missing permissions block.** CodeQL flagged `staffml-validate-vault.yml` for having no explicit `permissions` block, defaulting to the repo-wide token policy, broader than the job (checkout, install, lint, test) actually needed. Tightened to `contents: read`, closing two CodeQL alerts.

**2026-04-28 (`e153729057`): a shell-quoting bug that silently skipped the entire smoke test.** `staffml-publish-live.yml`'s smoke-test step had a botched heredoc conversion: opened with `python3 -c "..."`, closed with a `PY` heredoc terminator, and the inner Python f-strings used double quotes that silently closed the outer shell string six lines in. Bash then choked on the next `(` and exited before a single line of Python ran. A second, independent bug rode along in the same step, two `open()` calls referenced a `root` variable that was never defined.

**2026-04-29 (`e0d1d61edc`, issue #1600): a validation check that didn't match the actual build output shape.** The deploy validation step checked for flat `<page>.html` paths, but `next.config.mjs` sets `trailingSlash: true`, so Next.js actually emits `<page>/index.html`. The check aborted deploys on every push despite the build being completely correct, until rewritten to check the same path shape `staffml-validate-dev.yml` already checked correctly.
