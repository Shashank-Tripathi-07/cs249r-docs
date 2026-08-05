# Landing and Community Site: Design

*This is the contributor-facing design document for the landing and community site sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `site/` in that repo. It explains what this site is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers a real front-end redesign decision, and "Known issues" lists what's worth knowing before you touch this code.*

## Problem

Every sub-project in the MLSysBook ecosystem, the book, TinyTorch, the labs, the hardware kits, the interview-prep app, and more, is its own Quarto (or otherwise built) site. None of them is the right place for the ecosystem's actual front door: a landing page explaining what the whole project is, a place to learn about the people and mission behind it, a community hub, and a way to stay in touch via a newsletter. Without a dedicated project for that, every visitor's first experience with the ecosystem would depend on which sub-project they happened to land on first.

This project is that front door: the public landing and community experience at `mlsysbook.ai`, home, about, community, and newsletter, plus a small set of experimental interactive mini-games that make systems concepts tangible in a way static prose can't.

## Goals

- A polished, animated landing page at the ecosystem's root domain that explains what MLSysBook is and links out to every sub-project (the book, TinyTorch, the labs, hardware kits, slides, the instructor site, StaffML), since this is most visitors' actual entry point into the whole ecosystem.
- An about section covering the project's mission, the people and contributors behind it, and licensing.
- A community section covering events and partner organizations.
- A working, low-maintenance newsletter pipeline: authors draft posts as plain files in git, push them to a real email service for delivery, and have sent posts automatically synced back into the site's own archive, without anyone needing to hand-copy content between two places.
- A small set of experimental, playable browser mini-games that turn ML systems concepts (GPU memory packing, pipeline-parallelism bubbles, batch-size tradeoffs, and others) into short interactive loops, each explicitly tied back to the textbook chapter that covers the underlying concept.
- Shared visual and navigational consistency with the rest of the ecosystem's Quarto sites, via a common configuration layer, without needing to hand-maintain that consistency separately in every sub-project.
- Free and open, published as a fully public site with standard web analytics, no login or gating.

## Non-goals

- Not itself a content-authoring surface for the textbook, labs, or any other sub-project; it only links to and describes them.
- Not a custom-built email delivery system. Newsletter sending is deliberately delegated to a real, existing third-party email service rather than built in-house.
- Not a mature, fully built-out games platform. The mini-games section is explicitly marked as early development in the site's own content; it's a set of small, self-contained interactive demos, not a persistent, accreted game with saved state across sessions or a shared game engine.

## Technology stack

| Technology | What it is | How this project uses it |
|---|---|---|
| Quarto | A publishing system built on Pandoc. | Builds the entire site, landing, about, community, newsletter, and games, as one Quarto website project. |
| A shared, ecosystem-wide Quarto configuration and styling layer (`shared/`) | YAML, SCSS, and JS assets shared across several sibling Quarto projects in this monorepo. | Supplies the common navbar, a site-specific footer, shared theming (light and dark SCSS), and a shared subscribe-modal script, so this site (and its siblings, the slides and instructor sites) don't each maintain their own copy of the same navigation and branding logic. |
| Vanilla JavaScript (a `Canvas`-based animation, no framework) | Plain, dependency-free client-side scripting. | Powers the landing page's animated background, a pulsing, theme-aware grid effect, along with the interactive mini-games, each implemented as its own small, self-contained script. |
| Buttondown | A third-party email newsletter service, with a REST API. | The actual delivery mechanism for the project's newsletter. This site's own newsletter tooling is a thin client around Buttondown's API, not an email-sending system of its own. |
| A small Python CLI, built specifically for this project's newsletter workflow | A custom command-line tool, modeled deliberately on this monorepo's other project CLIs (for example TinyTorch's `tito`). | Manages the full newsletter authoring lifecycle: drafting posts as files in git, pushing them to Buttondown, and pulling sent posts back into the site's own archive. |
| GitHub Actions | GitHub's built-in CI/CD platform. | Runs content validation and link checking on every relevant change, drives dev-preview and production-publish deployments, and runs a daily scheduled job that syncs newly sent newsletter issues from Buttondown back into the repository automatically. |

## Architecture

### Site structure

Five content areas, each its own top-level directory: `about/` (mission, people, contributors, license), `community/` (a community hub, events, and partner organizations), `newsletter/` (an archive of past issues, plus the authoring CLI described below), `covers/` (static book-cover images used for social sharing previews), and `games/` (the interactive mini-games section). The landing page itself (`index.qmd`) sits at the site's root, distinct from all of these, and is the page most visitors will actually see first.

### The landing page and its animated background

The landing page loads two layered stylesheets, an older, broader base stylesheet and a newer one that's explicitly documented as layering on top of it rather than replacing it (see "Project history" for why). A small, dependency-free JavaScript module renders an animated canvas background behind the page content, a grid of colored cells that pulse gently over time, with separate color palettes for light and dark mode that swap live if the reader toggles the site's theme, and animation throttled to a fixed frame rate so it doesn't compete for resources with the rest of the page.

### The newsletter pipeline

This is the one part of the site with real backend logic behind it, rather than being purely static content. The workflow is designed to mirror how a developer would work with git, not how a typical newsletter tool works:

1. An author drafts a new post as a plain file in the repository, using the project's own small CLI.
2. The CLI pushes that draft to Buttondown (the actual email-delivery service) via its API.
3. Once the author sends the issue (from Buttondown's own interface, not from this repository), a scheduled, automated job pulls the sent issue's content back into the repository, committing it as a permanent, versioned copy in the site's own newsletter archive.
4. That commit is automatically propagated to the production branch and triggers a live site redeploy, so a newly sent newsletter issue appears in the site's public archive without anyone needing to manually copy content between Buttondown and the repository.

A separate, site-wide subscribe modal (shared with sibling Quarto sites via the common configuration layer) posts directly to Buttondown's own embeddable subscription endpoint from the browser, so a visitor subscribing to the newsletter never touches this project's own backend at all.

### The mini-games section

Each game is a small, focused, browser-playable simulation of one specific ML systems concept, deliberately framed as a nostalgic "arcade" homage (a Lunar-Lander-style game for batch-size and learning-rate balancing, a Tetris-style game for GPU memory packing, and others), each with an explicit callout linking back to the specific textbook chapter that covers the underlying concept. This section is explicitly marked as early development in its own content; it's meant to demonstrate the idea and provide a few working examples, not to be a comprehensive, polished game library yet.

### Shared configuration with sibling sites

This project deliberately does not maintain its own navbar, footer, or base theming independently. It pulls a common navbar definition, a site-specific footer, shared light and dark SCSS theming, and a shared `&lt;head&gt;` include from a monorepo-level `shared/` directory (a sibling to this project, not inside it), the same shared layer the slides and instructor sites also pull from. The subscribe-modal script specifically is documented as having one canonical source location, propagated to sibling projects as real file copies (not symlinks, since Quarto's own resource-copying step doesn't dereference symlinks the way a typical deploy pipeline would expect) via a small sync script that a maintainer runs manually and that CI checks for staleness.

## Known issues

- **The subscribe-modal script and other shared assets are propagated to sibling projects as manually-synced file copies, not symlinks or a shared package.** This is a deliberate choice (symlinks don't survive Quarto's resource-copy step cleanly for GitHub Pages deployment), but it means a change to the canonical shared script requires a maintainer to remember to run the sync script and commit the result everywhere it's mirrored; nothing prevents a mirror from silently going stale between syncs the way, for example, the Socratiq project's bundle-drift CI check prevents that specific kind of staleness for its own artifact. If you're touching `shared/scripts/subscribe-modal.js`, check whether the sync script's own check mode is wired into CI before assuming a drift would be caught automatically.
- **The landing page's CSS is layered across two files rather than consolidated into one**, an older base stylesheet plus a newer one that explicitly builds on top of it. This is a known, deliberate state (see "Project history"), not an accident, but it does mean a future contributor touching landing-page styling needs to check both files to understand the full picture, rather than assuming one file is the complete source of truth.
- **The games section is explicitly early-stage.** Don't assume feature parity or a shared architecture across every game listed; each is documented as its own small, self-contained interactive loop.

## Project history

- **The landing page went through a deliberate v2-to-v3 redesign that removed scroll-snapping.** The earlier version used mandatory scroll-snap behavior between page sections. The newer stylesheet's own header comment documents why that was removed: it fought iOS Safari's momentum scrolling and would yank a reader away from content they were still reading partway through a section on mobile. The desktop visual polish scroll-snap provided wasn't judged worth that mobile cost, so it was removed, while the newer file was deliberately layered on top of the older one (keeping its other base styles) rather than being a full rewrite, which is why the site currently loads two stylesheets in sequence instead of one.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, the real newsletter CLI commands, the shared-configuration mechanism, local setup steps, and common contribution workflows for adding content, editing the landing page, or working on a mini-game. The "Known issues" list above is a reasonable place to find a first task, particularly checking whether the shared-asset sync script has a CI-enforced check mode.
