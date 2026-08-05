# Socratiq: Design

*This is the contributor-facing design document for Socratiq, a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `socratiq/` in that repo. It explains what Socratiq is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers real design decisions that shaped the current architecture, and "Known issues" lists documented gaps, including one important current-state fact worth reading before you assume the widget is live anywhere.*

## Problem

A textbook, however well written, is a one-way conversation: a reader who gets stuck on a concept, wants to check their understanding, or would benefit from a quick quiz has no way to interact with the material itself. Building that interactivity into every page of a Quarto-based book or documentation site the conventional way would mean deeply coupling an AI assistant into each site's own build pipeline, once per site, with no shared implementation.

Socratiq solves this by being a single, self-contained, embeddable widget: any static HTML page gets AI chat, quiz generation, and progress tracking by adding one `&lt;script&gt;` tag, with zero coupling to how that page was built. It runs entirely inside a Shadow DOM, so it can be dropped into a Quarto book, a documentation site, or any other static HTML page without any risk of its styles or scripts colliding with the host page's.

## Goals

- A single embeddable bundle, one `&lt;script&gt;` tag, that adds an AI-powered learning companion to any static HTML page with zero integration work beyond that one tag.
- Complete style and script isolation from the host page via the Shadow DOM, so the widget can never break, or be broken by, the page it's embedded in.
- An AI chat feature grounded in the actual content of the page it's embedded on, so answers are relevant to what the reader is currently looking at, not a generic chatbot.
- Quiz generation (multiple-choice, short-answer, and spaced-repetition flashcards) derived from page content, with an offline fallback that still produces a usable quiz if every configured LLM provider is unavailable.
- Local, private progress tracking (IndexedDB-backed, including a spaced-repetition scheduler) so a reader's learning history persists across visits without needing a server-side account.
- A single, small proxy layer (a Cloudflare Worker) that holds real LLM provider API keys server-side, so the widget itself never ships a credential to the browser, while still supporting several interchangeable providers with automatic fallback if one is unavailable.
- Opt-in by design: the widget only activates behind a cookie or URL parameter, so a site can ship the bundle in production without it being active for every visitor by default.
- Free and open source, versioned alongside the rest of the `cs249r_book` monorepo, with an explicit intent (stated in the book's own placeholder documentation) to eventually be split into its own dedicated repository.

## Non-goals

- Not a general-purpose chatbot; every feature is scoped to the content of the page the widget is embedded on.
- Not a framework-based single-page application. The widget is deliberately built as vanilla JavaScript with Vite as a bundler, not React or Vue, since it needs to inject into arbitrary host pages rather than own a page itself.
- Not a direct-to-LLM-provider client. The widget never holds a provider API key; all calls go through a Cloudflare Worker proxy that holds credentials server-side.
- As of this document, not actually embedded in the live, published MLSysBook site. See "Known issues" below.

## Technology stack

| Technology | What it is | How Socratiq uses it |
|---|---|---|
| Vite | A JavaScript build tool and dev server. | Builds the widget in two distinct modes: a fast, HMR-enabled dev server for local iteration, and a fully bundled, minified single-file production build. |
| `vite-plugin-singlefile` | A Vite plugin that inlines every asset (JS, CSS, HTML, WASM) into one output file. | Produces the widget's actual distributable artifact, `bundle.js`, a single file with no external dependencies, so embedding it really is just one `&lt;script&gt;` tag with nothing else to load. |
| The Shadow DOM (a browser API, not a library) | A browser-native mechanism for attaching an isolated DOM subtree to a page, with its own scoped styles. | The entire widget UI lives inside a Shadow DOM attached to a host `&lt;div&gt;` the widget creates itself, which is the mechanism that guarantees the widget's styles and scripts can never conflict with the host page's. |
| IndexedDB (via the `idb` library) | A browser-native database, and a small Promise-based wrapper around its callback API. | Stores chat history, quiz results, and progress-tracking data locally in the reader's browser, with no server-side account needed. |
| D3.js | A data-visualization library. | Renders the widget's knowledge-graph feature, a visual map of concepts covered on the page. |
| KaTeX, Mermaid, and `markdown-it` | A math-typesetting library, a diagram-rendering library, and a Markdown parser. | Together they let the widget render AI-generated responses that include math notation, diagrams, and formatted text, matching what the host textbook itself can display. |
| DOMPurify | An XSS-sanitization library. | Sanitizes any HTML the widget generates or receives before inserting it into the page, since AI-generated content and user input both need to be treated as untrusted. |
| `compromise` | A lightweight, offline natural-language-processing library. | Powers the widget's offline quiz-generation fallback, so a basic quiz can still be produced if every configured LLM provider is unavailable. |
| `@leeoniya/ufuzzy` | A fast fuzzy-search library. | Finds paragraphs on the current page that are relevant to a reader's question, so the AI chat's answers can be grounded in specific page content rather than the whole page at once. |
| Cloudflare Workers | A serverless JavaScript/TypeScript runtime running at Cloudflare's edge locations. | Hosts the proxy layer the widget's AI features call through, holding real provider API keys server-side so the browser-side widget never has direct access to a credential. |
| `jsonrepair` | A library that fixes malformed JSON. | Repairs LLM output that's supposed to be valid JSON (for example, a generated quiz) but isn't quite, a common failure mode when parsing structured output from a language model. |
| `jspdf` | A client-side PDF-generation library. | Powers the widget's chat-history export feature. |

## Architecture

### Embedding and isolation

A host page loads the widget with a single tag:

```html
&lt;script type="module" src="path/to/bundle.js"&gt;&lt;/script&gt;
```

On load, the widget checks whether it should activate at all: activation is gated behind a `socratiq=true` cookie or URL parameter, checked and set on first visit if the URL parameter is present, so the same production bundle can ship on a live site without turning on for every visitor by default. If active, the widget creates a host `&lt;div&gt;`, attaches an open Shadow DOM to it, and injects its own CSS and markup entirely inside that shadow root. Every subsystem, chat, quiz, progress, settings, and so on, is wired up from inside that isolated tree.

### AI chat, grounded in page content

When a reader asks a question, the widget doesn't send the whole page to an LLM. It first extracts the page's table of contents and text content, then uses fuzzy paragraph search to find the passages most relevant to the question, and builds a prompt combining a fixed system prompt with the question and those relevant passages. The response streams back token by token and renders into a markdown-formatted chat bubble, so math, code, and diagrams in the AI's answer render the same way the surrounding textbook content does.

### Quiz generation with a provider fallback chain

Quiz generation tries a primary LLM provider, then falls back to a second if the first fails, and only falls back to a fully offline, `compromise`-based local quiz generator if both remote attempts fail. This three-tier fallback means a reader can always get some form of quiz, even in a degraded network or provider-outage scenario, rather than seeing an error.

### The LLM proxy layer

The widget itself never talks to an LLM provider directly. Every AI call routes through a small Cloudflare Worker proxy, which holds the real provider credentials server-side and exposes two endpoints, one for standard request/response calls and one for streaming responses. The proxy tries providers in a documented priority order, with several interchangeable options configured, so the system keeps working even if one provider has an outage. Which specific providers and models are used for which feature is centralized in one client-side configuration file, separate from prompt templates and UI copy, which live in a second configuration file, so a maintainer tuning model choice doesn't need to touch prompt engineering and vice versa.

### Progress tracking and the knowledge graph

Everything about a reader's interaction with the widget, chat history, quiz results, and a spaced-repetition schedule, is stored locally in the browser via IndexedDB, with no server-side account. A separate knowledge-graph feature, rendered with D3, visualizes the concepts covered on the current page as a graph, giving a reader a structural view of the material rather than only a linear one.

### Development and testing harness

Because the widget only makes sense in the context of a host page, local development runs against real, static snapshots of actual book pages (an encryption-textbook test page and an excerpt of the ML systems book itself) rather than an isolated component sandbox. The dev server opens directly into one of these test pages with the widget pre-activated and hot module replacement enabled, so a change to the widget's source is visible immediately against realistic content. A second, minimal Python static-file server exists as an alternative local preview path, notably adding the cross-origin isolation headers (`Cross-Origin-Opener-Policy`, `Cross-Origin-Embedder-Policy`) that some of the widget's browser-storage features need.

### Publishing into the book

The production build doesn't just produce `bundle.js` in the widget's own output directory; a custom Vite plugin copies that file directly into three destinations inside the `book/` sub-project on every build (the source location, plus both volume's rendered HTML output directories), so a Socratiq contributor's build step is also the mechanism that updates the book's copy of the bundle. A dedicated CI workflow (see "Testing and CI" below) exists specifically to catch the case where a contributor edits the widget's source but forgets to run that build, which would otherwise let the book silently keep serving a stale bundle.

### Testing and CI

There's no functional or unit test suite for the widget's behavior as of this document. The one automated check that exists is a drift guard: a GitHub Actions workflow that rebuilds the widget from source on every pull request touching Socratiq's source files, and fails the build if the freshly rebuilt bundle differs from the one currently committed into the book's directory, catching exactly the "forgot to rebuild" failure mode described above. It does not verify the widget's actual runtime behavior, chat, quizzes, or progress tracking, in a browser.

## Known issues

These are good starting points if you're looking for a first contribution, and the first one in particular is worth understanding before you assume the widget is doing anything on the live site today.

- **The widget's embed script tag is currently commented out in both of the book's Quarto configurations.** The production bundle is built, copied into the book's directory, and guarded by CI against drift, but the actual `&lt;script&gt;` tag that would activate it on the live, rendered book is disabled, with an inline comment noting it's "disabled pending quiz regeneration." As of this document, Socratiq is fully wired up as infrastructure but not actually live-embedded anywhere a reader would encounter it.
- **There's no functional test coverage for the widget's actual behavior.** The only CI check is the source-to-bundle drift guard described above; nothing verifies that chat, quiz generation, or progress tracking actually work in a real browser, so a functional regression could ship in a rebuilt bundle without being caught.
- **The book's own placeholder documentation for this feature states the eventual intent is to open-source the widget's code into its own dedicated repository**, distinct from where the real, active source currently lives in this monorepo. Anyone contributing here should be aware the project's long-term home may change.

## Project history

- **The widget was deliberately built as vanilla JavaScript rather than a framework**, since it needs to inject cleanly into arbitrary host pages it doesn't control, a constraint that rules out most component frameworks' assumptions about owning the page.
- **The bundle-drift CI check exists because the failure mode it guards against is easy to hit by accident**: since the widget's source lives in one place (`socratiq/`) and its built artifact is consumed from another (`book/quarto/tools/scripts/socratiQ/bundle.js`), a contributor editing only the source and forgetting the rebuild step would silently ship a stale widget to the book with no error anywhere in a normal PR review, which is exactly what the drift-check workflow was added to catch.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, real code from the chat and quiz subsystems, the build and publish pipeline, local setup steps, and common contribution workflows. The "Known issues" list above is a reasonable place to find a first task, especially since re-enabling the embed script tag (once whatever "quiz regeneration" blocker is resolved) would be a small, high-impact change.
