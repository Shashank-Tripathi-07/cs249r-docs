# SocratiQ: System Design

*This document describes how a reader's chat message actually reaches an LLM and comes back rendered, how the offline quiz fallback works with zero network calls, and where the XSS sanitization boundary sits. It is written for a contributor changing the provider chain, the quiz generation, or the progress tracking. All facts below are sourced from `socratiq/src_shadow/js/`.*

## 1. Problem this system solves

A widget that injects into arbitrary static HTML pages via one script tag has to do three things that are each individually hard: never leak an LLM API key to the browser, never let LLM-generated content become an XSS vector when it gets rendered, and keep working, at least in a degraded form, when every configured LLM provider is unavailable. This system solves the first with a server-side proxy Worker, the second with a mandatory sanitization pass before any HTML write, and the third with a real, local NLP-based quiz generator that needs no network at all.

## 2. Dependencies and what each one actually does here

SocratiQ's `package.json` documents its own 15 runtime dependencies with an inline size-and-purpose comment on each, the only manifest in this repo that self-audits this rigorously. See [`dependency-map.md`](../dependency-map.md) section 3 for the full annotated list. The two most architecturally significant: `dompurify`, the mandatory sanitization pass on every piece of LLM-generated HTML before it's written to the DOM, and `compromise`, the NLP library powering the fully offline quiz fallback described in section 5.

## 3. Component inventory

```
socratiq/src_shadow/js/
  index.js                     <- bootstrap: main() -> inject(), Shadow DOM setup
  libs/agents/
    cloudflareAgent.js          <- provider-fallback chat logic
  components/
    markdown/streamdown_markdown.js   <- renders chat responses
    quiz/
      create_quiz_button_grp.js, load_quiz.js, quiz-storage.js,
      backupQuiz.js              <- fully offline fallback generator
    progress/, progressReport/
    spaced_repetition/           <- SM-2-style scheduling, its own two
                                     architecture docs live in this folder
    visualizations/KnowledgeGraph.js   <- D3 concept graph
  libs/utils/save_chats.js       <- IndexedDB persistence (via idb)
```

## 4. Data flow: a chat message to a rendered, sanitized answer

```
1. Widget activates: a "socratiq=true" cookie (1-year max-age) or a
   URL parameter, checked in index.js's main()
                    |
2. main() -> inject(): creates a host <div>, calls
   hostElement.attachShadow({mode: "open"}), injects styles and HTML
   into the shadow root so the widget's CSS can never leak into, or
   be broken by, the host page's own styles
                    |
3. User sends a message. cloudflareAgent.js builds an OpenAI-style
   messages payload and fetches:
     ${apiUrl}?url=<upstream provider URL>&provider=<name>
   apiUrl resolves to a Cloudflare Worker (proxy-worker, prod URL
   hardcoded, localhost:8787 in dev), which injects the real
   provider API key server-side, the browser never holds one
                    |
4. Response streamed back as SSE, parsed incrementally
                    |
5. streamdown_markdown.js renders it:
   markdown-it (structure) -> katex.renderToString() (math)
   -> Mermaid (diagrams) -> DOMPurify.sanitize() BEFORE any
   innerHTML write, this is the actual XSS boundary, since the
   content originates from an LLM, not a trusted source
                    |
6. Persisted via save_chats.js to IndexedDB (through the idb wrapper)
```

The Worker itself is a generic reverse proxy, not a per-provider adapter: it accepts a target URL and a provider name as query parameters and forwards the request, injecting whichever credential that provider needs. Unlike StaffML's AI interviewer Worker, this proxy's own source isn't part of the `socratiq/` checkout, only the client call sites that talk to it are, worth knowing if you need to change proxy-side behavior rather than client-side provider selection.

## 5. Error handling and the offline fallback

`cloudflareAgent.js`'s `tryMultipleProvidersSingle`/`tryMultipleProvidersStream` first try a user-configured custom adapter, then walk a fixed provider order, Groq, Gemini, Cerebras, SambaNova, Mistral, an OpenAI-compatible route, HuggingFace, AwanLLM, catching and logging each failure (rate-limit 429s handled as a distinct case) and moving to the next. Only after all eight fail does it throw.

When quiz generation specifically hits that all-providers-failed case, `backupQuiz.js` takes over with a genuinely offline generator: `extractEntitiesAndNouns()` splits the source chapter text into sentences and pulls topics and nouns per sentence using `compromise`'s `.topics()`/`.nouns()`, then `generateQuestions()` builds fill-in-the-blank multiple-choice questions by masking one entity or noun per sentence and drawing three shuffled wrong answers from other nouns and terms in the same sentence. No network call anywhere in this path, it's a real fallback that produces a usable quiz, not a placeholder or an error message, even if every single configured provider is down.

## 6. Contributing

If you are adding a new LLM provider, add it to the `preferredOrder` array in `cloudflareAgent.js` and confirm the Worker-side proxy actually supports forwarding to it, the client and the Worker have to agree on the provider name string. If you are touching anything that renders LLM output to the DOM, the `DOMPurify.sanitize()` call before the `innerHTML` write is not optional scaffolding, it's the actual security boundary for this widget, see [`ci-workflows.md`](ci-workflows.md) for why a `dompurify` version bump specifically needs a bundle rebuild before it takes effect.
