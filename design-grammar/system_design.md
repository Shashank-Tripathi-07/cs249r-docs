# design-grammar: System Design

*This document describes how `grammar.yml` becomes both a rendered HTML catalog and a generated TypeScript file consumed by StaffML's `/framework` page, the one real, verified integration point this project has with the rest of the ecosystem. All facts below are sourced from `design-grammar/grammar.yml`, `grammar.schema.json`, `scripts/build-html.mjs`, and `interviews/staffml/scripts/sync-design-grammar.mjs`.*

## 1. Problem this system solves

Deriving ML systems techniques from first principles needs a small, closed vocabulary, a fixed set of primitives and the rules for legally rewriting them, that stays internally consistent as it grows, and that can be presented two different ways: as a browsable, styled HTML reference, and as typed data a React app can render without re-parsing anything at request time. One YAML file as the single source of truth, with two independent generators consuming it, solves both without duplicating the vocabulary itself in two places.

## 2. Dependencies and what each one actually does here

`design-grammar/package.json` declares exactly one real dependency, `js-yaml`, for reading and writing `grammar.yml`. Everything else is plain Node scripting, no framework.

## 3. Component inventory

```
design-grammar/
  grammar.yml            <- the source of truth
  grammar.schema.json    <- validates grammar.yml's structure
  rewrite-rules.yml       <- the separate "playbook" of named transformations
  scripts/
    build-html.mjs        <- grammar.yml -> regenerated index.html
    validate.mjs           <- standalone validation, same checks as build-html
    migrate-html-to-yaml.mjs  <- one-time bootstrap tool (HTML -> YAML, historical)
  index.html              <- generated content between sentinel HTML comments
```

`grammar.yml` defines four real, structured things: **roles** (5, Represent/Compute/Communicate/Control/Measure, each with a color and column position), **layers** (8, data through production, e.g. `data -> math -> algorithms -> architecture -> optimization -> runtime -> hardware -> production`), **primitives** (90 entries, each with an id, a symbol, a name, a role, a layer, a year, a description, and `composition_links` to related primitives), and **assemblies** (expression strings that compose primitives into named systems). `rewrite-rules.yml` is a separate file holding the actual transformation playbook, distinct from the primitive catalog.

## 4. What primitives and rewrite rules actually are, with real examples

A primitive is a catalog noun, what exists. A real entry from `grammar.yml`:

```
id: 1, sym: "Tn", name: "Tensor", role: "R", layer: 2
description: "The fundamental mathematical structure holding information..."
composition_links: ["Op", "Cr", "Ob"]
```

A rewrite rule is a verb, a behavior-preserving transformation applied when a named physical constraint binds. A real entry from `rewrite-rules.yml`:

```
tiling: symbol "Ti"
  relieves: [capacity, bandwidth]
  preserves: mathematical semantics
  change: "Partitions a large computation into blocks that fit in a
           faster memory tier"
  preconditions: [block-structured access, associative or locally
                  composable computation]
  examples: [FlashAttention, blocked matrix multiplication,
             cache-aware convolution]
```

Eight named constraints exist for rules to relieve: capacity, bandwidth, latency, utilization, fragmentation, energy, reliability, cost. This is the actual mechanism behind the "deriving techniques from first principles" framing, a technique like FlashAttention isn't described as a special case, it's the `tiling` rule applied to relieve `capacity` and `bandwidth` on the `Tn`/attention primitives, with its tradeoffs and preconditions stated explicitly rather than left implicit.

## 5. Data flow: one source, two independent consumers

```
grammar.yml
      |
      +---> scripts/build-html.mjs
      |       loads grammar.yml, runs validate(doc), then regenerates
      |       index.html by replacing content between sentinel HTML
      |       comments (<!-- @gen:roles -->, @gen:primitives,
      |       @gen:assemblies), everything else in the HTML (CSS,
      |       surrounding prose) is preserved untouched
      |
      +---> interviews/staffml/scripts/sync-design-grammar.mjs
              re-implements the SAME validate() logic independently,
              then writes interviews/staffml/src/data/designGrammar.ts,
              a file headed "@generated, DO NOT EDIT BY HAND"
              additionally pre-parses every assembly's expression
              string into typed ExpressionToken[] at sync time, so
              StaffML's React renderer (app/framework/page.tsx) only
              ever maps over already-parsed tokens, it never parses
              an expression string at request time
```

This confirms, with real file paths, what was previously only asserted in `ecosystem-map.md`: StaffML's `/framework` page is genuinely synced from `design-grammar/grammar.yml`, not independently authored content that happens to look similar. The sync script is wired as a `predev`/`prebuild` hook in StaffML's own `package.json`, so `npm run dev` or `npm run build` in StaffML always regenerates the synced file first, a stale `designGrammar.ts` shouldn't be possible in a normal StaffML development flow, only if someone bypasses the npm scripts entirely.

## 6. What does not exist, corrected from an earlier version of this docs set

design-grammar has no CI validation of its own, and, contrary to what an earlier version of the top-level `ci-workflows.md` claimed, it is **not** validated as part of `book-validate-dev.yml` either. Checked directly against the actual workflow file: the only appearance of the string "design-grammar" anywhere in `book-validate-dev.yml` is a stale comment documenting a one-off manual re-trigger after an unrelated fix to a contributor-crediting config file, not a real path filter or validation step. A search across `book/**/*.py` for any reference to design-grammar's schema or content found nothing. The only real automated check on this project's content is `npm run validate` (`scripts/validate.mjs`), and nothing in CI currently runs it, meaning a malformed `grammar.yml` would only be caught by `build-html.mjs` failing locally, or by StaffML's sync script failing locally when someone runs `npm run dev`/`build` there.

## 7. Contributing

If you are adding a primitive or a rewrite rule, edit `grammar.yml` or `rewrite-rules.yml` directly, then run both `npm run build` here and, separately, StaffML's dev/build command to confirm the sync script picks up your change cleanly, since nothing in CI currently verifies this for you. If you're touching `validate()`'s logic, remember it's implemented twice, once in `build-html.mjs` and independently again in StaffML's `sync-design-grammar.mjs`, a validation rule you add in one won't automatically exist in the other.
