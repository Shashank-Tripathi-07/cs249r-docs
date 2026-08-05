# StaffML: Design

*This is the contributor-facing design document for StaffML, a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository). It explains what StaffML is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the app as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-04); the "Project history" section at the end covers the pivots and bugs that shaped the current design, and "Known issues" lists what still needs work, both good places to look for your first contribution.*

## Problem

Machine-learning-systems interview prep exists mostly as generic LeetCode-style question banks (algorithmic, not systems-focused) or as unstructured "system design" blog posts with no verification, no progression, and no way to self-check quantitative reasoning. There is no free, structured, physics-grounded question bank for the specific skill of ML-systems interviewing: reasoning about compute, memory, and bandwidth tradeoffs, hardware constraints, and distributed-training or serving architecture with actual napkin math, not hand-waving.

StaffML is that question bank plus a practice app: roughly 9,500 questions spanning five deployment tracks (cloud, edge, mobile, tinyml, global) and six Bloom's-taxonomy-mapped difficulty levels (L1 recall through L6+ staff-level synthesis), each with a canonical answer, a common-mistake explanation, and, where applicable, a worked napkin-math derivation. It runs entirely client-side, is free, and requires no account.

## Goals

- A large (~9,500 question), physics-grounded, quantitatively verifiable ML-systems interview question corpus, organized by track, level, zone (cognitive-skill type), competency area, and topic.
- Multiple practice modes: untimed single-question drilling (Practice), timed multi-question mock interviews (Gauntlet), a conversational AI-driven mock interview (Live Interview), curated and custom study plans, and a visual corpus explorer.
- Runs entirely client-side as a static export, so no backend is required to *use* the core practice loop. The corpus ships bundled in the client, with an optional Cloudflare Worker for full-text search, per-question detail hydration, and offline caching.
- Local, private, no-account progress tracking (spaced repetition via SM-2, streaks, heat maps), all in `localStorage`, exportable and importable as JSON, never sent to a server.
- An AI-assisted "Ask Interviewer" clarification feature during practice, with a strict Socratic constraint (the AI helps clarify constraints, never solves the problem), backed by a multi-provider LLM proxy Worker with a local-journal fallback when no hosted endpoint is configured.
- A rigorous, versioned, schema-driven content pipeline (author, validate, build, publish) so the corpus stays internally consistent (chain progressions, taxonomy membership, provenance tracking) as it grows through both human authors and LLM-assisted generation.
- Free and open source (CC-BY-NC-4.0, matching the parent `cs249r_book` license), deployable by anyone who forks the repository.

## Non-goals

- No user accounts and no server-side persistence of personal progress. This is a deliberate privacy stance, not a missing feature. Progress lives in the browser only.
- No payment or subscription infrastructure for the core app (a "would you pay for a hosted-AI tier" waitlist exists, collecting signal only; see `AskInterviewer.tsx`'s `WaitlistModal`).
- No human-to-human mock interview scheduling or matching.
- No native mobile app. The web app is responsive but is not packaged for app stores.
- No video or audio interview simulation. Live Interview is text-chat only.
- The AI interviewer is explicitly not meant to solve problems for the candidate. Every LLM-facing prompt in the codebase (`AskInterviewer.tsx`'s `SOCRATIC_PROMPT_FOR_COPY`, and the worker-side equivalent) enforces this as a hard constraint, not a suggestion.

## Technology stack

Everything StaffML uses, what it is, and why it's the right tool for this project. If you're contributing for the first time, skim this before diving into the implementation doc.

### Frontend

| Technology | What it is | How StaffML uses it |
|---|---|---|
| Next.js 16 (App Router) | A React framework that handles routing, bundling, and build tooling. | Builds the whole app with `output: 'export'`, meaning Next.js produces a fully static site (plain HTML, CSS, and JS files) with no server runtime. That static output is what gets deployed to GitHub Pages. |
| React 19 | A UI library for building interfaces out of reusable components. | Every page and component in `interviews/staffml/src/` is a React component. All routes are marked `"use client"`, so there are no server components; everything renders in the browser. |
| TypeScript | A typed superset of JavaScript. | The entire codebase is TypeScript. Shared types (like the `Question` interface) are the contract between the frontend, the Cloudflare Workers, and the content pipeline's codegen output. |
| Tailwind CSS v4 | A utility-class CSS framework. | Used for all styling, including dark and light theming (see "Chrome, theming, accessibility" below). |
| `framer-motion` | An animation library for React. | Powers transitions and micro-interactions across the UI (card reveals, modal entrances, and similar). |
| `lucide-react` | An icon library. | Supplies the icon set used throughout the app. |
| `katex` | A math typesetting library. | Renders LaTeX-style math (`$...$` and `$$...$$`) inside question scenarios and solutions. It's hand-integrated into `components/MarkdownText.tsx` rather than pulled in through a markdown library, since StaffML doesn't use full markdown rendering. |
| `react-medium-image-zoom` | A click-to-zoom image component. | Used for question diagrams and figures. |
| `js-quantities` | A unit-parsing and unit-conversion library. | Powers the unit-aware napkin-math grading in `lib/corpus.ts`: if a student's numeric answer and the canonical answer both parse as physical quantities with compatible dimensions (for example, both are times, or both are bytes), it converts them to the same base unit before comparing. |
| `graphology`, `sigma`, `@react-sigma` | Graph data-structure and graph-visualization libraries. | Installed for future taxonomy-graph visualization work (see `graph.py` in the content pipeline) but not currently wired into any shipped page. |

### Backend and infrastructure (Cloudflare)

| Technology | What it is | How StaffML uses it |
|---|---|---|
| Cloudflare Workers | A serverless JavaScript/TypeScript runtime that runs at Cloudflare's edge locations. | StaffML runs three independent Workers: `staffml-vault-worker` (question data API), the AI interviewer worker (LLM proxy), and the analytics worker (usage telemetry). Each is optional at runtime; the app degrades gracefully to bundled data and local-only behavior if a Worker is unreachable. |
| Cloudflare D1 | A managed SQLite database that runs at the edge, paired with a Worker. | Stores the compiled question corpus (`questions`, `chains`, `taxonomy`, and related tables) that `staffml-vault-worker` serves from. |
| SQLite FTS5 | SQLite's built-in full-text-search extension. | Powers the `/search` endpoint on the vault worker, with a `LIKE`-based fallback if FTS5 is ever unavailable. |
| Cloudflare KV | A key-value store, also edge-distributed, used by Workers for fast reads. | Backs rate limiting on all three Workers, and additionally backs the waitlist-capture feature on the AI interviewer worker. |
| Cloudflare Workers AI | Cloudflare's own hosted inference platform. | Serves as the last-resort LLM provider in the AI interviewer worker's provider chain, so the feature can still work even if no external LLM API key is configured. |
| GitHub Pages | Static site hosting built into GitHub. | Hosts the built Next.js static export, both the production site and the dev preview. |

### Content pipeline

| Technology | What it is | How StaffML uses it |
|---|---|---|
| Python | A general-purpose programming language. | The language the entire content pipeline (`interviews/vault-cli/`) is written in. |
| Typer | A Python library for building command-line tools. | The `vault` CLI (build, validate, author, and release the corpus) is a Typer app. Run `vault --help` to see every subcommand. |
| LinkML | A schema modeling language for describing structured data, and the tooling that generates code from that schema. | `interviews/vault/schema/question_schema.yaml` is the single source of truth for what a "question" is. Pydantic models, the D1 table definitions, and the frontend's TypeScript types are all generated from this one file via `vault codegen`, so the data shape can never drift between the three. |
| Pydantic | A Python data-validation library. | Every question YAML file is validated against Pydantic models generated from the LinkML schema before it can be built into the corpus. |
| YAML | A human-readable data-serialization format. | Every question is authored as one YAML file (see the content pipeline section below for a real example). |

### AI providers

| Provider | How StaffML uses it |
|---|---|
| Groq, OpenAI, Anthropic, Gemini, OpenRouter | Configured, in that priority order (overridable), as the LLM backends behind the AI interviewer worker's Socratic clarification and Live Interview features. The worker tries each in order and uses whichever is configured and available. |
| Cloudflare Workers AI | The final fallback in that same provider chain, so the AI features still function with zero external API keys configured. |
| Gemini | Also used offline (not at runtime) by the content pipeline for LLM-assisted question drafting (`vault generate`), chain-progression generation, and corpus quality audits. These are maintainer-run, opt-in tools, not something the deployed app calls. |

### Testing and CI/CD

| Technology | What it is | How StaffML uses it |
|---|---|---|
| Vitest | A JavaScript/TypeScript test runner, built to work well with Vite-style tooling. | Runs the frontend's unit and component tests (`src/__tests__/`) and the vault worker's own test suite. |
| jsdom | A JavaScript implementation of the DOM, used for testing without a real browser. | The environment Vitest's component tests render into. |
| Playwright | A browser-automation framework for real, headless browsers. | Runs the frontend's end-to-end specs, the project's various ad-hoc audit scripts, and (via a separate Python driver) part of CI's smoke-testing stage. |
| pytest | Python's standard test framework. | Runs the content pipeline's (`vault-cli`) own test suite. |
| GitHub Actions | GitHub's built-in CI/CD platform. | Runs every validation workflow and deploy job described later in this document. |

## Architecture

### Data model

The question schema lives in one place, `interviews/vault/schema/question_schema.yaml` (LinkML, v1.0.0), and every other representation of a question (Pydantic validation models, the D1 `questions` table DDL, the TypeScript `Question` interface in `interviews/staffml/src/lib/corpus.ts`, the bundled JSON) is derived from or kept in sync with it.

**Classification axes** (every question is tagged on all of these):
- `track`: `tinyml | edge | mobile | cloud | global` (deployment context).
- `level`: `L1..L5, L6+`, Bloom's-taxonomy-mapped difficulty (L1 = recall/entry, L6+ = staff/principal-level synthesis). See `lib/levels.ts` for the full ladder with human-role mappings.
- `zone`: 11 values, four "pure" cognitive-skill zones (`recall, analyze, design, implement`), six compound zones (`fluency, diagnosis, specification, optimization, evaluation, realization`), and `mastery`.
- `competency_area`: a closed 13-value enum (`deployment, parallelism, networking, latency, memory, compute, data, power, precision, reliability, optimization, architecture, cross-cutting`), locked down after roughly 40 items had been mis-tagged with free-text values (see "Project history").
- `bloom_level`: `remember | understand | apply | analyze | evaluate | create`.
- `phase`: `training | inference | both`.
- `topic`: a string that must exist in the 87-topic taxonomy (`interviews/vault/schema/staffml_taxonomy.yaml` / `taxonomy_data.yaml`).

**Content fields**: `title` (up to 120 characters), `scenario` (required, at least 30 characters, the setup text), `question` (optional, up to 200 characters, an explicit interrogative), `visual` (an optional SVG diagram reference; `mermaid` support was removed in v0.1.2, so diagrams are SVG-only now), and a required `details` block: `realistic_solution` (the canonical answer), `common_mistake` (optional but format-enforced, must contain, in order, the literal markers `**The Pitfall:**` then `**The Rationale:**` then `**The Consequence:**`), `napkin_math` (optional, format-enforced: `**Assumptions...** **Calculations:** ... **Conclusion...**`), and optionally `options`/`correct_index` (exactly 4 MCQ options, 0-indexed answer) and `resources` (external links, HTTPS-only).

**Workflow and provenance fields**: `status` (`draft | published | flagged | archived | deleted`), `provenance` (closed enum: `human | llm-draft | llm-then-human-edited | imported`), plus independently-tracked LLM validation lineage (`validated`, `validation_status`, `validation_model`, and related fields), a separate math-verification pass (`math_verified`, `math_status`, `math_model`, and related fields), and human review state (`human_reviewed: {status, by, date, notes}`, distinct from LLM validation; a question can be LLM-validated without ever having been human-reviewed, or vice versa).

**Chains**: a chain is a pedagogically-ordered sequence of questions on one topic, usually spanning increasing difficulty (for example, the same scenario asked at L2 "understand," L3 "apply," and L5 "evaluate"). Roughly 32% of the corpus participates in at least one chain. As of the v1.1 pipeline architecture, chain membership is a sidecar rather than a per-question field: `interviews/vault/chains.json` (a JSON array of `{chain_id, track, topic, competency_area, levels, questions: [{level, id, title, bloom}], rationale, tier: "primary"|"secondary"}` objects) is authoritative, and the build pipeline joins it against the question YAMLs to produce each question's `chain_ids`/`chain_positions`/`chain_tiers`. The LinkML schema still declares a `Question.chains` field that the pipeline no longer reads; that's a known, intentional inconsistency, since the schema hasn't yet been pruned of the now-vestigial field.

**Scale** (`interviews/vault/questions/`, one file per question): roughly 10,700 question YAML files across all tracks (cloud: 4,368, edge: 2,371, mobile: 2,009, tinyml: 1,535, global: 428), of which the published, release-eligible subset (after `release-policy.yaml` filtering) is what actually ships to the client, reported via `lib/stats.ts`'s `QUESTION_COUNT` as "9,500+" in the UI.

**On-disk ID integrity**: `interviews/vault/id-registry.yaml` is an append-only log (one `{id, created_at, created_by}` entry per line) of every question ID ever minted, enforced append-only by CI (it rejects line deletions), which prevents ID reuse or collision across the corpus's history.

### The three-tier data pipeline

Question content flows through three representations, each optimized for a different consumer:

1. **Source of truth**: YAML files under `interviews/vault/questions/`, validated against the LinkML schema via Pydantic models, edited through `vault` CLI authoring commands (`vault new/edit/rm/move`), which enforce validation and typed-confirmation safety rather than allowing raw file edits.
2. **Compiled artifact**: `vault build` (`interviews/vault-cli/src/vault_cli/commands/build.py`) reads all YAML, validates it, and compiles a SQLite database (`vault.db`) plus a JSON manifest (`interviews/staffml/src/data/vault-manifest.json`: release ID, release hash, question and chain counts, track and level distributions). This `vault.db` is what `interviews/staffml-vault-worker` is loaded from at deploy time (via D1), and what `vault api`/`vault serve` use to mirror the production API locally for development.
3. **Client bundle**: two JSON shapes are emitted for the frontend, both derived from the same build:
   - `corpus-summary.json` (production bundle, roughly 2.9 MiB): every question's classification fields plus MCQ options and correct-index, but `scenario`/`details.*` prose fields are empty-string stubs (roughly 80% smaller than the full corpus). This is what ships in every production build (`vault build --site-bundle`).
   - `corpus.json` (local development only, roughly 20 MiB or more): the same questions with full prose fields populated, written by `vault build --local-json`, served from `public/data/corpus.json`, and consumed only when `NEXT_PUBLIC_VAULT_FALLBACK=static` is set (which `.env.development` does automatically). This exists so local YAML edits are visible in `npm run dev` without waiting for a Worker deploy.

In production, the summary bundle ships in the client and the heavy prose fields (`scenario`, `details.*`) are lazily fetched per-question from the Cloudflare Worker's `/questions/:id` endpoint the first time a question is opened, and cached in memory for the session (see "Client data layer" below).

### Backend: `staffml-vault-worker` (Cloudflare Worker + D1)

Endpoints, all `GET` (`interviews/staffml-vault-worker/src/index.ts`):

| Path | Purpose |
|---|---|
| `/manifest` | Release metadata (release_id, release_hash, schema fingerprint status), `If-None-Match`-aware |
| `/questions/:id` | Single question's full detail (denormalized flat row, re-nested client-side into `{details:{...}}`) |
| `/questions` | Filtered/paginated list (`track`, `level`, `zone`, `topic`, `status`), keyset pagination via `cursor` |
| `/search` | Full-text search (FTS5 with a `LIKE` fallback if FTS5 is unavailable) |
| `/chains/:id` | One chain's ordered questions |
| `/taxonomy` | Topics grouped by area plus zone definitions |
| `/stats` | `{ count }` |

**D1 schema** (`migrations/0001_bootstrap.sql`): `questions`, `chains`, `chain_questions` (primary key `(chain_id, question_id)`), `tags` (defined, unused by any current query), `taxonomy`, `taxonomy_edges` (defined, unused), `zones`, `release_metadata`, and an FTS5 virtual table `questions_fts` over `title, scenario, realistic_solution` kept in sync via insert, delete, and update triggers.

**Reliability and safety design**:
- **CORS**: an explicit allowlist (the `CORS_ALLOWLIST` environment variable), fail-closed by design. An empty or missing allowlist emits no `Access-Control-Allow-Origin` header at all rather than falling back to `*`; an origin not on the list is silently omitted (deny by omission), never echoed back incorrectly.
- **Rate limiting**: a KV-backed fixed-60-second-window token bucket, keyed per `(client IP, endpoint class)`. The IP is read only from `CF-Connecting-IP` (never `X-Forwarded-For`, which is spoofable); a missing header fails closed with a 429. There are two classes: `"default"` (60 requests per minute) and `"search"` (10 requests per minute, since FTS queries are more expensive). Documented caveat: the read-then-write pattern isn't atomic across Cloudflare's edge points of presence, so a burst can leak up to two or three times the nominal cap.
- **Caching**: two layers. HTTP `Cache-Control`/`stale-while-revalidate` headers per endpoint (manifest and question responses cache for 1 hour, search for 5 minutes, taxonomy for 24 hours), plus an edge-level Cloudflare Cache API wrap keyed by `/__vault__/{release_id}/...path`, so a new deploy (a new release_id) atomically invalidates the entire cache namespace with no manual purge needed.
- **Schema fingerprint check**: at cold start, and re-checked every 5 minutes if it previously failed, the Worker computes a SHA-256 hash over the normalized DDL of every D1 table, index, trigger, and view (explicitly excluding FTS5's auto-generated shadow tables, since their DDL differs across SQLite versions between the offline compiler and D1's runtime), and compares it against the release's recorded `schema_fingerprint`. On a mismatch, responses are still served (status 200) but carry `X-Vault-Degraded: schema-fingerprint-mismatch`, a soft degradation signal rather than a hard failure.
- A `GRACE_WINDOW_SECONDS` environment variable (600 seconds, both production and staging) is declared for a documented "10-minute cross-release grace window during propagation" but is not referenced anywhere in the current Worker code, a known, unimplemented-but-declared mechanism.
- **Environments**: production (`staffml-vault`) and staging (`staffml-vault-staging`) are separate Worker deployments with separate D1 databases, but they currently share the same KV rate-limit namespace, a known cross-environment coupling worth fixing.

### Backend: the AI interviewer Worker

A materially different, separate Worker (`interviews/staffml/worker/`), not to be confused with the vault worker. It proxies to whichever of six LLM providers is configured (provider-priority order is overridable), enforcing three distinct system personas depending on request mode: a Socratic "clarify constraints, never solve it" interviewer persona (the default), a Tutor persona (post-reveal "study" mode), and a full multi-turn "Live Interview Conductor" persona for the conversational mock-interview flow. Endpoints: `/health`, `/waitlist` (captures "would you pay" signal), `/interview` (a full conductor turn), `/ask` (a single clarifying-question turn). Rate limiting is three-tiered (per-IP-per-hour, per-IP-per-day, and a global-per-day ceiling) and fails closed (503) if its KV binding is missing, stricter than the vault worker, which fails open when its own KV binding is absent. That asymmetry is deliberate: local development without KV should still let you browse and search questions, but it should not silently allow unlimited LLM spend. Its CORS policy is also less strict than the vault worker's: an unrecognized origin still gets `allowed[0]` echoed back rather than the header being omitted.

### Backend: the analytics Worker

The simplest of the three: KV-only (no D1), it stores event batches under `events:{date}:{batchUUID}` keys with a 90-day TTL, batch-per-key specifically to avoid read-modify-write races. It validates every event against an allowlist of known `type` values, caps batch and event sizes, and rejects anything containing an email-like pattern before storage. This is the receiving end of `lib/error-reporter.ts`'s `client_error` events and `lib/analytics.ts`'s general event tracking. A documented header comment mentions persisted `summary:latest`/`meta:total_events` keys that the actual code does not write, aspirational documentation that drifted from the implementation; the real summary endpoint computes aggregates live over the last 7 days on each request instead.

### Client data layer

`lib/corpus.ts` (roughly 960 lines) is the root data module. It imports the bundled `corpus-summary.json` once at module load (a build-time constant, not fetched over the network), and exposes the `Question` type plus filter, search, and selection functions. There are two hydration modes, chosen via `lib/vault-config.ts`'s `getVaultMode()`:

- **`static`** (local development only, via `NEXT_PUBLIC_VAULT_FALLBACK=static`): full question detail comes from `public/data/corpus.json`, fetched once and cached as an in-memory `Map`. The fetch itself is de-duplicated via a shared in-flight promise (`_staticCorpusPromise`), so that a batch hydration (for example, Gauntlet's `Promise.allSettled` over N questions) doesn't trigger N redundant multi-megabyte fetches. (This de-duplication, and switching the batch hydration from `Promise.all` to `Promise.allSettled`, were both bug fixes; see "Project history.")
- **`worker`** (the default, used in production): `getQuestionFullDetail(id)` fetches `${apiBase}/questions/${id}` through `lib/vault-fetch.ts`'s resilient transport (an 8-second per-attempt timeout, up to 2 retries with full-jitter exponential backoff on 5xx, 429, or network errors, and a per-origin circuit breaker with correct single-probe half-open semantics), then re-nests the Worker's denormalized flat row into the `Question.details` shape. Results are cached (`_detailsCache`), and hydration state is tracked explicitly (`_hydratedIds`, rather than inferred from whether `details.realistic_solution` is truthy), since a recall or MCQ question can legitimately have an empty solution, and inferring from truthiness caused those questions to re-fetch on every access.

`lib/corpus-provider.tsx` wraps the whole app in a React context (`useVault()`) that probes the Worker's `/manifest` once on mount (with a 5-second timeout) to decide whether to enable Worker-backed search and hydration, or degrade gracefully to the bundled summary plus client-side substring search (`corpus.ts`'s `searchQuestions`). On success it also registers a service worker for offline API-response caching.

`useFullQuestion(summary)` (`lib/hooks/useFullQuestion.ts`) is the React-idiomatic wrapper most pages use: it returns `{question, status: "loading"|"ready"|"error"}`, re-rendering as hydration resolves, and merges the fetched detail over the summary (summary-only fields survive; Worker fields win on overlap).

### Practice modes

- **`/practice`** (the largest route, roughly 1,530 lines): the core single-question drilling loop. It offers a filterable pool (track, level, area, zone, napkin-only, visual-only, chains-only), answer-then-reveal with a deliberation guard ("Think longer?" confirm if revealing with under 15 seconds elapsed and under 50 characters typed, self-calibrating off once the user demonstrates real engagement once per session), unit-aware napkin-math grading (via `js-quantities`, converting compatible units before relative-error comparison, falling back to a legacy bare-number comparison if units aren't parseable), a heuristically-extracted 2-to-4-item self-assessment rubric (`lib/rubric.ts`, pure regex scoring, no LLM call), an MCQ self-check (explicitly not wired into spaced-repetition scoring, since it's a self-check and the open self-rating remains the sole SR signal), chain badges and navigation for questions that belong to a progression, and a star-gate interstitial (see below) after 5 lifetime reveals.
- **`/gauntlet`** ("Mock Interview"): timed multi-question sessions (Quick, 5 questions in 10 minutes; Standard, 10 questions in 20 minutes; Full, 15 questions in 35 minutes; or a single deep Design question in 15 minutes), with phases `setup`, `active`, `review`, `results`, and a "realism" setting (`strict`/`standard`/`open`) controlling whether Hardware Reference, Napkin Calc, and Ask Interviewer tools are available and how prominently. Time expiry auto-scores all unanswered remaining questions as 0 (skipped) and persists the result.
- **`/interview`** ("Live Interview"): a full conversational AI mock interview. `lib/interview-conductor.ts` (a pure state machine, no I/O) manages chain-aware question progression, tracks per-competency-area performance ratings, and windows the transcript (walking backward from the most recent entry, capped at 48KB, with a synthetic "N earlier exchanges omitted" summary entry) before each turn is sent via `lib/interview-api.ts` to the AI interviewer Worker's `/interview` endpoint. Sessions persist to `localStorage` and offer resume-on-return if interrupted mid-interview.
- **`/plans`**: 7 hardcoded curated study plans (each an ordered competency-area sequence plus target question count and track/level constraints), plus user-buildable custom paths (track, area, starting level, optional target date, with `dailyQuota` computed from days remaining) via `components/plans/PathBuilder.tsx`.
- **`/explore`**: a hand-built polar-coordinate SVG radial visualization (Track to Competency Area to Topic to Question drill-down), with chain-tier filtering (primary versus all).

### Progress tracking (entirely local, no account)

`lib/progress.ts` is the de facto owner of all `localStorage`-based progress state, even though a few keys are written directly by sibling modules (`plans.ts`, `daily.ts`, `star-gate.ts`, `analytics.ts`):

- **Attempts**: a capped (5,000-entry) log of `{questionId, competencyArea, track, level, selfScore(0-3), timestamp}`.
- **Spaced repetition (SM-2)**: the canonical SM-2 algorithm. `updateSRCard(questionId, quality)` maps the app's 0-3 self-score to SM-2's 0-5 quality scale, updates the ease factor (floored at 1.3) via the standard SM-2 formula, and schedules `nextReview` based on repetition count and interval growth.
- **Heat map**: per-competency-area and per-track attempted and correct counts, driving `/progress`'s color-coded grid.
- **Streaks**: day-based, with named milestones at 3, 7, 14, 30, and 100 days.
- **Export and import**: `exportProgress()`/`importProgress()` are transactional. Import validates every field's shape before writing anything, snapshots current values, and rolls back all touched keys if any single write throws.

### AI-assisted features

**Ask Interviewer** (`components/AskInterviewer.tsx`, 793 lines) mounts inside Practice and Gauntlet. It has three theoretical modes (JOURNAL, HOSTED, FALLBACK) based on whether `NEXT_PUBLIC_INTERVIEWER_ENDPOINT` is configured, but since the code defaults to the production Worker URL when that variable is unset, HOSTED is effectively always active; JOURNAL (local-only logging, no AI call) is the dormant fallback path. The Socratic constraint prompt is enforced server-side in the Worker, so it can't be bypassed by a modified client, and is also embedded client-side in the "Copy as prompt" fallback text, so a user pasting the prompt into an external LLM gets the same constraint. A separate Tutor prompt is used post-reveal (study mode), with explicit XML-like delimiters (`<scenario>`, `<canonical_answer>`, `<student_attempt>`) and a matching sanitizer that strips those exact tags from user-controlled text before embedding, mirroring the Worker's own prompt-injection defense. A `WaitlistModal` sub-component captures "would you pay, and how much" signal, falling back to a prefilled `mailto:` link if the Worker call fails for any reason, so no submission is ever silently lost.

### Star gate (growth mechanic)

`lib/star-gate.ts` + `components/StarGate.tsx`: an honor-system "please star the repo" interstitial shown once a user has revealed 5 answers (lifetime, across sessions) and hasn't already starred or dismissed it. Any of the three dismissal paths (starred, "I already starred," or explicit dismiss/Escape) retires the gate permanently; it is not re-shown, and the distinction between them is tracked only for diagnostics, not enforcement. It's fully accessible (proper dialog semantics, focus trap, focus restoration, Escape-to-dismiss); that accessibility work was itself a bug fix (see "Project history").

### Chrome, theming, accessibility

- **`app/layout.tsx`** wires up, in order: `Providers` (theme, corpus context, and toast context, plus error-reporter installation and analytics session-start on mount), `EcosystemBar` (a hand-styled clone of the parent Quarto book site's Bootstrap navbar, built with inline styles specifically to avoid loading Bootstrap CSS), `Nav` (StaffML's own in-app navigation, with a live spaced-repetition due-count badge polled only while the tab is visible), `AnnouncementBar`, the page content, `MaybeFooter` (hidden on full-viewport workspace routes: practice, gauntlet, explore, simulator, roofline), `VersionDriftToast` (detects when a stale tab's baked release is behind the live Worker's manifest and suggests a refresh, never auto-reloading), and globally-mounted `CommandPalette` (Cmd/Ctrl+K, searches pages, topics, and questions, Worker-FTS-backed when available) and `KeyboardShortcutsOverlay` (the `?` key).
- A hand-written Content-Security-Policy `<meta>` tag is used instead of an HTTP header, since GitHub Pages can't set custom headers on a static export. `script-src 'unsafe-inline'` is a documented, accepted residual-risk trade-off, required because Next.js App Router emits inline RSC-streaming scripts that a static export (with no middleware and no per-request nonces) can't otherwise allow.
- Dark and light theme (`ThemeProvider.tsx`) persists to `localStorage['staffml_theme']` and also mirrors to a second key, `quarto-color-scheme`, for cross-site theme interop with the parent Quarto book sites. It deliberately does not honor the OS `prefers-color-scheme` setting, since light is the ecosystem-wide default and dark is opt-in only.
- Markdown-ish text rendering (`components/MarkdownText.tsx`) is hand-rolled regex, not a markdown library. It splits out LaTeX math (`$...$`/`$$...$$`) before any other processing, so bold, code, and strikethrough parsing don't corrupt math bodies, and it specifically avoids treating bare `$3M`-style currency as math (only `$...$` bodies containing a LaTeX signal character are treated as math).

### Frontend route map

14 top-level routes, all client components (`"use client"`, since this is a fully client-rendered static export with no server components in any page body): `/` (Vault home/browser), `/welcome` (first-run landing), `/practice`, `/gauntlet`, `/interview`, `/plans`, `/progress` (personal stats), `/explore` (radial visualizer), `/framework` (Design Grammar reference, corpus-independent, driven by hand-authored primitive/assembly data), `/roofline` (Roofline Model calculator), `/simulator` (distributed-training cluster simulator), `/dashboard` (aggregate anonymous usage analytics, distinct from `/progress`), `/contribute` (community question submission, which opens a GitHub issue URL and performs no backend write), `/about`.

### Deployment

Three deploy targets per merge or release, all via GitHub Actions:

1. **Dev preview** (`staffml-preview-dev.yml`, triggered on push to `dev` touching relevant paths): builds with `vault build --local-json` (so the preview always reflects the current YAML state, not the last release), and deploys via SSH to a separate `DEV_REPO_URL` at `harvard-edge.github.io/{DEV_REPO}/staffml/`. It's gated on two reusable validate workflows passing (`staffml-validate-dev.yml`, `staffml-validate-vault.yml`) before the deploy job runs, closing a previously-real race where preview could deploy on a commit the validate workflows had already failed.
2. **Production** (`staffml-publish-live.yml`, manual `workflow_dispatch` only, requires typing `PUBLISH` to confirm): builds with `vault build --site-bundle` (summary-only, no local corpus.json), runs the full test, typecheck, build, and vault-smoke gauntlet, deploys the static site to `mlsysbook.ai/staffml/` (via the `gh-pages` branch), and in the same run also ships the vault data to Cloudflare D1 and deploys `staffml-vault-worker` itself (`npx wrangler deploy`), so the frontend and its data backend always release together.
3. **Paper PDF** (`staffml-update-paper.yml`): rebuilds a LaTeX research paper describing the corpus, deployed to `mlsysbook.ai/staffml/downloads/StaffML-Paper.pdf`.

Two automation workflows operate on the corpus independently of app deploys: `staffml-auto-pr.yml` (turns a labeled community GitHub issue into a draft question PR), and `staffml-chain-rebuild.yml` / `staffml-audit-corpus-monthly.yml` (Gemini-driven chain-progression regeneration and full-corpus quality audits), both opt-in and manual-trigger-preferred rather than fully automatic, since their diffs need human review before landing.

### CI/CD validation

Two parallel reusable validate workflows run on every relevant PR and push, and are required gates before any deploy:
- **`staffml-validate-dev.yml`** (site-side): typecheck plus unit tests, then build, then a Playwright-based end-to-end smoke test (headless Chromium, added specifically after a hydration bug shipped to production undetected), then corpus-invariant smoke assertions, then a non-blocking link check.
- **`staffml-validate-vault.yml`** (data-side): `vault-cli`'s own lint, type, and test suite, `vault check --strict` (structural invariants), a chain-coverage regression floor per track, build reproducibility, codegen drift detection (making sure the schema and its derived artifacts stay in sync), an append-only registry guard, a reviewer-identity spoof check, and the `staffml-vault-worker`'s own `vitest` suite.

### Testing

- **Unit and component tests** (Vitest with jsdom, 24 files): a mix of accessibility-regression guards (dialog semantics, focus traps, ARIA on every interactive overlay, each added after a specific real bug), timer-cleanup regression guards (a leaked `setTimeout`/`setInterval` after unmount), pure-logic tests (SM-2, napkin grading, question-count display), and a few full-component render and interaction tests.
- **End-to-end tests** (Playwright): only 2 files are wired into the actual `npx playwright test` harness (`command-palette.spec.ts`, `practice-smoke.spec.ts`). The `tests/` directory also contains roughly 10 ad-hoc `.mjs` scripts (screenshot audits, dark-mode contrast checks, responsive-viewport audits) that are run manually with `node` and aren't part of any automated suite. CI's own end-to-end smoke step is a third, separate implementation, a Python/Playwright script (`scripts/e2e-smoke.py`), meaning there are three distinct, non-overlapping browser-automation code paths in this project (TS Playwright specs, ad-hoc TS/JS scripts, and a Python script), none of which know about the others. If you're adding browser-level coverage, know which of the three you're extending.

## Known issues

These are good starting points if you're looking for a first contribution.

- **`npm run lint` is currently broken on `dev`.** `eslint` is pinned to `10.4.0` (bumped by an auto-merged Dependabot PR), but `eslint-config-next@16.2.6`'s nested `eslint-plugin-react` only supports `eslint` through `^9.7`, so running lint crashes with a `TypeError` instead of reporting results. CI does not run a lint step, so this hasn't blocked any deploys, but any contributor running `npm run lint` locally today will hit this. A fix exists and was reviewed but is not yet merged as of this document.
- Two components (`HardwareConfigurator.tsx`, `LevelExplainer.tsx`) are fully built but imported nowhere in the app; they're dead code.
- `GRACE_WINDOW_SECONDS` is declared in the vault Worker's config and documented as a 10-minute cross-release grace window, but is not referenced anywhere in the Worker's actual request-handling code.
- The analytics Worker's header comment describes persisted `summary:latest`/`meta:total_events` KV keys that the implementation does not actually write, since aggregates are computed live instead. This is stale documentation, not a functional gap.
- Production and staging deployments of `staffml-vault-worker` share one Cloudflare KV namespace for rate limiting, meaning load on one environment's rate limiter affects the other's counters.
- The LinkML schema still declares a per-question `chains` field that the build pipeline no longer reads (chain membership moved to the `chains.json` sidecar); the schema hasn't been pruned to match.

## Project history

- **`competency_area` went from free text to a closed 13-value enum** after roughly 40 corpus items had been tagged with inconsistent or incorrect values. The schema was tightened to make that class of error structurally impossible going forward.
- **Chain membership moved from an inline per-question YAML field to a `chains.json` sidecar** (the "v1.1 architecture"). This let chain curation, including Gemini-assisted chain generation and rebuilding, happen as a bulk, reviewable operation over one file instead of touching thousands of individual question YAMLs.
- **Git LFS was used in the parent `cs249r_book` repository, then removed entirely.** The LFS "clean" filter produced phantom-modified-file noise on every pre-existing binary and crashed the pre-commit stash mechanism. The actual LFS-tracked footprint was small (roughly 540 KB of paper figures, all derivable from SVG sources), so the historical LFS pointers were converted to raw blobs and future binaries go straight into git.
- **The static-corpus fetch path had a race condition that caused silent, wasteful re-fetching.** `getStaticFullDetail`'s cache-population check (`if (!cache) { await fetch(...) }`) wasn't atomic across concurrent callers, so a batch hydration (Gauntlet starting a multi-question session) triggered one full corpus re-fetch per question instead of one shared fetch. Fixed by caching the in-flight fetch promise itself, not just its resolved value.
- **That same batch-hydration call site used `Promise.all`, which meant a single failed per-question fetch silently sank the entire batch** as an unhandled promise rejection, despite an adjacent code comment already claiming per-question fallback behavior that `Promise.all` structurally cannot provide. Fixed by switching to `Promise.allSettled` with an explicit per-item fallback to the original summary.
- **The local-dev corpus-rebuild script's `vault`-CLI-availability probe shelled out to the Unix-only `which` command**, which does not exist on Windows. `spawnSync("which", ...)` always failed there, so on every Windows contributor's machine the probe always reported "not installed" and silently skipped rebuilding `public/data/corpus.json`, even when `vault-cli` was correctly installed. The practical symptom: `/practice` (and any route needing full question detail in static/local-dev mode) rendered completely blank, with nothing in the browser console pointing at the real cause, since the failure happened in a build step rather than the running app. Fixed by probing with `vault --version` directly instead of routing through a Unix-only intermediary.
- **The production vault Worker's CORS allowlist never included the dev-preview site's origin** (`https://harvard-edge.github.io`, which serves both the production GitHub Pages mirror and the `/cs249r_book_dev/` preview path). Every question-detail fetch from the dev preview was silently rejected by the browser's CORS check, and because it was a static configuration gap rather than a transient failure, reloading never helped. Fixed by adding that origin to the Worker's allowlist.
- **`StarGate` originally rendered as a plain, unstyled-for-accessibility `<div>`**, with no dialog role, no `aria-modal`, no focus management, and no Escape handler, meaning the dimmed page behind a full-viewport blocking overlay stayed keyboard-reachable and screen readers didn't announce it as a dialog at all. Rewritten to match the accessible-dialog pattern now shared by `CommandPalette`, `KeyboardShortcutsOverlay`, and `WaitlistModal`.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the full file map, real code from every subsystem, local setup steps, and common contribution workflows. The "Known issues" list above is a reasonable place to find a first task, and the "Project history" section shows the kind of bug that tends to hide in this codebase (silent failures inside async or cross-language boundaries) so you know what to watch for when reviewing your own changes.
