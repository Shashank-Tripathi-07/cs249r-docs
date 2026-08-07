# Site: System Design

*This document describes how the unified landing site actually works: the newsletter sync CLI, the mini-games, and the two-stage pipeline that populates every live number on the homepage. It is written for a contributor changing the newsletter sync, adding a game, or debugging a stat that won't update. All facts below are sourced from `site/newsletter/cli/`, `site/scripts/`, and `site/games/`.*

## 1. Problem this system solves

The landing site has to display numbers, GitHub stars, merged PR count, newsletter subscriber count, GA4 readership, that come from four different external sources with four different failure modes, without ever showing a blank or broken number to a visitor, and without requiring a full site rebuild every time one of those numbers changes. Separately, its newsletter content lives in a third-party service (Buttondown) but needs to be real, versioned, Quarto-rendered content in this repository, not just an embed. Both problems get solved the same way: a small, focused CLI that syncs external state into the repo on its own schedule, decoupled from the site's own build.

## 2. Dependencies and what each one actually does here

`site/newsletter/pyproject.toml` declares `rich` (console output), `requests` (the Buttondown REST client), and `python-frontmatter` (parsing and writing the YAML frontmatter on synced post files). The newsletter CLI itself, `news`, is a hand-rolled `argparse` application explicitly modeled on TinyTorch's `tito` CLI pattern, a command registry dict (`COMMANDS`) mapping ten subcommand names to `BaseCommand` subclasses: `new`, `list`, `check`, `push`, `pull`, `set-author`, `archive`, `diff`, `open`, `status`, grouped into `draft`/`publish`/`archive`/`info` categories for the welcome screen a bare `news` invocation prints.

## 3. Component inventory

```
site/
  about/  community/  newsletter/  games/
  config/          <- stats-cache.json lands here
  scripts/         <- build_stats.py, inject_stats.py
  assets/games/    <- sprites, vendor JS, shared CSS for the mini-games
```

## 4. Data flow: a newsletter post, from Buttondown to the rendered site

```
1. news pull  (site/newsletter/cli/commands/pull.py, PullCommand)
                    |
2. Fetch sent emails via core/buttondown.py, newest-first, stop after
   3 consecutive already-synced posts (idempotent early exit)
                    |
3. _email_to_markdown() per post:
   strip HTML comments
   _categorize() keyword heuristics -> community / hands-on / essay / update
   _detect_author(), a 5-tier fallback chain:
     structured metadata -> HTML author marker -> "Written by X" regex
     -> subject-line keywords -> hardcoded CLI default
   build Quarto frontmatter (title/date/author/description/categories/image)
                    |
4. Write to site/newsletter/posts/<year>/<date>_<slug>.md
   _preserve_existing_author() guards a hand-corrected byline from
   being overwritten on a later re-pull
                    |
5. Unless --no-stats: fetch subscriber count from Buttondown's
   /subscribers endpoint, write site/newsletter/_stats.yml
   (a separate, smaller stats file from the site-wide one below)
                    |
6. sync-newsletter.yml (CI) invokes exactly this, commits to dev and
   main, dispatches site-publish-live.yml to actually deploy
```

The 5-tier author-detection fallback exists because Buttondown emails don't carry a single, reliable structured author field, this is a real, deliberate degrade-gracefully chain, not an accident, ending in a hardcoded default rather than ever leaving a post unattributed.

## 5. Data flow: the site-wide stats pipeline

Two scripts, chained, not one:

```
build_stats.py (Quarto pre-render step)
  three source tiers, each degrading independently on failure:
    1. repo-derived, via `git ls-files`: slide/SVG counts, tinytorch
       module count, Vol II chapter count (parsed directly out of
       book/quarto/config/_quarto-pdf-vol2.yml), lab count (from
       labs/mlsysbook_labs/catalog.py), last-updated date
    2. public GitHub API: star count, merged PR count
    3. credentialed: GA4 Data API (readership, via a service-account
       JSON secret) and Buttondown's subscriber endpoint
  a failed fetch at any tier leaves the PREVIOUS cached value in
  place rather than zeroing it out or failing the build
  writes site/config/stats-cache.json

inject_stats.py (Quarto post-render step)
  substitutes {{stats.key}} placeholders across the built HTML/JSON
  using stats-cache.json's display map
  an unresolved placeholder FAILS the build, so a stat that silently
  stopped being generated is a hard build error, not a blank page
```

`site-refresh-stats.yml` (the six-hourly workflow) runs `build_stats.py` and repackages just the `display` values into a flat `stats.json`, pushed only to `gh-pages`, never to `dev` or `main`. The homepage fetches `/stats.json` client-side on load, which is exactly why this workflow can refresh live numbers without triggering a full Quarto rebuild, deploy, and Cloudflare purge every six hours.

## 6. The mini-games

Real Quarto pages under `site/games/`, not a placeholder: `index.qmd` (a gallery landing page titled "MLSysBook Playground", marked early development), plus one page per concept, `lander.qmd` (a Lunar-Lander-styled batch-size/learning-rate/VRAM tradeoff game), `allreduce.qmd`, `batch.qmd`, `checkpoint.qmd`, `cluster.qmd`, `kvcache.qmd`, `loader.qmd`, `moe.qmd`, `oom.qmd`, `prune.qmd`, `quantization.qmd`, `roofline.qmd`, `topology.qmd`, each mapping a real ML-systems concept onto a small interactive game. Supporting sprites, vendor JS, and shared CSS live in `site/assets/games/`.

## 7. Contributing

If you are touching the stats pipeline, remember it's two scripts, not one, `build_stats.py` computes and caches, `inject_stats.py` substitutes at render time, and a stat that stops appearing could be broken in either stage, or simply degraded to a stale cached value because one of the three source tiers failed silently by design. If you are touching the newsletter sync, test `news pull` against a real Buttondown post with an ambiguous author before assuming the 5-tier detection chain handles your case, it's a heuristic stack, not a guarantee.
