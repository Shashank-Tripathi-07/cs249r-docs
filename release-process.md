# Release Process

*How a change actually gets from `dev` to the live site and, for the sub-projects that ship as real packages, to PyPI. Written from the actual workflow files (`publish-all-live.yml`, each sub-project's own `*-publish-live.yml`, `infra-publish-guard.yml`, `mlsysim-pypi-publish.yml`), not from a README description of intent. If you're about to run a release, or trying to understand why one behaved a certain way, this is the doc.*

## 1. Two ways a release happens

**The orchestrator**, `publish-all-live.yml`, is a thin `workflow_dispatch`-only sequencer with no build logic of its own. It calls each sub-project's own standalone publish workflow via `uses:`, in a fixed order, with real `needs:` dependencies between them:

```
Book → Kits → TinyTorch → MLSys·im → Labs → StaffML → Slides → MLPerf EDU (preview, off by default) → Site → Instructors
```

Every step after the first checks that every prior *enabled* step actually succeeded before running (`!inputs.deploy_X || needs.deploy-X.result == 'success'`), so a failure early in the chain stops everything downstream from deploying against a broken assumption, while a step you toggled off doesn't block anything after it.

**The standalone path**: any sub-project's `*-publish-live.yml` can also be triggered directly, independent of the orchestrator, for a single-project release that doesn't need to touch the other ten.

Both paths share the same required safety input: typing the literal string `PUBLISH` into a `confirm` field. Nothing publishes without it.

## 2. The publish guard, every release is gated on a green `dev`

`infra-publish-guard.yml` is a reusable `workflow_call` every `*-publish-live.yml` calls before it does anything else. It queries the latest run of that sub-project's own `*-validate-dev.yml` on `dev`, and refuses to let the publish proceed unless that run is `success` **and** no older than 24 hours (`max_age_minutes: 1440` by default). This is a deliberate anti-staleness check: a validate run from three days ago being green tells you nothing about whether the `dev` branch is still healthy right now.

## 3. `site_only` mode: the escape hatch for content-only touch-ups

Every sub-project's publish workflow accepts a `site_only` boolean. When true: the merge to `main`, the full build, and the deploy all still run normally, but version bumping, git tagging, release-notes generation, and GitHub Release creation are all skipped. This exists for exactly the situation this session hit more than once, a real content fix that needs to ship, but doesn't deserve a version bump. `publish-all-live.yml` defaults `site_only: true` for this reason, "most publish-all runs are content refreshes," not real releases.

## 4. `release_type`: patch / minor / major

Where a real version bump does happen, it follows standard semver via a `release_type` choice input (`patch`, `minor`, `major`, default `patch`). TinyTorch additionally accepts an `explicit_version` override that bypasses the calculated bump entirely, and ignores `release_type` when set.

## 5. Book: the heaviest, most defended release path

Book publishing is opt-in even inside `publish-all-live.yml` (`deploy_book: false` by default, "book release is heavier, opt in explicitly"), and for good reason, it's the only release with its own multi-stage safety net:

- **DOI rewrite protection**: each volume's `index.qmd` carries a `doi:` field, and the release step reads it as the source of truth for that volume's current version. The rewrite logic explicitly refuses to downgrade, if the DOI in the file is already ahead of what a tag search would suggest, it leaves it alone rather than silently reverting a manually-corrected version forward.
- **AI-generated release notes**, togglable via `ai_generated_notes`.
- **A 3-hour ceiling on the whole release**: `180` status-check attempts at `60` second intervals is the literal timeout budget encoded in the workflow inputs.
- **Automatic revert on failure or timeout**: the merge to `main` gets reverted, not left in a half-published state, if the release doesn't complete cleanly. This is not a hypothetical, the 2026-06-20 to 2026-06-23 incident cluster documented in [`ci-workflows.md`](ci-workflows.md#a-cluster-worth-knowing-about-but-not-fully-detailed-here) is exactly this machinery being exercised under real pressure (a wrong Cloudflare purge token, the publish chain continuing after a failed deploy, TinyTorch PDFs getting wiped by a site-only deploy that shouldn't have touched them).
- **`deploy_target`**: `vol1`, `vol2`, or `all`, letting a release touch one volume without forcing a rebuild of the other.

## 6. TinyTorch: version bumped in six files, not one

A full TinyTorch release bumps its version string across six separate files on `dev` before tagging (the workflow's own comment states this explicitly), then syncs `dev` to `main` and builds the release PDFs. `site_only` skips the version bump, the tag, and the PDF build entirely, deploying the current site as-is.

## 7. MLSys·im: the only sub-project that ships to PyPI

`mlsysim-pypi-publish.yml` is a separate, dedicated workflow (not just a step inside `mlsysim-publish-live.yml`) that publishes the real `mlsysim` package to `https://pypi.org/project/mlsysim/`. Worth knowing if you touch it:

- **No stored API token.** It authenticates via **OIDC Trusted Publishing** (`id-token: write` permission, configured one-time on pypi.org's own web UI against this exact workflow filename and a named GitHub environment), which means there is no long-lived PyPI credential sitting in repo secrets to leak.
- **PEP 740 attestations** are generated and attached to the published artifact via `pypa/gh-action-pypi-publish`, a supply-chain provenance guarantee beyond what a plain `twine upload` gives you.
- **Environment protection**: the publish job runs under the `pypi-mlsysim` GitHub environment, which is how deployment protection rules (if configured) would gate it, and is what the release URL (`https://pypi.org/project/mlsysim/<version>/`) is computed against.
- `skip_pypi` exists as a dispatch input for a dry run that does everything except the actual upload.

MLSys·im was also the sub-project relicensed to Apache-2.0 at its v0.1.0 release (PR #1521), worth knowing if you're checking what license a `pip install`'d copy actually carries.

## 8. StaffML: the only release that ships both a static site and live backend state

StaffML's publish workflow does something none of the others do, it ships **data**, not just code:

1. `npx wrangler d1 execute` (or equivalent) pushes the current `vault.db` contents to the production **D1** database, so live question data matches what the build just validated.
2. The **Cloudflare Worker** (`staffml-vault-worker`) is only redeployed if `interviews/staffml-vault-worker/**` actually changed in this release, not unconditionally every time.
3. `wrangler` is pinned to a specific version (`4.87` as of this writing) for the worker-deploy step specifically, called out with an explicit comment in the workflow, worth checking if a `wrangler` bump PR (like the pattern in [`dependency-map.md`](dependency-map.md) section 4) needs updating here too.

The D1-then-Worker ordering matters: shipping the Worker before the data would risk it serving a schema or dataset the deployed code doesn't expect yet.

## 9. What "PUBLISH" actually gates, and what it doesn't

Typing `PUBLISH` is a human-intent confirmation, not a validation gate, the guard from section 2 already handled correctness. It exists specifically to prevent an accidental `workflow_dispatch` (a stray click, a copy-pasted `gh workflow run` command) from shipping a real release nobody meant to trigger. It says nothing about whether `dev` is actually in a good state, that's the publish guard's job, running before this check is ever reached.

## 10. If a release goes wrong

Check [`ci-workflows.md`](ci-workflows.md) first, several real, dated release-pipeline incidents are already documented there (the concurrency-collision pattern hitting publish workflows specifically, the terse multi-commit cluster around Cloudflare purge and site-only deploys). [`troubleshooting.md`](troubleshooting.md) is the fast symptom-lookup version of the same information.
