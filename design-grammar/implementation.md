# ML Systems Design Grammar: Implementation Reference

> **Status: as-built, contributor-facing.** The Design Grammar is a live, already-implemented catalog and toolchain. This document is your map for reading and modifying the real source: file paths, line numbers, and representative data pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| The catalog itself (`grammar.yml`, `rewrite-rules.yml`) | Node.js and npm, to run the build and validation scripts. No other tooling. |
| The standalone HTML visualization | The above; `index.html` can otherwise be opened directly in any browser with no server. |
| The research paper | A LaTeX distribution, plus Python (for the figure-generation script) and `rsvg-convert` for SVG-to-PDF conversion. |
| Syncing into StaffML | The above, plus everything StaffML itself needs (see the StaffML implementation reference); `make sync` calls into StaffML's own script directly. |

## Repository layout

```
cs249r_book/
  design-grammar/
    grammar.yml            # Canonical primitive + assembly catalog
    grammar.schema.json     # JSON Schema documenting grammar.yml's required shape
    rewrite-rules.yml       # Canonical rewrite-rule playbook
    DESIGN_GRAMMAR.md        # The conceptual framing: the five-tuple, worked example
    README.md
    index.html               # Standalone, self-contained interactive visualization (build target)
    package.json, Makefile
    scripts/
      build-html.mjs         # Regenerates index.html from grammar.yml
      validate.mjs            # Structural validator for both YAML files
      migrate-html-to-yaml.mjs # One-shot, recovery-only: index.html -> grammar.yml
    paper/
      paper.tex                # "A Design Grammar for ML Systems"
      appendix-primitives.tex, appendix-proofs.tex, nomenclature.tex, references.bib
      scripts/generate_primitive_catalog.py  # Renders the paper's catalog figure from grammar.yml
      figures/, Makefile
  interviews/staffml/
    scripts/sync-design-grammar.mjs   # The one confirmed downstream consumer
    src/data/designGrammar.ts          # Generated output, imported by the /framework page
```

---

## 1. `grammar.yml`: the canonical catalog

### 1.1 Top-level shape

Required keys, per `grammar.schema.json`: `version`, `title`, `roles`, `layers`, `primitives`, `assemblies`. Optional: `subtitle`, `known_collisions`.

```yaml
roles:
  R: { name: "Represent",  cols: [1, 4],  color: "#..." }
  C: { name: "Compute",    cols: [5, 9],  color: "#..." }
  X: { name: "Communicate", cols: [10, 12], color: "#..." }
  K: { name: "Control",    cols: [13, 16], color: "#..." }
  M: { name: "Measure",    cols: [17, 18], color: "#..." }

layers:
  1: "Data"
  2: "Math"
  3: "Algorithms"
  4: "Architecture"
  5: "Optimization"
  6: "Runtime"
  7: "Hardware"
  8: "Production"
```

### 1.2 A real primitive entry

```yaml
primitives:
  - id: 1
    sym: "Tn"
    name: "Tensor"
    role: R
    layer: 2
    col: 1
    description: "The fundamental mathematical structure holding information (scalars, vectors, matrices)."
    composition_links: [...]
    rationale: "..."

  - id: 2
    sym: "Pr"
    name: "Probability"
    role: R
    layer: 2
    col: 2
    year: "1654"
    description: "The mathematical primitive for representing uncertainty."
```

`sym` is a two-letter code (validated by the schema as matching `[A-Z][a-z]`), unique within a `(layer, col)` cell except where explicitly documented in `known_collisions`. `year`, where present, marks when the underlying mathematical or engineering idea first appeared, not when it was added to this catalog.

### 1.3 A real assembly entry

```yaml
assemblies:
  "Transformer": "Eb → [(At ∥ Mk) → Nm → Sk → Dd]ᴺ"
  "Mixture of Experts (MoE)": "Ro ? (Dd ∥ … ∥ Dd) → Gt"
  "Diffusion Model": "[St → Nm → (Dd → Ac → Sk)]ᴺ → Ob"
```

Every two-letter token in an assembly expression must resolve to a real primitive's `sym`. Composition operators: `→` sequence, `∥` parallel, `?` conditional, `⇌` adversarial, `↺` feedback loop, `[ ]ᴺ` a repeated group.

### 1.4 `known_collisions`

Documents deliberate symbol reuse: which two primitives share a symbol, and a note explaining why the collision is intentional rather than a mistake. The validator (section 3 below) treats any undocumented collision as an error.

---

## 2. `rewrite-rules.yml`: the playbook

```yaml
constraints:
  - capacity
  - bandwidth
  - latency
  - utilization
  - fragmentation
  - energy
  - reliability
  - cost

rules:
  tiling:
    name: "Tiling"
    symbols: [...]
    relieves: [capacity, bandwidth]
    preserves: [...]
    changes: [...]
    preconditions: [...]
    tradeoffs: [...]
    examples: ["FlashAttention", "Blocked matmul"]

  sharding:
    name: "Sharding"
    relieves: [capacity]
    examples: ["ZeRO", "FSDP", "Tensor parallelism"]

  virtualization:
    name: "Virtualization"
    relieves: [fragmentation, capacity]
    examples: ["PagedAttention"]

  routing:
    name: "Routing"
    relieves: [utilization, latency, capacity]
    examples: ["Mixture of Experts"]
```

Twelve rules total as of this document: tiling, fusion, sharding, factorization, pipelining, batching, caching, prefetching, quantization, sparsification, virtualization, scheduling, routing, and replication. Every rule's `relieves` list must only name constraints declared in the top-level `constraints` vocabulary; this is one of the checks `validate.mjs` runs.

---

## 3. The validator (`scripts/validate.mjs`)

Loads both YAML files via `js-yaml` and runs, in order:

1. **Primitive field-shape checks**: `sym` matches the required two-character pattern, `layer` is between 1 and 8, `col` is between 1 and 18, `role` is one of the five declared roles.
2. **Cell-collision check**: for every `(layer, col)` pair, confirms at most one primitive occupies it, unless the collision is documented in `known_collisions`.
3. **Composition-link resolution**: every primitive's `composition_links` entries must resolve to a real primitive symbol.
4. **`known_collisions` accuracy**: every documented collision must actually cite the correct row and column for the primitives it names, so the exception list itself can't silently go stale.
5. **Assembly-expression resolution**: every two-letter token appearing in any assembly's expression must resolve to a known primitive symbol.
6. **Rewrite-rule constraint validity**: every rule's `relieves` values must be drawn from the `constraints` list declared at the top of `rewrite-rules.yml`.

Exits with code 1 and an itemized list of issues on failure, or code 0 with a summary on success. Run it with `npm run validate` or `node scripts/validate.mjs` from `design-grammar/`.

---

## 4. The HTML build (`scripts/build-html.mjs`)

Treats the existing `index.html` as a template. It locates three sentinel HTML comments:

```html
&lt;!-- @gen:roles --&gt;
&lt;!-- @gen:primitives --&gt;
&lt;!-- @gen:assemblies --&gt;
```

and replaces the content between each pair with freshly rendered markup built from the current `grammar.yml`. If the sentinels don't yet exist (a one-time bootstrap case), it locates the original `const roles = ...` / `const primitives = ...` style data blocks instead. It runs the same structural validation as `validate.mjs` before writing, so a build never silently emits an inconsistent page. Run it with `npm run build` or `node scripts/build-html.mjs`.

---

## 5. The migration script (`scripts/migrate-html-to-yaml.mjs`)

A one-shot, recovery-only tool. It extracts the original, inline JavaScript data (`const roles`, `layerLabels`, `primitives`) out of `index.html`'s inline `&lt;script&gt;` tag using Node's `vm` sandbox, and emits `grammar.yml` from it. Its own comments are explicit that this direction of generation is historical: "after this runs, the YAML is canonical; only do that if you're recovering from a corrupt YAML." Do not run this as part of a normal edit workflow; it will overwrite `grammar.yml` with whatever is currently baked into `index.html`, discarding any YAML-only edits that haven't yet been built back into the HTML.

---

## 6. `index.html`: structure

A single, dependency-free file. Key sections:

- A header badge showing the current version and primitive/assembly counts (for example, "v0.2, 90 Primitives, 51 Assemblies"), sourced from `grammar.yml`'s own `version` field and the generated counts.
- A "Two Axes" explainer describing the row (layer) and column (role) system.
- A searchable primitive grid: an interactive, periodic-table-style 8-row by 18-column grid, populated via the `@gen:primitives`/`@gen:roles` sentinels, with a text search box filtering by name or symbol.
- A "System Assemblies" section rendering all 51 assemblies as clickable cards showing their symbolic expressions, with hover tooltips on individual symbols within an expression, and a legend explaining every composition operator.

---

## 7. The research paper (`paper/`)

`paper.tex` is the main LaTeX source, titled "A Design Grammar for ML Systems: Primitives, Constraints, and Rewrite Rules." It presents the formal five-tuple `G = (P, O, T, C, R)`, includes the primitive-catalog figure, and validates the grammar with five end-to-end derivations of real systems, plus a "dead-end analysis" showing a case where the grammar reveals a design as infeasible rather than merely difficult.

`paper/scripts/generate_primitive_catalog.py` generates the paper's Figure 1 (the primitive catalog visualization) as an SVG directly from the canonical `design-grammar/grammar.yml`, not from a separately maintained copy. The paper's own `Makefile` rasterizes that SVG to PDF via `rsvg-convert` as part of the LaTeX build.

Supporting files: `appendix-primitives.tex`, `appendix-proofs.tex`, `nomenclature.tex`, `references.bib`.

---

## 8. Downstream sync into StaffML

`interviews/staffml/scripts/sync-design-grammar.mjs` is the one confirmed real consumer of this catalog outside `design-grammar/` itself. It reads `design-grammar/grammar.yml` directly (there is no intermediate JSON export step) and writes `interviews/staffml/src/data/designGrammar.ts`, a generated TypeScript module. StaffML's own `package.json` wires this into its lifecycle:

```json
"predev": "node scripts/sync-design-grammar.mjs && node scripts/build-local-corpus.mjs",
"prebuild": "node scripts/sync-design-grammar.mjs",
"sync:design-grammar": "node scripts/sync-design-grammar.mjs"
```

So any StaffML contributor running `npm run dev` or `npm run build` transitively regenerates this file, without needing to know the Design Grammar project exists or run anything from `design-grammar/` themselves. StaffML's `/framework` page imports directly from the generated module (`import { primitives, ... } from "@/data/designGrammar"`), and its own CSS carries a comment noting it is "a pixel-port of the original `.panel` CSS from `design-grammar/index.html`," meaning the page's visual design is a manual, one-time reimplementation of the standalone HTML visualization's look, not a live embed of it.

---

## 9. The `Makefile`

```makefile
html:      # regenerate index.html from grammar.yml
validate:  # run the structural validator
sync:      # invoke interviews/staffml/scripts/sync-design-grammar.mjs directly
all:       # html + sync
clean:     # remove generated artifacts (destructive)
```

`make sync` is notable: it calls StaffML's own sync script by path, so a Design Grammar contributor can push a freshly edited catalog into StaffML's generated file without switching directories or knowing StaffML's build commands.

---

## 10. Local development workflow

1. `cd design-grammar && npm install` (the only runtime dependency is `js-yaml`).
2. Edit `grammar.yml` or `rewrite-rules.yml` directly; these are the source of truth. Never hand-edit `index.html`'s generated sections; they'll be overwritten on the next build.
3. `npm run validate` (or `node scripts/validate.mjs`) before doing anything else. Fix any reported issues; the validator's error messages cite the specific primitive or assembly at fault.
4. `npm run build` (or `make html`) to regenerate `index.html`. Open it directly in a browser to visually confirm your change looks right.
5. If your change should be reflected in StaffML's `/framework` page, run `make sync` (or `cd ../interviews/staffml && npm run sync:design-grammar`), then check `interviews/staffml/src/data/designGrammar.ts` for the diff, and if you're working in that repo directly, run StaffML's own tests (`framework-modal-a11y.test.tsx` specifically imports from the generated file).
6. If you're touching the paper, confirm `paper/scripts/generate_primitive_catalog.py` still runs cleanly against your updated `grammar.yml` before rebuilding the PDF.

---

## 11. Common contribution workflows

### Adding a new primitive

1. Pick an unused `(layer, col)` cell, or confirm a deliberate collision is acceptable and document it in `known_collisions` if so.
2. Add the entry to `grammar.yml`'s `primitives` list: `id` (next available), `sym` (a unique two-letter code), `name`, `role`, `layer`, `col`, `description`, and optionally `year`, `composition_links`, and `rationale`.
3. `npm run validate`, then `npm run build` to see it rendered in the grid.
4. If the primitive is used in any assembly expression you're also adding, confirm the symbol resolves correctly.

### Adding a new assembly

1. Add a `"Name": "expression"` entry under `assemblies`, using only symbols that already exist in `primitives`.
2. `npm run validate` will catch any symbol that doesn't resolve.
3. `npm run build` and visually confirm the card renders with the operators you intended (double-check operator characters, since they're not ASCII).

### Adding a new rewrite rule

1. Add a new entry under `rewrite-rules.yml`'s `rules`, with a `key`, `name`, `symbols`, `relieves` (drawn only from the declared `constraints` list), `preserves`, `changes`, `preconditions`, `tradeoffs`, and real-world `examples`.
2. `npm run validate` to confirm the `relieves` list only references declared constraints.
3. Consider whether the design doc's worked-example pattern (naive system, binding constraint, rewrite rule, feasible system) applies to your new rule, and whether it's worth adding as a new derivation in the paper.

### Wiring up CI validation (a good first task)

As noted in the design doc's "Known issues," there is currently no dedicated GitHub Actions workflow running `design-grammar/scripts/validate.mjs`. A reasonable first contribution is adding one: a small workflow triggered on changes to `design-grammar/**`, installing Node dependencies, and running `npm run validate`, modeled on the validate-dev workflows used by the other sub-projects in this monorepo (see, for example, `tinytorch-validate-dev.yml` or `mlsysim-validate-dev.yml` for the repo's general pattern of gating merges on a green validation run).
