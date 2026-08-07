# Site: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only the Site workflows; read the top-level doc first for the general validate/preview/publish pattern every project follows.*

`site-validate-dev.yml` (Quarto build plus a non-blocking Tier 2 link check, added specifically because `site-preview-dev.yml` used to jump straight to building and deploying with no validation gate at all), `site-preview-dev.yml`, `site-publish-live.yml` (both sync newsletter content from Buttondown before building).

A third workflow, `site-refresh-stats.yml`, runs every six hours and writes only `gh-pages/stats.json`, deliberately not `dev`, specifically so a scheduled bot commit never leaves every contributor's local checkout behind, and any missing secret (GA4, Buttondown, GitHub) degrades to the previously committed value rather than failing the run.

A fourth, `sync-newsletter.yml`, runs daily, pulls published emails from Buttondown, commits them to both `dev` and `main` (cherry-picked, since `main` is the stable release branch gh-pages actually publishes from), and dispatches `site-publish-live.yml` rather than building the deploy itself, specifically because a past version of this workflow did its own partial deploy and corrupted `site_libs/` for the about and community pages by leaving them referencing a stale hashed bootstrap CSS file.
