# Socratiq: Implementation Reference

> **Status: as-built, contributor-facing.** Socratiq is a live, already-implemented widget, though not currently activated on the published book (see the design doc's "Known issues"). This document is your map for reading and modifying the real source: file paths and representative code pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| The widget itself (`socratiq/src_shadow/`) | Node.js and npm. `npm install` from `socratiq/`, then `npm run dev`. |
| Testing against real book pages | Nothing extra; the dev server opens directly into a bundled test page. The `serve.py` alternative preview path needs Python 3. |
| The proxy Worker (LLM provider calls) | Access to the separate `cloudflare/proxy-worker/` project (not inside `socratiq/` itself) and its own Wrangler setup, plus real provider API keys if you're testing live calls rather than against `USE_LOCAL_WORKERS`. |
| Verifying the book-publish pipeline | A local checkout of `book/` alongside `socratiq/`, since the production build copies its output there directly by relative path. |

## Repository layout

```
cs249r_book/
  socratiq/
    package.json                  # @mlsysbook/socratiq
    vite.config.dev.mjs             # Dev server: HMR, opens a real test page
    vite.config.prod.mjs             # Prod build: single-file bundle + copy-to-book plugin
    serve.py                          # Alternative local static server with COOP/COEP headers
    src_shadow/
      index.html, indexHtml.js, cssStyles.js   # Shadow DOM HTML/CSS, injected at runtime
      configs/
        env_configs.js                # Backend/runtime config: providers, models, worker URLs
        client.config.js               # Prompt templates and UX copy
        db_configs.js, db_configs_one.js, initial_config_styles.js
      js/
        index.js                       # Main entry point (~2,900 lines)
        components/                     # UI: chat, quiz, progress, settings, onboarding, etc.
        libs/
          agents/                        # LLM call/streaming logic, per-provider serverless calls
          diagram/                        # Mermaid wiring
          vector_db/, memory/, workers/, messaging/, drag/, utils/
        utils/
          textExtractor.js                # Page text/TOC extraction
          viewportContextCapture.js
      resources/quarto.html
    test_website/
      encryption_textbook/index.html     # Real static test harness page (dev server default)
      mlsys_book_removed_most/           # Real static test harness page (serve.py default)
    dist_vite/                            # Build output (gitignored; bundle.js lands here)
  book/
    quarto/tools/scripts/socratiQ/bundle.js  # Production copy of the built widget
    tools/scripts/socratiQ/bundle.js          # Symlink to the above
    socratiQ/README.md                         # Book-facing placeholder doc (not the real source)
    quarto/config/_quarto-html-vol1.yml         # Where the (currently disabled) embed tag lives
    quarto/config/_quarto-html-vol2.yml
    quarto/contents/frontmatter/socratiq/socratiq.qmd  # Reader-facing documentation page
  .github/workflows/
    socratiq-bundle-drift.yml            # The one CI check: source-to-bundle drift guard
```

---

## 1. Entry point and injection (`src_shadow/js/index.js`)

`main()` is the top-level bootstrap. In order: initializes an IndexedDB database, checks widget activation via `checkWidgetAccess()` (reads the `socratiq=true` cookie or URL parameter, and sets a one-year cookie if the URL parameter is present), asynchronously extracts the page's table of contents and text, and then calls `inject()`.

`inject()` does the actual DOM work:

```js
const host = document.createElement("div");
host.id = "widget-chat-container";
document.body.appendChild(host);
const shadowRoot = host.attachShadow({ mode: "open" });
shadowRoot.innerHTML = cssStyles + htmlContent;
// then wires up every subsystem inside shadowRoot:
// highlighting, markdown rendering, settings, theme manager,
// feedback, help modal, quiz stats, spaced-repetition modal,
// image zoom, onboarding, tooltips, knowledge graph, chat loading
```

Every feature module receives the shadow root (or elements queried from it) rather than touching `document` directly, which is what keeps the widget fully isolated from the host page.

---

## 2. Chat (`handleGeneralAction`, `handleQueryActionStream`)

1. `findSimilarParagraphsNonBlocking()` uses `@leeoniya/ufuzzy` to fuzzy-match the reader's question against extracted page paragraphs, returning the most relevant ones.
2. A prompt is assembled from a fixed system prompt (defined in `configs/client.config.js`), the question, and those relevant paragraphs.
3. `handleQueryActionStream()` calls `query_agent(params, token, ...)` from `libs/agents/`, which streams the response token by token.
4. Each token is appended to a markdown-rendered chat bubble in the shadow DOM, so KaTeX math, Mermaid diagrams, and formatted code render live as the response streams in.

A separate `handleResearchAction()` path asks the LLM to produce an Arxiv-style search phrase from the conversation, then calls into a research/paper-search flow, `initiateResearch()`.

---

## 3. Quiz generation (`handleSummativeAction`)

Tries, in order:

1. `query_agent_groq_serverless()`, a direct serverless call to the proxy for Groq.
2. `query_agent_gemini_serverless()`, the same for Gemini, if Groq fails.
3. `createQuiz()`, a fully offline, `compromise`-based local generator, if both remote attempts fail.

LLM-generated quiz JSON is repaired with `jsonrepair` before parsing, since structured output from a language model is not guaranteed to be strictly valid JSON on the first try.

---

## 4. Progress reporting (`handleProgressReport`)

Reads and writes an IndexedDB `progressReports` object store, and streams an AI-generated summary of the reader's progress via `tryMultipleProvidersStream()` (a helper that walks the same kind of provider-fallback chain as quiz generation), then renders the result as charts (XY plots, quadrant charts) using Chart.js.

---

## 5. Configuration (`src_shadow/configs/`)

`env_configs.js` is backend and runtime configuration:

```js
const USE_LOCAL_WORKERS = ...; // dev vs prod proxy switch
const MAIN_TOPIC = ...;
const PROVIDER_MODELS = { groq: "...", gemini: "...", ... };
const SIZE_LIMIT_LLM_CALL = ...;

const URL_SERVER_QUIZ_PROD = "https://proxy-worker.mlsysbook.workers.dev";
const URL_SERVER_QUIZ_STREAMING = "https://proxy-worker-streaming.mlsysbook.workers.dev";

export const WORKER_URL_AI = USE_LOCAL_WORKERS
  ? URL_SERVER_QUIZ_DEV + "/ai"
  : URL_SERVER_QUIZ_PROD + "/ai";
export const WORKER_URL_AI_STREAM = USE_LOCAL_WORKERS
  ? URL_SERVER_QUIZ_DEV_STREAMING + "/ai/stream"
  : URL_SERVER_QUIZ_STREAMING + "/ai/stream";
```

`client.config.js` holds prompt templates and user-facing copy, kept deliberately separate from `env_configs.js` so changing which model a feature uses doesn't require touching prompt text and vice versa.

The widget never talks to Groq, Gemini, or any other provider directly; every call goes through the Cloudflare Worker proxy at the URLs above, which holds the real provider credentials server-side. Documented provider priority order (from the widget's own README): Groq, then Gemini, then Cerebras, SambaNova, Mistral, OpenRouter, HuggingFace, and Awan, with quiz generation specifically defaulting to a fast Groq model and falling back to a fast Gemini model.

---

## 6. Build configuration

### 6.1 Dev (`vite.config.dev.mjs`)

- `root: 'src_shadow'`, dev server on port 4175.
- `open: '/test_website/encryption_textbook/index.html?socratiq=true'`, so starting the dev server immediately opens a real, static test page with the widget pre-activated.
- File-watch polling and an HMR overlay, so editing widget source updates the running page live.
- A custom middleware serves `/test_website/*` from disk, stripping any production `&lt;script&gt;` tag on that page and injecting the dev entry point plus the Vite HMR client instead.

### 6.2 Prod (`vite.config.prod.mjs`)

- `root: 'src_shadow'`, single entry `src_shadow/js/index.js`, output directory `../dist_vite`, output filename `bundle.js`.
- `minify: true`, `sourcemap: false`.
- The `vite-plugin-singlefile` plugin inlines every asset type (`js`, `css`, `html`, `wasm`) into that one output file, so the final artifact really is one file with nothing else to load.
- A custom plugin, `multi-dist-copy-and-cleanup`, runs on every `writeBundle` and copies the freshly built `dist_vite/` output to three destinations inside `book/`:
  ```
  ../book/quarto/tools/scripts/socratiQ
  ../book/quarto/_build/html-vol1/tools/scripts/socratiQ
  ../book/quarto/_build/html-vol2/tools/scripts/socratiQ
  ```
  This is why running the production build from `socratiq/` is also the mechanism that updates the book's copy of the widget; there's no separate "publish" step.

---

## 7. Local preview alternatives

- **`npm run dev`** (Vite): the primary workflow, with HMR, described above.
- **`serve.py`**: a minimal `http.server`-based static file server serving `test_website/mlsys_book_removed_most` on port 8000, with a custom handler adding `Cross-Origin-Opener-Policy: same-origin` and `Cross-Origin-Embedder-Policy: require-corp` response headers. Some browser storage and worker features need these cross-origin-isolation headers to function; this script exists as an alternative preview path when you specifically need them and Vite's own dev server isn't providing them. It must be run from the project root, since it checks for `vite.config.mjs` on startup.

---

## 8. How the widget reaches the book

`book/quarto/config/_quarto-html-vol1.yml` and `_quarto-html-vol2.yml` both contain, inside their `include-in-header` block:

```html
&lt;!-- SocratiQ bundle disabled pending quiz regeneration: --&gt;
&lt;!-- &lt;script type="module" src="/tools/scripts/socratiQ/bundle.js" defer&gt;&lt;/script&gt; --&gt;
```

This is currently commented out in both volumes; see the design doc's "Known issues." The referenced path, `/tools/scripts/socratiQ/bundle.js`, resolves to `book/quarto/tools/scripts/socratiQ/bundle.js` at runtime, the file the production Vite build copies to directly. `book/tools/scripts/socratiQ/bundle.js` (a different path, one level up in the book's own `tools/` rather than `quarto/tools/`) is a symlink pointing at the same underlying file, documented by its own small README.

`book/quarto/contents/frontmatter/socratiq/socratiq.qmd` is a separate, reader-facing documentation page describing the feature to a reader of the book (linked from the navbar/sidebar in both volume configs), distinct from `book/socratiQ/README.md`, which is a contributor-facing placeholder noting that the widget's source will eventually be open-sourced into its own dedicated repository and that this directory is a future home for it, not its current one.

---

## 9. CI: the bundle-drift guard

`.github/workflows/socratiq-bundle-drift.yml`, the only automated check for this project:

- **Triggers**: pull requests touching `socratiq/src_shadow/**`, `socratiq/vite.config.prod.mjs`, `socratiq/vite.config.dev.mjs`, `socratiq/package.json`, or `socratiq/package-lock.json`. Also runnable manually.
- **What it does**: checks out the repo, sets up Node, runs `npm ci` and `npm run build:vite` inside `socratiq/` (which also triggers the copy-to-book plugin described in section 6.2), then runs `git diff --exit-code` against `book/quarto/tools/scripts/socratiQ/bundle.js`.
- **What a failure means**: the bundle currently committed into the book doesn't match what building from the current source produces, meaning a contributor edited widget source without rebuilding and committing the result. The job's error message tells the contributor to run `npm run build:vite` and commit the regenerated file.

This check verifies the bundle is up to date with source; it does not verify the widget actually works correctly in a browser. There is no automated functional or visual test suite for chat, quiz generation, or progress tracking as of this document.

---

## 10. Local development workflow

1. `cd socratiq && npm install`.
2. `npm run dev`. This opens the encryption-textbook test page with the widget active and HMR wired up.
3. Make your change in `src_shadow/`. For UI/feature work, `js/components/` is usually where you'll be; for AI-call behavior, `js/libs/agents/`; for prompt or model tuning, the two files under `configs/`.
4. When you're satisfied, run `npm run build:vite` to produce a real production bundle and confirm it still copies cleanly into `book/`'s three destinations (you'll see the copy plugin's output in the build log).
5. If your change touches anything under the paths `socratiq-bundle-drift.yml` watches, expect CI to fail if you forget step 4 and don't commit the resulting `book/quarto/tools/scripts/socratiQ/bundle.js` diff alongside your source change.

---

## 11. Common contribution workflows

### Adding or changing a UI feature

1. Find the relevant component under `src_shadow/js/components/` (for example, `quiz/`, `progress/`, `settings/`).
2. Keep all DOM manipulation scoped to the shadow root passed into your component; never query or modify `document` directly, since that would break the isolation the whole project depends on.
3. Test against the real book-page harness (`npm run dev`), not an isolated fixture, since the widget's actual failure modes tend to show up only against real page content and real host-page CSS.
4. Run `npm run build:vite` before committing, and confirm the resulting `git diff` in `book/quarto/tools/scripts/socratiQ/bundle.js` is what you expect.

### Adding or changing an LLM-backed feature (chat, quiz, progress reports)

1. Look at how the existing feature you're closest to (`handleQueryActionStream`, `handleSummativeAction`, or `handleProgressReport`, all in `js/index.js`) builds its prompt and walks the provider fallback chain, and follow the same pattern rather than inventing a new one.
2. Add or adjust prompt text in `configs/client.config.js`, not inline in `index.js`, to keep prompt engineering separable from call logic.
3. If you're adding a new provider to the fallback chain, that change happens on both sides: the client-side priority list in `env_configs.js`/`index.js`, and the actual provider credential and routing logic in the separate `cloudflare/proxy-worker/` project, since the widget itself never holds a credential.
4. Consider whether your feature needs an offline fallback the way quiz generation does; a feature with no offline path will simply fail with no result if every remote provider is unavailable.

### Re-enabling the widget on the live book (a good first task)

1. Understand why the embed tag was disabled ("pending quiz regeneration," per the inline comment in both `_quarto-html-vol1.yml` and `_quarto-html-vol2.yml`), since re-enabling it without resolving whatever that refers to would likely reintroduce the original problem.
2. Once resolved, the change itself is small: uncomment the `&lt;script&gt;` tag in both files.
3. Given there's no automated functional test suite, verify manually against a real Quarto build of at least one book chapter before considering this done, chat, quiz generation, and progress tracking should all be exercised by hand.
