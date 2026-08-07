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

## A real, verified bug: the concurrency-group collision

`staffml-validate-vault.yml`'s concurrency group used to silently collide with `staffml-validate-dev.yml`'s. Both are called via `workflow_call` from the same parent, `staffml-preview-dev.yml`. Before a documented fix (landed 2026-05-02), both reusable workflows resolved their concurrency group key from `${{ github.workflow }}`, which evaluates to the *caller's* workflow name inside a `workflow_call`, not the callee's own name. That meant both vault-validate and dev-validate produced the identical group key whenever invoked from the same parent run, and GitHub's `cancel-in-progress` behavior silently killed whichever queued a few seconds later, turning the README badge red on every single push despite every real check having actually passed.

The fix uses a literal, workflow-identifying string plus `${{ github.head_ref || github.run_id }}` instead of `${{ github.workflow }}`, so each reusable workflow's group key is genuinely unique per invocation regardless of which parent called it. Worth checking for in any new reusable workflow this project adds: if two `workflow_call` targets are ever invoked from the same parent and either one's concurrency group derives from `github.workflow` without a literal override, they will collide.
