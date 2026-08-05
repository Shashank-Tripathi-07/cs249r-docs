# Landing and Community Site: Implementation Reference

> **Status: as-built, contributor-facing.** This is a live, already-published Quarto site. This document is your map for reading and modifying the real source: file paths and real code pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| Any static content page | Quarto. Nothing else. |
| The newsletter CLI | Python 3, and a `BUTTONDOWN_API_KEY` (only needed for commands that actually call the Buttondown API, like `push` or `pull`; drafting and diffing work without one). |
| The mini-games | Quarto, plus whatever the specific game's own JS needs (each is self-contained; check the individual `.qmd`/script pair). |
| Shared-asset sync work | Access to the sibling `shared/` directory, and awareness of every project that mirrors the asset you're touching. |

## Repository layout

```
cs249r_book/
  site/
    _quarto.yml                 # Site config: navbar, footer, theming (pulls from ../shared/)
    index.qmd                    # Landing page
    landing.css                   # Base ("v2") landing styles
    landing-v3.css                 # Layered on top of landing.css (see design doc history)
    neural-bg.js                    # Animated canvas background
    404.qmd
    about/
      index.qmd                     # Mission
      people.qmd, people.yml          # Team roster
      contributors.qmd, contributors.json
      license.qmd
      about.css
    community/
      index.qmd
      events.qmd, events.yml
      partners.qmd, partners.yml
      community.css
    covers/
      cover-hardcover-book-vol1.png/.webp
      cover-hardcover-book-vol2.png/.webp
    newsletter/
      index.qmd
      posts/YYYY/                     # Archived, synced-back sent issues
      pyproject.toml                   # "CLI for the ML Systems newsletter. Drafts in git, send via Buttondown."
      cli/
        main.py
        core/
          buttondown.py                 # Thin REST client for api.buttondown.com/v1
          config.py                      # Loads BUTTONDOWN_API_KEY from env or .env
        commands/
          new.py, check.py, push.py, pull.py, list.py, status.py,
          diff.py, open.py, archive.py, set_author.py, base.py
        .env.example
      bin/news                          # Zero-install CLI wrapper
      README.md
    games/
      index.qmd                        # "MLSysBook Playground"
      lander.qmd, pipeline.qmd, oom.qmd, allreduce.qmd, batch.qmd,
      checkpoint.qmd, cluster.qmd, kvcache.qmd, loader.qmd, moe.qmd,
      prune.qmd, quantization.qmd, roofline.qmd, topology.qmd
    config/
      announcement.yml
    assets/
  shared/
    config/
      navbar-common.yml
      footer-site.yml               # "SHARED FOOTER - Site Subsites"
      site-head.html
    scripts/
      subscribe-modal.js              # Canonical source; mirrored into sibling projects
      sync-mirrors.sh                  # Propagates the canonical copy; supports --check
    styles/
      style-site.scss, dark-mode-site.scss
  .github/workflows/
    site-validate-dev.yml
    site-preview-dev.yml
    site-publish-live.yml
    sync-newsletter.yml
```

---

## 1. `_quarto.yml`: real configuration

```yaml
# Single Quarto project for: landing page, about, community, newsletter

project:
  type: website
  output-dir: _build
  resources: [assets/**]

website:
  title: "Machine Learning Systems"
  site-url: https://mlsysbook.ai/
  favicon: ...
  google-analytics: "G-M21L0CBCVN"
  navbar:
    # Home mega-menu: Landing, Mission, People, Contributors, Community, Events, Newsletter
  sidebar: []   # no book-style sidebar; this is a marketing/content site, not a docs site

metadata-files:
  - ../shared/config/navbar-common.yml
  - ../shared/config/footer-site.yml
  - config/announcement.yml

format:
  html:
    theme:
      light: [../shared/styles/style-site.scss]
      dark: [../shared/styles/style-site.scss, ../shared/styles/dark-mode-site.scss]
    include-in-header: ../shared/config/site-head.html
    # loads /assets/scripts/subscribe-modal.js site-wide
```

Open Graph and Twitter card images point at `site/covers/cover-hardcover-book-vol1.png`, so a shared link to the landing page previews with the actual book cover, not a generic placeholder.

---

## 2. The landing page: real front-end code

### 2.1 `neural-bg.js`

```js
// Mounts into <div id="mls-neural-bg"></div> (referenced from index.qmd).
// Draws a grid of colored squares that pulse via sin()-based opacity animation.
// Throttled to roughly 30fps: skips a frame if timestamp - lastTime < 33.
// Separate lightColors / darkColors palettes.
// A MutationObserver watches data-bs-theme on <html> and swaps palettes live
// when the reader toggles light/dark mode, no reload needed.
// Resize handling is tuned to ignore mobile-browser chrome show/hide,
// which otherwise fires spurious resize events on scroll on some phones.
```

### 2.2 `landing.css` and `landing-v3.css`

`index.qmd` loads both, in this order, and applies both CSS classes to the page body:

```html
&lt;link rel="stylesheet" href="landing.css"&gt;
&lt;link rel="stylesheet" href="landing-v3.css"&gt;
&lt;body class="mls-landing mls-landing-v3"&gt;
```

`landing-v3.css`'s own header comment states it is layered on top of `landing.css` (inherits all base styles and demo-component CSS), and documents removing mandatory scroll-snap behavior specifically because it fought iOS Safari's momentum scrolling, keeping only `scroll-behavior: smooth` and `scroll-padding-top` from the base file. If you're touching landing-page styling, read both files; `landing-v3.css` alone is not the complete picture.

---

## 3. The newsletter CLI (`site/newsletter/`)

### 3.1 The Buttondown client (`cli/core/buttondown.py`)

```python
API_BASE = "https://api.buttondown.com/v1"

def upload_image(...): ...      # POST /images
def create_draft(...): ...       # POST /emails, status: draft
def list_emails(...): ...         # GET /emails, paginated
def get_email(email_id): ...       # GET /emails/{id}
def update_email(email_id, ...): ... # PATCH /emails/{id}, e.g. to set metadata.author
```

A thin, direct REST wrapper, no local caching or ORM layer. `cli/core/config.py` loads `BUTTONDOWN_API_KEY` from the environment or from `cli/.env` (gitignored; `cli/.env.example` documents the expected shape).

### 3.2 Commands (`cli/commands/`)

| Command | What it does |
|---|---|
| `new` | Scaffolds a new draft post file. |
| `check` | Validates a draft's shape before pushing. |
| `push` | Uploads a draft to Buttondown as a draft email via `create_draft`. |
| `pull` | Syncs sent emails back from Buttondown into `posts/YYYY/`, the mechanism behind the automated daily sync (see section 5.1). |
| `list` | Lists emails (local drafts and/or remote). |
| `status` | Shows the current state of a draft (pushed, sent, synced). |
| `diff` | Shows the difference between a local draft and its remote Buttondown counterpart. |
| `open` | Opens a draft in Buttondown's own web editor. |
| `archive` | Archives a post locally. |
| `set_author` | Sets the `metadata.author` field on a remote email via `update_email`. |

Entry point: `site/newsletter/bin/news`, a zero-install wrapper script delegating to `cli.main:main`. The project's own README documents the full author-push-send-pull workflow and explicitly states it's "designed to mirror the Tito CLI" (TinyTorch's own CLI), the same monorepo-wide pattern of a small, purpose-built Python CLI per sub-project.

### 3.3 The front-end subscribe form

`shared/scripts/subscribe-modal.js` posts directly to Buttondown's embeddable subscribe endpoint from the browser:

```js
// form action="https://buttondown.email/api/emails/embed-subscribe/mlsysbook"
// fields: metadata__role, metadata__organization, ...
// hidden: tag=mlsysbook-site
```

This means a visitor subscribing never touches this project's own backend or the newsletter CLI at all; the browser talks to Buttondown directly. The script is loaded site-wide via `_quarto.yml`'s `include-in-header` (`/assets/scripts/subscribe-modal.js`).

---

## 4. `site/games/`: the mini-games section

`games/index.qmd` is the section landing page ("MLSysBook Playground"), marked early development. Each game is its own `.qmd` page paired with its own script; a representative sample:

- `lander.qmd` (Gradient Lander): a Lunar-Lander-style game balancing batch size and learning rate.
- `pipeline.qmd` (Pipeline Pacer): pipeline-parallelism bubble scheduling.
- `oom.qmd` (Tensor Tetris): GPU memory packing.

Every game page explicitly links back to the specific textbook chapter covering its underlying concept (for example, Gradient Lander links to the Vol II chapter on large-batch training and convergence). There is no shared game engine or framework across the games as of this document; each is its own self-contained implementation, so don't assume changing one changes the pattern for the others.

---

## 5. CI/CD implementation notes

### 5.1 `sync-newsletter.yml`: the automated Buttondown sync

Triggers daily at 06:00 UTC via cron, or manually. The job runs `news pull` against the Buttondown API (using the `BUTTONDOWN_API_KEY` secret), commits any newly synced posts under `site/newsletter/posts/` to `dev`, cherry-picks that same commit onto `main`, and then dispatches `site-publish-live.yml` against `main` so a newly sent newsletter issue goes live on the site without any manual step. This is the concrete mechanism behind the "sent posts automatically synced back" goal in the design doc.

### 5.2 `site-validate-dev.yml`

Triggers on `workflow_dispatch`, `workflow_call`, and on pull requests and pushes to `dev` touching `site/**` or `shared/**` (both paths matter, since this site depends on the shared configuration layer). Jobs: `build-site` (installs newsletter CLI dependencies without hitting the real Buttondown API, runs `quarto render`, and validates that `index.html`, `about/index.html`, `community/index.html`, and `newsletter/index.html` all exist), `check-links` (non-blocking, via the shared `infra-link-check.yml` reusable workflow), and `summary`.

### 5.3 `site-preview-dev.yml`

Triggers on push to `dev` touching `site/**` or `shared/**`, or manually. Gated on `site-validate-dev.yml`. Syncs the newsletter via `news pull`, renders the site, rewrites URLs for the dev-preview base path, re-validates the same critical pages, updates the announcement banner, then SSH-deploys to a separate dev-preview repository's `main` branch.

### 5.4 `site-publish-live.yml`

`workflow_dispatch` or `workflow_call` only (no direct push trigger; both the manual publish path and the automated newsletter-sync path go through this same workflow). Gated on `site-validate-dev.yml` being green via the shared `infra-publish-guard.yml`. Syncs the newsletter, renders the site, re-validates critical pages, then deploys to the `gh-pages` branch: it copies `about/`, `community/`, `newsletter/`, the landing root, and the shared `site_libs/` directory, while explicitly preserving every other sub-project's already-deployed subdirectory on `gh-pages` (`book`, `kits`, `tinytorch`, `labs`, `slides`, `instructors`) rather than overwriting the whole branch. Deploy target: `mlsysbook.ai/{about,community,newsletter}/` and the root landing page.

---

## 6. Shared assets: the mirror-and-sync pattern

`shared/scripts/subscribe-modal.js` is the canonical source, per its own header comment, propagated to sibling Quarto projects (currently including the slides and instructor sites, and noted as intentionally *not* mirrored into TinyTorch's own separate copies, which are deliberately kept independent) as real file copies rather than symlinks, since Quarto's resource-copy step during a GitHub Pages deploy preserves symlinks as symlinks rather than dereferencing them, which breaks the deployed site. `shared/scripts/sync-mirrors.sh` performs the actual copy and supports a `--check` mode (comparing mirrors against the canonical source without writing) intended for CI use. If you're editing the canonical script, check whether `--check` is actually wired into a CI workflow before assuming staleness elsewhere would be automatically caught; verify this directly against the current workflow files rather than assuming, since this is exactly the kind of gap called out in the design doc's "Known issues."

---

## 7. Local development workflow

1. `cd site && quarto preview`. No install step for static content; Quarto is the only requirement.
2. For newsletter CLI work: `cd site/newsletter`, set up a Python environment, copy `cli/.env.example` to `cli/.env` and fill in a real `BUTTONDOWN_API_KEY` if you need to test `push`/`pull` against the real API (most authoring and validation commands don't need one).
3. For shared-asset changes (`shared/scripts/subscribe-modal.js`, `shared/config/*`, `shared/styles/*`): after editing the canonical file, run `shared/scripts/sync-mirrors.sh` and confirm every mirror location is updated in the same commit, don't rely on CI to catch a forgotten sync unless you've confirmed the `--check` mode is actually wired into a workflow.
4. For a mini-game: work directly in its `.qmd`/script pair under `games/`; there's no shared build step beyond the normal Quarto render.

---

## 8. Common contribution workflows

### Editing landing-page content or styling

1. Content changes go in `index.qmd`. Styling changes need you to check both `landing.css` and `landing-v3.css`, remember the newer file only overrides specific things and inherits the rest from the older one.
2. If your change touches the animated background, `neural-bg.js` is a single, self-contained file; test it in both light and dark mode, since it swaps palettes live via a `MutationObserver`, and on a real mobile browser if you're touching resize handling, since that logic is specifically tuned around a mobile Safari quirk.

### Drafting and sending a newsletter issue

1. `cd site/newsletter && ./bin/news new` (or the equivalent current command; check `cli/commands/new.py` and the newsletter README for the exact current invocation) to scaffold a draft.
2. Write the post content, then `news check` to validate its shape.
3. `news push` to upload it to Buttondown as a draft.
4. Send the issue from Buttondown's own interface, not from this repository.
5. The daily `sync-newsletter.yml` cron job will pull the sent issue back into `posts/YYYY/` and publish it automatically; you don't need to do anything further. If you need it live sooner, the workflow can be triggered manually.

### Adding a new mini-game

1. Create a new `.qmd` page under `games/`, following the pattern of an existing game (a self-contained script, a short description, and an explicit link back to the textbook chapter the game is teaching).
2. Add it to `games/index.qmd`'s listing.
3. Since there's no shared game engine, you're free to implement the game however makes sense for its specific mechanic, but keep it self-contained and dependency-light, consistent with every existing game.

### Working on the shared configuration layer

1. Understand that a change to `shared/config/navbar-common.yml`, `shared/config/footer-site.yml`, `shared/styles/style-site.scss`, or `shared/scripts/subscribe-modal.js` doesn't just affect this project, it affects every sibling Quarto site pulling from the same shared layer (at minimum the slides and instructor sites for the navbar/footer/theming files).
2. For `subscribe-modal.js` specifically, always run `shared/scripts/sync-mirrors.sh` after editing the canonical copy, and check that every mirrored location's diff is included in your commit.
3. Test your change against at least two of the consuming sites, not just this one, before considering it done.
