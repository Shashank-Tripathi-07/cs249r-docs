# StaffML: Implementation Reference

> **Status: as-built, contributor-facing.** StaffML is a live, already-implemented app. This document is your map for reading and modifying the real source: file paths, line numbers, and representative code pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-04). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how." Section 11, "Common contribution workflows," is the fastest way in if you already know what you want to change.

## Prerequisites

What you need installed, and when you need it, depends on what you're touching:

| To work on... | You need |
|---|---|
| The frontend (`interviews/staffml/`) | Node.js (a recent LTS release) and npm. That's it; the frontend runs entirely against the production Worker's data by default. |
| The question corpus (adding or editing questions) | The above, plus Python 3.11 or newer and `vault-cli` installed locally (`pip install -e interviews/vault-cli`). No Cloudflare account needed; the corpus compiles and previews entirely offline. |
| A Cloudflare Worker (`staffml-vault-worker`, the AI interviewer worker, or the analytics worker) | Node.js, the `wrangler` CLI (installed as a dev dependency in each Worker's own `package.json`), and a Cloudflare account with a personal D1 database and KV namespace if you want to run that Worker against your own edge resources rather than mocks. |
| The AI interviewer worker specifically, in hosted mode | An API key for at least one of the six supported providers (Groq, OpenAI, Anthropic, Gemini, OpenRouter, or Cloudflare Workers AI), set via `wrangler secret put`. You can also just exercise the local-journal fallback path with no key at all. |

Everything else (the CI/CD pipeline, deploys) runs through GitHub Actions and doesn't require any local setup to understand or read. See "CI/CD implementation notes" below.

## Repository layout

```
cs249r_book/
  interviews/
    staffml/                    # Next.js frontend (this doc's main focus)
    staffml-vault-worker/       # Cloudflare Worker: question data API (D1-backed)
    staffml-vault-types/        # Shared TS types package (codegen target, used by worker + site)
    vault/                      # Question corpus: YAML source, schema, chains, compiled vault.db
    vault-cli/                  # Python `vault` CLI: build/validate/author/release the corpus
    paper/                      # LaTeX research paper describing the corpus
  .github/workflows/
    staffml-preview-dev.yml
    staffml-publish-live.yml
    staffml-validate-dev.yml
    staffml-validate-vault.yml
    staffml-auto-pr.yml
    staffml-welcome.yml
    staffml-chain-rebuild.yml
    staffml-audit-corpus-monthly.yml
    staffml-update-paper.yml
```

### `interviews/staffml/` file map (frontend)

```
src/
  app/
    layout.tsx, not-found.tsx, globals.css
    page.tsx                    # / : Vault home/browser (652 lines)
    welcome/page.tsx            # /welcome - first-run landing (234 lines)
    practice/{page.tsx,layout.tsx}   # /practice (1531 lines, largest route)
    gauntlet/{page.tsx,layout.tsx}   # /gauntlet - Mock Interview (882 lines)
    interview/page.tsx          # /interview - Live Interview (749 lines)
    plans/{page.tsx,layout.tsx} # /plans (416 lines)
    progress/{page.tsx,layout.tsx}   # /progress (530 lines)
    explore/page.tsx            # /explore - radial visualizer (1009 lines)
    framework/page.tsx          # /framework - Design Grammar reference (676 lines)
    roofline/{page.tsx,layout.tsx}   # /roofline (295 lines)
    simulator/{page.tsx,layout.tsx}  # /simulator (342 lines)
    dashboard/{page.tsx,layout.tsx}  # /dashboard - usage analytics (466 lines)
    contribute/{page.tsx,layout.tsx} # /contribute (385 lines)
    about/{page.tsx,layout.tsx} # /about (372 lines)
  lib/
    corpus.ts                   # 959 lines - Question model, filters, search, napkin grading, chains
    corpus-provider.tsx         # 145 lines - VaultContext, worker-readiness probe, SW registration
    vault-config.ts             # 49  lines - static vs worker mode resolution
    vault-fetch.ts              # 209 lines - resilient transport (timeout/retry/circuit-breaker)
    interview-conductor.ts      # 375 lines - Live Interview session state machine
    interview-api.ts            # 97  lines - POST turn to AI interviewer worker
    interview-types.ts          # 147 lines - pure types for Live Interview
    progress.ts                 # 519 lines - attempts, SM-2, heat map, streaks, export/import
    plans.ts                    # 302 lines - curated + custom study plans
    analytics.ts                # 388 lines - event tracking, batching, local summary
    taxonomy.ts                 # 243 lines - area/topic tree from bundled JSON
    rubric.ts                   # 110 lines - heuristic self-assessment checkpoint extraction
    simulator.ts                # 143 lines - distributed-training time/memory model
    star-gate.ts                 # 126 lines - GitHub-star growth-nag gate
    stats.ts                    # 85  lines - build-time corpus size/release constants
    hardware.ts                 # 119 lines - hardware specs, interconnects, formulas
    glossary.ts                 # 60  lines - acronym detection/lookup
    levels.ts                   # 88  lines - L1-L6+ ladder definitions
    daily.ts                    # 51  lines - deterministic daily-3-questions selection
    error-reporter.ts           # 79  lines - window.onerror/unhandledrejection to analytics
    announcement.ts             # 51  lines - announcement bar static content
    env.ts                      # 22  lines - ecosystem base URL / live-vs-dev detection
    hooks.ts                    # 7   lines - useMounted()
    issue-url.ts                # 131 lines - GitHub issue URL builders (report/suggest/contribute)
    meta-descriptions.ts        # 126 lines - tooltip copy for level/track/area badges
    url.ts                      # 38  lines - safeHref() XSS guard for corpus-authored links
    hooks/
      useFullQuestion.ts        # 87  lines - hydrate summary to full Question
      useVisibilityPoll.ts      # 74  lines - tab-visibility-aware setInterval
  components/                   # 44 .tsx files, 6,146 lines - see design doc for full inventory
    Providers.tsx, Nav.tsx, EcosystemBar.tsx, CommandPalette.tsx,
    KeyboardShortcutsOverlay.tsx, AnnouncementBar.tsx, MaybeFooter.tsx, Footer.tsx,
    VersionDriftToast.tsx, AskInterviewer.tsx, StarGate.tsx, StreakBadge.tsx,
    NapkinCalc.tsx, HardwareRef.tsx, MCQOptions.tsx, MarkdownText.tsx, MathText.tsx,
    GlossaryText.tsx, NapkinMathDisplay.tsx, QuestionVisual.tsx, ScenarioSkeleton.tsx,
    BookRefCard.tsx, ChainBadge.tsx, ChainStrip.tsx, QuestionFeedback.tsx,
    LevelBadge.tsx, MetaTooltip.tsx, FirstRunExplainer.tsx, GithubIcon.tsx,
    ThemeProvider.tsx, Toast.tsx, PaperCitationCard.tsx,
    HardwareConfigurator.tsx (unused), LevelExplainer.tsx (unused)
    vault/ (TopicDetail, AreaOverview, ExpandedArea, SearchResults, TopicCard, FilterPill, SectionDivider)
    plans/ (PathBuilder, PrepDashboard)
    progress/ (AreaProgressSection, TopicProgressBar)
  data/                          # generated + hand-authored static data (designGrammar.ts, glossary.json, ...)
  __tests__/                     # 24 vitest files
scripts/
  sync-design-grammar.mjs        # generates src/data/designGrammar.ts from design-grammar/grammar.yml
  build-local-corpus.mjs         # predev hook: regenerates local corpus.json via `vault build --local`
worker/                          # AI interviewer Cloudflare Worker (separate from staffml-vault-worker)
analytics-worker/                # analytics ingestion Cloudflare Worker
tests/                           # Playwright: 2 real specs + ~10 ad-hoc .mjs scripts
vitest.config.ts, playwright.config.ts, next.config.mjs, package.json
```

---

## 1. The content pipeline (`interviews/vault/`, `interviews/vault-cli/`)

This is the part of the project you'll touch most often if your contribution is a new question or a correction to an existing one, rather than a code change.

### 1.1 Schema (`interviews/vault/schema/question_schema.yaml`)

A LinkML schema, v1.0.0. LinkML is a schema-modeling language: you describe your data shape once, in YAML, and generate code from it for every language and format that needs to agree on that shape. The file's own header states it is "the sole source of truth for the vault's data model. Pydantic models, SQL DDL, and TypeScript types are derived from this file" (regenerated via `vault codegen`, with a `--check` diff mode used in CI to catch drift). If you ever need to add a new field to a question, this schema file, not the Pydantic models or the TypeScript types directly, is where you make the change; the rest is generated.

A real question, unmodified, from `interviews/vault/questions/cloud/architecture/cloud-0231.yaml`:

```yaml
schema_version: '1.0'
id: cloud-0231
track: cloud
level: L4
zone: optimization
topic: attention-scaling
competency_area: architecture
bloom_level: analyze
phase: both
title: The KV-Cache Context Explosion
scenario: You are serving a Llama-3 8B model...
question: Why is this physically impossible?
details:
  realistic_solution: You hit the KV-Cache memory wall...
  common_mistake: |
    **The Pitfall:** ...
    **The Rationale:** ...
    **The Consequence:** ...
  napkin_math: |
    **Assumptions & Constraints:** ...
    **Calculations:** ...
    **Conclusion & Interpretation:** ...
status: published
provenance: imported
requires_explanation: false
expected_time_minutes: 10
validated: true
validation_status: OK
validation_date: '2026-04-01'
validation_model: gemini-2.5-flash
math_verified: true
math_status: CORRECT
math_date: '2026-04-03'
math_model: gemini-3.1-pro-preview
human_reviewed:
  status: not-reviewed
  by: null
  date: null
  notes: null
```

On-disk layout: `interviews/vault/questions/<track>/<competency_area>/<track>-<NNNN>.yaml`, one file per question, filename equals the question's `id`. The competency-area sub-folder is a navigability convenience only; the YAML's own `competency_area` field is authoritative if they ever disagree.

### 1.2 The `vault` CLI (`interviews/vault-cli/src/vault_cli/`)

Built with Typer, a Python library for building command-line tools from plain function signatures and type hints. Entry point `main.py`, subcommands each in `commands/<name>.py`. Run `vault --help` or `vault <command> --help` for the authoritative, always-current option list; the table below is a map of what exists and when you'd reach for it.

| Command | What it does |
|---|---|
| `vault build [--local-json] [--site-bundle] [--release-id ID]` | Compile YAML into `vault.db` plus a manifest and, conditionally, client JSON bundles. This is the command you run after editing any question YAML to see your change reflected locally. |
| `vault check [--strict]` | Tiered invariant checks (fast, structural, slow). Run this before opening a PR that touches question content. |
| `vault new/edit/rm/restore/move` | Authoring primitives with validation and typed-confirmation safety, the recommended way to create or edit a question rather than hand-editing YAML directly. |
| `vault api` / `vault serve` | Mirror the production Worker API locally from `vault.db`, or launch a Datasette browser over it. |
| `vault release` | Snapshot, emit migrations, export the paper, tag, publish, verify. Maintainer-only in practice. |
| `vault stats [--exemplar-coverage]` | Corpus scorecard: counts and coverage by track, level, and area. |
| `vault codegen [--check]` | Regenerate the Pydantic, D1-DDL, and TypeScript artifacts from the LinkML schema. Run this after any schema change. |
| `vault doctor --check <name>` | Independent diagnostic subchecks, JSON output. |
| `vault diff <from> <to> [--classify]` | Compare two release artifacts (cosmetic, semantic, or structural difference). |
| `vault promote --reviewed-by <email>` | Moves a question from draft to published; bumps `llm-draft` to `llm-then-human-edited`. |
| `vault dup` | Acknowledge scenario-dedup (LSH/Jaro-Winkler) false positives. |
| `vault generate` | LLM-assisted drafting from `vault/exemplars/` only, hard-capped at 25 questions per invocation. |
| `vault lint` | Author-facing linter (ERROR, WARNING, INFO severities). Good first pass before `vault check`. |
| `vault ls` / `vault show` | Browse and inspect questions with axis filters. |
| `vault chain ls` / `vault chain show` | Browse and inspect chains. |
| `vault audit run/review/summarize/merge` | Gemini-driven quality audit (level-fit, coherence, math-correctness gates). Maintainer-run. |

### 1.3 `vault build`: what it actually reads and writes

Source: `commands/build.py`. Reads every YAML under `--vault-dir` via `loader.load_all()` (Pydantic-validated); questions that fail validation are reported but don't hard-fail the whole build, so `vault.db` contains everything that passed. Release-set membership is a separate filter step (`policy.py` + `release-policy.yaml`).

Always writes:
1. **`vault.db`** (SQLite) via `compiler.py`. Tables: `questions` (indexed on `topic`, `(track, level)`, `zone`, `status`, `human_review_status`), `chains`, `chain_questions`, `tags`.
2. **`interviews/staffml/src/data/vault-manifest.json`**: `{releaseId, releaseHash, schemaVersion, policyVersion, buildDate, questionCount, chainCount, conceptCount, trackDistribution, levelDistribution, areaCount, taxonomyVersion}`.

Conditionally:
- `--local-json`: also writes `interviews/staffml/src/data/corpus.json` (full prose) and mirrors it to `public/data/corpus.json` (the path the Next.js static-mode loader fetches), plus copies visual SVGs into `public/question-visuals/<track>/`. This is the flag you want while contributing question content, since it's what lets `npm run dev` show your local edits.
- `--site-bundle`: writes only `interviews/staffml/src/data/corpus-summary.json` (prose stripped) plus visual assets. This is the production flag.

Every legacy-JSON emission (`legacy_export.py`) also auto-writes a `<name>-summary.json` companion (`_to_summary`), and joins chain membership from the `chains.json` sidecar at export time: the per-question `chain_ids`/`chain_positions`/`chain_tiers` fields are computed, not stored in the source YAML.

### 1.4 Chains (`interviews/vault/chains.json`)

```json
{
  "chain_id": "cloud-chain-auto-001-01",
  "track": "cloud",
  "topic": "model-serving-infrastructure",
  "competency_area": "deployment",
  "levels": ["L2", "L3", "L5"],
  "questions": [
    {"level": "L2", "id": "cloud-2519", "title": "...", "bloom": "understand"},
    {"level": "L3", "id": "cloud-2520", "title": "...", "bloom": "apply"},
    {"level": "L5", "id": "cloud-2521", "title": "...", "bloom": "evaluate"}
  ],
  "rationale": "...",
  "_origin": "gemini-3.1-pro-preview",
  "tier": "primary"
}
```

CI enforces a structural invariant on this file: within a chain, levels ordered by array position must be non-decreasing (chain-Bloom-monotonicity, `validator.py:280-294`).

---

## 2. Backend: `staffml-vault-worker`

If you're new to Cloudflare Workers: a Worker is a small piece of JavaScript or TypeScript that Cloudflare runs at edge locations close to the requesting user, instead of on a single origin server. This one is the question-data API. `wrangler` is Cloudflare's CLI for developing and deploying Workers; `wrangler dev` runs one locally.

### 2.1 D1 schema (`migrations/0001_bootstrap.sql`)

D1 is Cloudflare's managed SQLite database product. It behaves like ordinary SQLite for schema and query purposes, with migrations tracked the same way you'd track them for any SQL database.

```sql
CREATE TABLE questions (
  id TEXT PRIMARY KEY, title, topic, track, level, zone, status,
  scenario, common_mistake, realistic_solution, napkin_math,
  deep_dive_title, deep_dive_url, provenance, created_at, last_modified,
  file_path, content_hash, authors_json
  -- newer fields (competency_area, bloom_level, phase, human_review_status/by/date)
  -- are used by index.ts's queries but not yet reflected in this bootstrap DDL
);
CREATE INDEX ... ON questions(topic);
CREATE INDEX ... ON questions(track, level);
CREATE INDEX ... ON questions(zone);
CREATE INDEX ... ON questions(status);

CREATE TABLE chains (id PRIMARY KEY, name, topic);
CREATE TABLE chain_questions (chain_id, question_id, position, PRIMARY KEY(chain_id, position), FOREIGN KEY...);
CREATE TABLE tags (question_id, tag, FOREIGN KEY...);            -- unqueried by index.ts
CREATE TABLE taxonomy (id PRIMARY KEY, name, description, area, prerequisites_json, tracks_json, question_count);
CREATE TABLE taxonomy_edges (source, target, PRIMARY KEY(source, target), FOREIGN KEY...);  -- unqueried
CREATE TABLE zones (id PRIMARY KEY, description, skills_json, levels_json);
CREATE TABLE release_metadata (key PRIMARY KEY, value);

CREATE VIRTUAL TABLE questions_fts USING fts5(title, scenario, realistic_solution, content='questions', content_rowid='rowid');
-- kept in sync via questions_ai / questions_ad / questions_au triggers
```

### 2.2 CORS: fail-closed by construction (`src/index.ts:124-141`)

```ts
function corsHeaders(env: Env, origin: string | null): Record<string, string> {
  const raw = (env.CORS_ALLOWLIST || "").trim();
  const allowed = raw ? raw.split(",").map(s => s.trim()).filter(Boolean) : [];
  const base = {
    "Access-Control-Allow-Methods": "GET, OPTIONS",
    "Access-Control-Allow-Headers": "Content-Type, If-None-Match, X-Vault-Release",
    "Vary": "Origin",
  };
  if (allowed.length === 0) return base;   // no ACAO header at all: deny by omission
  if (origin && allowed.includes(origin)) {
    base["Access-Control-Allow-Origin"] = origin;
  }
  return base;   // origin not on the list -> still no ACAO header
}
```

Current production allowlist (`wrangler.toml`): `https://staffml.mlsysbook.ai,https://mlsysbook.ai,https://staffml-vault.mlsysbook.ai,https://harvard-edge.github.io,http://localhost:3000`; the `harvard-edge.github.io` entry was added to fix the dev-preview CORS bug (see design doc, "Project history").

### 2.3 Rate limiting (`src/rate_limit.ts`)

Fixed 60-second window, KV-backed. Cloudflare KV is a simple, fast, eventually-consistent key-value store that Workers can read and write; it's the natural place to keep short-lived counters like this.

```ts
// key = `rl:{class}:{ip}:{windowSec}` where windowSec = floor(nowSec / 60)
// class: "default" (60 rpm) | "search" (10 rpm)
// IP resolved ONLY from CF-Connecting-IP (never X-Forwarded-For, spoofable)
// missing header -> fail closed, 429, Retry-After: 60
// missing RATE_LIMIT_KV binding (local dev) -> open-allow
```

Documented caveat: read-then-write isn't atomic across Cloudflare edge points of presence, so a burst can leak past the nominal cap by up to two or three times.

### 2.4 Schema fingerprint (`src/index.ts:31-73`)

At cold start, and re-checked every 5 minutes if previously failed:

```
SHA-256(concatenated, whitespace-normalized DDL of every table/index/trigger/view in sqlite_master)
  EXCLUDING FTS5 shadow tables (questions_fts_data/idx/docsize/content/config)
  EXCLUDING internal _cf_%/d1_%/sqlite_% objects
compared against release_metadata.schema_fingerprint
```

On mismatch: `manifest.schema_fingerprint_ok = false`, responses carry `X-Vault-Degraded: schema-fingerprint-mismatch` but still return 200, a soft signal, not a hard failure. Excluding FTS5 shadow-table DDL matters because that DDL differs across SQLite versions between the offline compiler and D1's runtime, which would otherwise produce false-positive mismatches on every deploy.

### 2.5 Caching: two layers

1. **HTTP**: `cacheControl(ttl)` returns `Cache-Control: public, max-age={ttl}, stale-while-revalidate={ttl*2}`. Per-endpoint: manifest and question responses cache 3600 seconds, search 300 seconds, taxonomy 86400 seconds, the `/questions` list 600 seconds (hardcoded), `/chains/:id` 3600 seconds (hardcoded).
2. **Edge (Cloudflare Cache API)**: `cachedOrCompute()` wraps every endpoint except `/stats`. The cache key prefixes the path with `/__vault__/{release_id}/...`, so a new deploy (a new release_id) atomically invalidates the whole cache namespace with zero manual purge. Degraded (`X-Vault-Degraded`) and non-2xx responses are never cached.

### 2.6 `wrangler.toml`

```toml
name = "staffml-vault"
main = "src/index.ts"
compatibility_date = "2026-04-15"
compatibility_flags = ["nodejs_compat"]

[[d1_databases]]
binding = "DB"
database_name = "staffml-vault"
database_id = "254f630f-dd6a-400e-8d86-786e92be7a70"
migrations_dir = "migrations"

[[kv_namespaces]]
binding = "RATE_LIMIT_KV"
id = "19f1f3d7e03c4c3cb8704d951f7a22e7"

[vars]
CACHE_TTL_MANIFEST = "3600"
CACHE_TTL_QUESTION = "3600"
CACHE_TTL_SEARCH = "300"
CACHE_TTL_TAXONOMY = "86400"
CORS_ALLOWLIST = "https://staffml.mlsysbook.ai,https://mlsysbook.ai,https://staffml-vault.mlsysbook.ai,https://harvard-edge.github.io,http://localhost:3000"
SCHEMA_FINGERPRINT = "b97218dae6354b1ba0ed4a93242f0ca848554ed02ec86246dc8977ae918c5ad6"
GRACE_WINDOW_SECONDS = "600"   # declared, not referenced in index.ts

[env.staging]
name = "staffml-vault-staging"
# own D1 database, but reuses the SAME RATE_LIMIT_KV id as production
```

If you're setting this Worker up against your own Cloudflare account for local development, you'll create your own `[[d1_databases]]` and `[[kv_namespaces]]` entries (or a `wrangler.toml.local` override) rather than pointing at the production IDs above.

---

## 3. Backend: AI interviewer Worker (`interviews/staffml/worker/`)

Multi-provider adapter, first-available-wins over `groq → openai → anthropic → gemini → openrouter → cf-workers-ai` (configurable via `PROVIDER_PRIORITY`). Endpoints: `GET /health`, `POST /waitlist`, `POST /interview` (full conductor turn), `POST /ask` (single clarifying-question turn, `mode: "interview"|"study"`).

Rate limiting is three-tiered (per-IP-per-hour, default 10; per-IP-per-day, default 60; global-per-day ceiling, default 8000) and fails closed (503) if `RATE_LIMIT_KV` is missing, deliberately stricter than the vault worker, since an open-allow default here would mean unbounded LLM API spend rather than just degraded search.

CORS here is looser than the vault worker: an unmatched origin still gets `allowed[0]` echoed back rather than the header being omitted entirely, a materially different (weaker) policy, worth knowing if you're auditing cross-worker consistency.

`wrangler.toml`: an `[ai]` binding named `AI` (Cloudflare Workers AI, the final-fallback provider), two KV namespaces (`RATE_LIMIT_KV`, `WAITLIST_KV`), no D1, no staging environment. Provider API keys are `wrangler secret put` secrets, never in `[vars]`, so they never end up committed to the repository.

---

## 4. Backend: analytics Worker (`interviews/staffml/analytics-worker/`)

KV-only. `POST /` validates event `type` against an allowlist (includes `client_error`), caps batch to 100 events per request, per-event size 1024 bytes (8192 for `client_error`, to fit stack traces), rejects anything matching an email-like regex, strips to an explicit allowed-fields list before storage. Storage key: `events:{YYYY-MM-DD}:{batchUUID}`, mapping to a JSON array with a 90-day TTL, batch-per-key specifically to avoid read-modify-write races that a single growing-list key would have. `GET /` (admin-secret-gated) computes 7-day aggregate stats live by listing the day-keyed events, not from a persisted summary.

---

## 5. Client data layer (`interviews/staffml/src/lib/`)

### 5.1 `corpus.ts`: the `Question` type

```ts
interface Question {
  // bundled (always present, sync):
  id: string; track: string; level: string; title: string; question?: string;
  visual?: { kind: "svg"; path: string; alt: string; caption: string };
  topic: string; zone: string; competency_area: string;
  bloom_level?: string; phase?: string; status?: string;
  chain_ids?: string[]; chain_positions?: number[]; chain_tiers?: ("primary"|"secondary")[];
  book_refs?: BookRef[]; validated?: boolean; math_verified?: boolean; human_reviewed?: ...;

  // heavy: ship as empty stubs in corpus-summary.json, hydrated on demand:
  scenario: string;
  details: {
    common_mistake: string; realistic_solution: string; napkin_math?: string;
    resources?: { name: string; url: string }[];
    options?: string[]; correct_index?: number;   // MCQ fields ARE bundled (sync scoring)
  };
}
```

### 5.2 Static-mode hydration: the fixed cache-stampede bug

```ts
let _staticDetailsCache: Map<string, Question> | null = null;
let _staticCorpusPromise: Promise<Map<string, Question>> | null = null;

async function loadStaticCorpus(): Promise<Map<string, Question>> {
  if (_staticDetailsCache) return _staticDetailsCache;
  if (!_staticCorpusPromise) {
    _staticCorpusPromise = fetch("/data/corpus.json")
      .then(async (res) => {
        if (!res.ok) throw new Error(`Static corpus.json not available (status ${res.status}). Run 'vault build --local-json'.`);
        const data = await res.json() as Question[];
        const map = new Map(data.map(q => [q.id, q]));
        _staticDetailsCache = map;
        return map;
      })
      .catch((err) => { _staticCorpusPromise = null; throw err; });  // allow retry on next call
  }
  return _staticCorpusPromise;
}
```

The key property: N concurrent callers (for example, Gauntlet hydrating 10 questions via `Promise.allSettled`) all `await` the same in-flight promise instead of each independently checking `_staticDetailsCache === null` and firing their own fetch. Before this fix, starting a 5-question Gauntlet session fired 5 separate multi-megabyte fetches of `corpus.json`.

### 5.3 Worker-mode hydration and resilient transport

```ts
// vault-fetch.ts: the ONE transport every Worker call goes through
const DEFAULT_TIMEOUT_MS = 8_000;
const DEFAULT_RETRIES = 2;                         // 1 initial + 2 retries
const RETRYABLE_STATUS = new Set([408, 425, 429, 500, 502, 503, 504]);
const FAIL_THRESHOLD = 5;                          // circuit opens after 5 consecutive failures
const RESET_MS = 30_000;                           // half-open probe window

function backoffDelay(attempt: number): number {
  const base = Math.min(2 ** attempt * 150, 2_000);
  return base * (0.5 + Math.random());              // full jitter
}
```

A circuit breaker is a pattern for stopping repeated calls to a service that's already failing, instead of hammering it and waiting out the timeout on every single request. Per-origin circuit breaker states here: `closed{failures}`, then after 5 failures `open{openedAt}`, then after 30 seconds elapsed `half-open` (exactly one probe admitted; concurrent others rejected until it resolves), then back to `closed` on success or back to `open` on failure. This correct single-probe half-open semantics was itself a fix for a prior version that admitted every concurrent caller during half-open.

`getQuestionFullDetail(id)` re-nests the Worker's denormalized flat response (`{scenario, common_mistake, realistic_solution, napkin_math}` at the top level) into `Question.details` before merging with the bundled summary, and tracks hydration completion via an explicit `_hydratedIds: Set<string>` rather than checking `details.realistic_solution` truthiness, since a recall or MCQ question can have a legitimately empty solution, which would otherwise cause it to re-fetch on every single access.

### 5.4 Gauntlet's batch hydration: the fixed unhandled-rejection bug

```ts
// gauntlet/page.tsx, inside startGauntlet()
Promise.allSettled(selected.map(q => getQuestionFullDetail(q.id))).then(results => {
  const merged = selected.map((s, i) => {
    const r = results[i];
    return (r.status === 'fulfilled' && r.value) ? r.value : s;   // fall back to summary
  });
  setQuestions(merged);
});
```

Previously `Promise.all`: a single rejected fetch sank the entire batch as an unhandled promise rejection, even though an adjacent comment already claimed per-question fallback behavior that `Promise.all` cannot structurally provide.

### 5.5 SM-2 spaced repetition (`progress.ts`)

SM-2 is a well-known spaced-repetition scheduling algorithm, originally developed for flashcard software: given how well you recalled something, it computes an ease factor and an interval, and uses those to decide how many days until you should see that item again.

```ts
export function updateSRCard(questionId: string, quality: number): void {
  // quality is the app's 0-3 self-score: 0=skip, 1=wrong, 2=partial, 3=nailed it
  const cards = getSRCards();
  const card = cards[questionId] || { questionId, easeFactor: 2.5, interval: 1, repetitions: 0, nextReview: 0, lastScore: 0 };

  const q = [0, 1, 3, 5][quality] ?? 0;   // map 0-3 -> canonical SM-2 quality 0-5

  if (q < 3) {
    card.repetitions = 0;
    card.interval = 1;
  } else {
    if (card.repetitions === 0) card.interval = 1;
    else if (card.repetitions === 1) card.interval = 3;
    else card.interval = Math.round(card.interval * card.easeFactor);
    card.repetitions++;
  }

  card.easeFactor = Math.max(1.3, card.easeFactor + (0.1 - (5 - q) * (0.08 + (5 - q) * 0.02)));  // canonical SM-2 formula
  card.lastScore = quality;
  card.nextReview = Date.now() + card.interval * 24 * 60 * 60 * 1000;

  cards[questionId] = card;
  saveSRCards(cards);
}
```

### 5.6 Unit-aware napkin-math grading (`corpus.ts`)

```ts
// Relative-error grading against a per-track tolerance
const TOLERANCE: Record<string, number> = { cloud: 0.25, edge: 0.15, mobile: 0.10, tinyml: 0.05 };
// grades: exact | close | ballpark | off | way_off, each with a maxSelfScore cap

// Unit-aware layer (js-quantities): if both user and model answers parse to a
// Qty with COMPATIBLE dimensions, convert to base units and compare that way.
// Incompatible dimensions -> forced way_off (e.g. comparing seconds to bytes).
// If either side has no parseable unit, fall back to the legacy bare-number path.
```

### 5.7 `interview-conductor.ts`: transcript windowing

```ts
function windowTranscript(transcript: TranscriptEntry[], maxBytes: number): TranscriptEntry[] {
  // Walk BACKWARD from the most recent entry, JSON-stringifying each and
  // accumulating byte size. Stop once adding the next (older) entry would
  // exceed maxBytes. Prepend a synthetic summary entry noting how many
  // earlier exchanges (and distinct chainRef topics) were dropped, so the
  // LLM still has continuity context even for a truncated transcript.
}
```

Called with `maxBytes = 48_000` before every turn sent to the AI interviewer Worker.

---

## 6. Core practice-flow implementation notes

### 6.1 The deliberation guard (`practice/page.tsx`)

```
On reveal attempt:
  if (elapsedSeconds < 15 AND charsTyped < 50) AND not yet self-calibrated-off:
    show "Think longer?" confirm dialog (Keep thinking / Reveal anyway)
  Once the user demonstrates real engagement (>=20s OR >=80 chars) ONCE in
  the session, the guard self-calibrates off for the rest of the session.
```

### 6.2 Rubric extraction (`lib/rubric.ts`): pure heuristic, no LLM call

```
1. Split realistic_solution into sentences (20-250 chars each).
2. Score each sentence:
     + 2 per technical-signal keyword match (bottleneck, FLOPS, NVLink, cache, ...)
     + 3 if it contains a causal signal (because/therefore/causes/leads to)
     + up to 3 for digit density
     + 2 if it contains an "Nx" multiplier pattern
     - penalty for being short or starting with a generic filler opener
3. Take top 3 by score, then RE-SORT to original document order (so the
   checklist reads in the same order as the solution prose).
4. Shorten each to <=100 chars at a word boundary, strip filler openers.
5. Append one "Avoided: <best sentence from common_mistake>" item.
6. Append one fixed "Included quantitative estimate" item if napkin_math present.
Cap at 4 items total.
```

### 6.3 Gauntlet question selection (`corpus.ts::selectGauntletQuestions`)

```
1. Prepend one warm-up question from an easier level (L4->L2, L5->L3, L6+->L3).
2. Round-robin across zones for competency breadth.
3. Fisher-Yates shuffle.
```

---

## 7. Local development setup

1. `cd interviews/staffml && npm install`.
2. `npm run dev` (runs `predev` first: `sync-design-grammar.mjs`, which regenerates `src/data/designGrammar.ts` from `design-grammar/grammar.yml`, then `build-local-corpus.mjs`).
3. `build-local-corpus.mjs`'s vault-CLI probe (fixed to use `spawnSync("vault", ["--version"])`, not the Unix-only `which`, so it now works correctly on Windows):
   ```js
   const probe = spawnSync("vault", ["--version"], { encoding: "utf8" });
   if (probe.error || probe.status !== 0) {
     // vault-cli not installed -> soft-fail (exit 0), fall back to mirroring
     // SVG visuals only; the dev server will fetch scenario/details from the
     // production Worker instead of local YAML.
   } else {
     // vault build --local  ->  writes src/data/corpus.json,
     // public/data/corpus.json, and mirrors visual SVGs.
   }
   ```
   Without `vault-cli` installed (`pip install -e interviews/vault-cli`), local dev still works, just against the production Worker's data rather than uncommitted local YAML edits. If you're only working on frontend code and don't need to see local question edits, you can skip installing `vault-cli` entirely.
4. `.env.development` sets `NEXT_PUBLIC_VAULT_FALLBACK=static` automatically, so `npm run dev` serves question detail from the local `corpus.json` (once generated) rather than the network.
5. `npm run build`: static export to `out/`. Requires `next.config.mjs`'s `output: 'export'`; no server runtime is produced.
6. `npm test`: Vitest unit suite. `npx playwright test tests/command-palette.spec.ts tests/practice-smoke.spec.ts`: the two wired-in e2e specs (dev server must already be running; `playwright.config.ts` does not auto-launch one, by design).

---

## 8. CI/CD implementation notes

### 8.1 Dev preview (`staffml-preview-dev.yml`)

Job graph: `validate-dev` and `validate-vault` (both reusable `uses:` calls) run in parallel with `build`; `deploy` needs all three. Build step regenerates the corpus with `vault build --vault-dir "$VAULT_DIR" --release-id preview-dev --local-json` so the preview always reflects current `dev`-branch YAML, injects the paper PDF into `out/downloads/`, rewrites URLs for the dev subpath via `.github/scripts/rewrite-dev-urls.sh`, validates that critical pages exist, then uploads an artifact. Deploy SSH-clones the separate dev-preview repo, rsyncs the artifact into `staffml/`, and pushes with a retry-and-rebase loop to handle concurrent deploys from other sub-projects sharing that repo.

### 8.2 Production publish (`staffml-publish-live.yml`)

Manual-only (`workflow_dispatch`, requires `confirm: "PUBLISH"`), gated behind two `infra-publish-guard.yml` calls (one per validate workflow; both must be green on the latest `dev` commit before this can run). Build uses `vault build --site-bundle` (no local corpus.json; production never ships prose eagerly). After the static site deploys to `gh-pages` (twice: once for `/staffml/`, once for a `/interviews/` to `/staffml/` redirect page), the same job run also ships vault data to Cloudflare D1 (`ship_d1.py --skip-build`) and deploys the Worker itself (`npx wrangler deploy`), so the frontend and data backend release atomically in one workflow run, avoiding a window where the deployed frontend expects a schema the deployed Worker doesn't yet have.

### 8.3 Validate workflows: the concurrency-group gotcha

Both `staffml-validate-dev.yml` and `staffml-validate-vault.yml` use a literal string concurrency group (`staffml-validate-dev-${{ head_ref || run_id }}`) rather than `${{ github.workflow }}`, a deliberate fix for a real incident where both workflows resolved to the same concurrency group when called as reusable workflows from `staffml-preview-dev.yml`, causing one to cancel the other mid-run.

---

## 9. Testing implementation notes

- `vitest.config.ts`: `environment: 'jsdom'`, single setup file mocking `localStorage`/`sessionStorage` (in-memory), `crypto.randomUUID`, `ResizeObserver` (needed by `react-medium-image-zoom`), and polyfilling `Element.prototype.scrollIntoView` (absent in jsdom, needed by `CommandPalette`'s active-row-scroll test).
- Every accessibility test in `src/__tests__/` exists because a real accessibility bug shipped first: `stargate-dialog-a11y.test.tsx`, `framework-modal-a11y.test.tsx`, `toast-a11y.test.tsx`, `nav-disclosure-aria.test.tsx`, `waitlist-modal.test.tsx` are all regression guards, not speculative coverage.
- Every timer-cleanup test (`stargate-timer-cleanup.test.tsx`, `toast-timer-cleanup.test.tsx`, `use-visibility-poll.test.tsx`) exists for the same reason: a leaked `setTimeout`/`setInterval` calling `setState` after unmount, caught once, guarded forever after.
- `playwright.config.ts`: `fullyParallel: false`, `workers: 1`, `retries: 0`, expects the dev server already running at `baseURL: "http://localhost:3000"`, deliberately not auto-launched by the config, so the same config works whether you're iterating locally or running against a CI-built static export.
- Three independent, non-overlapping browser-automation code paths exist in this project: the 2 wired-in Playwright TS specs, roughly 10 ad-hoc Playwright `.mjs` scripts (screenshot/audit tooling, run manually), and a separate Python/Playwright script (`scripts/e2e-smoke.py`) that CI's `staffml-validate-dev.yml` actually runs. None of the three know about the others, worth being aware of before assuming "the e2e suite" means any one specific thing in this codebase.

---

## 10. Known-broken as of this document

`npm run lint` crashes (`TypeError` in `eslint-plugin-react`'s `react/display-name` rule) because `package.json` pins `eslint@10.4.0` (an auto-merged Dependabot bump) while `eslint-config-next@16.2.6`'s nested `eslint-plugin-react` only supports `eslint` through `^9.7`. CI does not run a lint step, so this hasn't blocked deploys, but running lint locally today will fail outright rather than report findings. A fix (repin to `eslint@9.39.5`) exists and has been reviewed but is not yet merged into `dev` as of this document.

---

## 11. Common contribution workflows

Concrete steps for the changes contributors most often make.

### Adding or editing a question

1. `cd interviews/vault-cli && pip install -e .` (once), then from the repository root: `vault new --track <track> --competency-area <area>` to scaffold a new question YAML, or open an existing one under `interviews/vault/questions/<track>/<area>/` directly for an edit.
2. Fill in the required fields (see the design doc's "Data model" section for the full field list and format rules, especially the `**The Pitfall:**`/`**The Rationale:**`/`**The Consequence:**` markers in `common_mistake` and the `**Assumptions...**`/`**Calculations:**`/`**Conclusion...**` markers in `napkin_math`, both of which are format-enforced).
3. `vault lint` for a quick author-facing pass, then `vault check --strict` for the full structural invariant check.
4. `vault build --local-json` to compile your change into a local `corpus.json`.
5. `cd ../staffml && npm run dev`, then find your question in `/practice` or `/explore` to see it rendered exactly as a student would.
6. Open a PR. CI's `staffml-validate-vault.yml` re-runs the same checks against your branch.

### Fixing a frontend bug

1. `npm run dev` and reproduce the bug first; note which route and which file in the file map above (Section 0) owns the behavior.
2. Make the fix. If the bug was a silent failure (a swallowed exception, an unhandled rejection, a race condition), check whether the fix needs a regression test; the "Project history" section of the design doc is a good reference for the kinds of bugs this codebase has hidden before.
3. Add or update a test in `src/__tests__/` (Vitest) for logic-level changes, or a Playwright spec for a full user-flow change.
4. `npm test` locally before opening a PR. CI's `staffml-validate-dev.yml` runs typecheck, the unit suite, a production build, and the Playwright end-to-end smoke.

### Changing a Cloudflare Worker endpoint

1. `cd interviews/staffml-vault-worker` (or `interviews/staffml/worker`, or `interviews/staffml/analytics-worker`, depending on which Worker you're touching) and `npm install`.
2. `npx wrangler dev` to run the Worker locally. Without your own D1 database or KV namespace configured, most endpoints will still respond using local emulation; check that Worker's `wrangler.toml` for what's bound.
3. Make your change in `src/index.ts` (or the relevant module). If you add a new environment variable, add it to `wrangler.toml`'s `[vars]` and document it inline the way `GRACE_WINDOW_SECONDS` is documented, so it's clear to the next reader whether it's actually wired up.
4. Add or update a `vitest` test for the Worker. `staffml-validate-vault.yml` runs the vault worker's suite in CI.
5. Worker deploys happen only through `staffml-publish-live.yml` (production) or are exercised locally via `wrangler dev`; you won't deploy a Worker directly from your own machine as part of a normal contribution.

### Where to look when something breaks silently

This codebase has a track record of bugs that hide at async or cross-language boundaries rather than throwing immediately: a fire-and-forget `Promise.all` that swallows one failure and sinks a whole batch, a fetch race that fires redundant requests instead of erroring, a CORS gap that fails every request the same silent way regardless of retries. If your bug reproduces inconsistently or fails without an error message, check first whether something is failing open (returning success without actually completing) rather than failing loud. The design doc's "Project history" section walks through several real examples of exactly this pattern.
