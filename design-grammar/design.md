# ML Systems Design Grammar: Design

*This is the contributor-facing design document for the ML Systems Design Grammar, a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `design-grammar/` in that repo. It explains what the design grammar is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers real design decisions that shaped the current architecture, and "Known issues" lists documented gaps, both good places to look for a first contribution.*

## Problem

Modern ML systems techniques (FlashAttention, ZeRO, PagedAttention, Megatron-LM-style parallelism) tend to look like separate, independently-invented tricks, each with its own name and its own paper. In reality, most of them recombine a small, stable set of building blocks (tensors, memory tiers, communication primitives, scheduling mechanisms) in response to a specific binding constraint (not enough HBM capacity, not enough bandwidth, too much latency). Without a shared vocabulary for those building blocks, students and engineers end up memorizing a growing catalog of named techniques instead of learning to derive a new one from first principles when they hit a new constraint.

The Design Grammar project exists to give that vocabulary a precise, structured form: a catalog of primitives, a small set of composition operators for combining them, typing rules for what's a valid composition, a cost model for reasoning about constraints, and a catalog of rewrite rules (tiling, fusion, sharding, quantization, and similar) that map a specific binding constraint to the transformation that relieves it. The core teaching loop the project is built around is: naive system plus a binding constraint, apply a rewrite rule, arrive at a feasible system, the same reasoning path that actually produced techniques like FlashAttention.

## Goals

- A single, versioned catalog of ML systems primitives (mathematical objects, algorithmic blocks, runtime mechanisms, hardware resources, production controls, and measurements), each classified along two axes: an abstraction layer (data through production) and an information-processing role (represent, compute, communicate, control, measure).
- A small, fixed set of composition operators (sequence, parallel, conditional, adversarial, feedback loop, repeated group) that let any named system (a Transformer, a Mixture of Experts, a diffusion model) be written as a short symbolic expression over the primitive catalog, rather than described only in prose.
- A companion catalog of rewrite rules, each naming which constraint it relieves, what it preserves, what it changes, its preconditions, and real named systems that demonstrate it, so the grammar is directly actionable for design reasoning, not just a taxonomy.
- One canonical, machine-readable source of truth (`grammar.yml` and `rewrite-rules.yml`) that every downstream consumer, a standalone browsable HTML visualization, the StaffML interview-prep app's "Framework" page, and a formal research paper, is generated from or reads directly, so the catalog can never drift into multiple disagreeing copies.
- A validated, versioned artifact: every change to the catalog is checked for structural consistency (no accidental symbol collisions, every composition link and assembly expression resolves to a real primitive) before it's trusted by any downstream consumer.
- A formal research paper demonstrating the grammar's explanatory power by deriving several real, state-of-the-art systems from first principles using only the catalog and the rewrite rules.
- Free and open source, versioned alongside the rest of the `cs249r_book` monorepo.

## Non-goals

- Not a general-purpose ML systems textbook chapter; it's a compact, structured reference artifact, meant to be read alongside the textbook and used as a design tool, not to replace either.
- Not an executable simulator. Unlike MLSys·im (which computes real latency, memory, and cost numbers), the Design Grammar's cost semantics are qualitative, naming which physical resource a rewrite rule relieves, not computing a numeric prediction.
- Not a repo-wide shared taxonomy consumed by every sub-project. As of this document, StaffML's "Framework" page is the only real downstream consumer of the generated catalog; the book, kits, and slides projects don't reference it.
- Not independently CI-validated as its own gate. There is no dedicated GitHub Actions workflow that runs the catalog's own validator on every change; see "Known issues."

## Technology stack

| Technology | What it is | How the Design Grammar uses it |
|---|---|---|
| YAML | A human-readable data-serialization format. | `grammar.yml` (the primitive and assembly catalog) and `rewrite-rules.yml` (the rewrite-rule playbook) are the two canonical source files everything else is generated from or validated against. |
| JSON Schema | A vocabulary for validating the shape of JSON (and, by extension, YAML) documents. | `grammar.schema.json` formally documents the required shape of `grammar.yml`, and states explicitly that the file is consumed by both the standalone HTML emitter and the StaffML React app, the two real downstream consumers. |
| Node.js (plain ESM scripts) | A JavaScript runtime. | All three build/validation scripts (`build-html.mjs`, `validate.mjs`, `migrate-html-to-yaml.mjs`) are small, dependency-light Node scripts, not a full application; the only runtime dependency across the whole toolchain is `js-yaml`. |
| A hand-written HTML/CSS/JS single-page artifact | A self-contained, dependency-free web page. | `index.html` is both the template and the output of the catalog build: an interactive, searchable, periodic-table-style browser of all primitives and assemblies, regenerated from `grammar.yml` on every build via sentinel-comment injection rather than a full templating engine. |
| LaTeX | A typesetting system. | Builds the standalone research paper (`paper/paper.tex`), which formalizes the grammar as a five-tuple and validates it with real worked derivations of named systems. |
| A Python figure-generation script | A small script reading the same YAML the rest of the project reads. | `paper/scripts/generate_primitive_catalog.py` renders the paper's primitive-catalog figure as an SVG directly from `grammar.yml`, so the paper's figure and the interactive catalog can never show different data. |

## Architecture

### The grammar as a formal five-tuple

The project's own conceptual framing (`DESIGN_GRAMMAR.md`) is a formal grammar `G = (P, O, T, C, R)`:

- **P, the primitive vocabulary**: everything in `grammar.yml`'s `primitives` list, mathematical objects, algorithmic blocks, runtime mechanisms, hardware resources, production controls, and measurements.
- **O, composition operators**: a small, fixed "System Assembly Notation": sequence, parallel composition, conditional branching, adversarial pairing, a feedback loop, and a repeated group, used to write a named system as a short symbolic expression.
- **T, typing and validity rules**: what makes a composition valid, role, layer, residency, dependency, and rewrite-precondition constraints.
- **C, cost semantics**: the physical resources a design has to respect, capacity, bandwidth, latency, utilization, fragmentation, energy, reliability, and cost.
- **R, rewrite rules**: the catalog in `rewrite-rules.yml`, each one a named transformation (tiling, fusion, sharding, and so on) that relieves a specific constraint while preserving specified properties.

The worked example the project itself uses to motivate this: naive attention materializes an O(n squared) score matrix in HBM, which violates the capacity and bandwidth constraints at long sequence lengths; applying the tiling and fusion rewrite rules in sequence produces exactly the execution pattern FlashAttention uses, derived from the grammar rather than looked up as a named trick.

### The primitive catalog (`grammar.yml`)

Every primitive is classified on two axes: an abstraction **layer** (1 Data, 2 Math, 3 Algorithms, 4 Architecture, 5 Optimization, 6 Runtime, 7 Hardware, 8 Production) and an information-processing **role** (Represent, Compute, Communicate, Control, Measure, each spanning several columns and carrying its own color for the visualization). As of this document the catalog holds 90 primitives, each with a short two-letter symbol, a name, its role and layer and column position, an optional year (many entries are tagged with when the underlying mathematical or engineering idea first appeared), a description, links to related primitives, and a rationale for its inclusion.

`assemblies`, a second section of the same file, defines 51 named, real systems (Transformer, Mixture of Experts, Diffusion Model, and similar) as short symbolic expressions over the primitive symbols and the composition operators. A `known_collisions` section documents cases where the same symbol is deliberately reused for two different primitives, with a note explaining the intentional collision, since the catalog is dense enough (90 primitives across an 18-column, 8-row grid) that a small number of symbol collisions are expected rather than accidental.

### The rewrite-rule playbook (`rewrite-rules.yml`)

A separate, smaller YAML file defining the constraint vocabulary (capacity, bandwidth, latency, utilization, fragmentation, energy, reliability, cost) and twelve named rewrite rules: tiling, fusion, sharding, factorization, pipelining, batching, caching, prefetching, quantization, sparsification, virtualization, scheduling, and routing (plus replication). Each rule states which constraints it relieves, what it preserves and changes, its preconditions, its tradeoffs, and real named systems where it's used, for example tiling and fusion both cite FlashAttention, sharding cites ZeRO and FSDP, virtualization cites PagedAttention, and routing cites Mixture of Experts. This is the part of the project that makes the grammar prescriptive rather than purely descriptive: given a constraint a student has hit, this file is where they look for the transformation that relieves it.

### Build and validation pipeline

`grammar.yml` and `rewrite-rules.yml` are hand-edited directly; they are the source of truth (the header comment in `grammar.yml` notes the file was originally extracted from `index.html`'s inline data via a one-time migration script, but is now canonical in the other direction). Three small Node scripts operate on them:

- `build-html.mjs` regenerates `index.html` by treating the existing file as a template, locating sentinel HTML comments (`&lt;!-- @gen:roles --&gt;`, `&lt;!-- @gen:primitives --&gt;`, `&lt;!-- @gen:assemblies --&gt;`), and replacing the content between them with freshly rendered markup from `grammar.yml`, running its own validation pass before writing.
- `validate.mjs` is the structural checker: it confirms every primitive's fields are well-formed (correct symbol pattern, layer and column and role within valid ranges), that no two primitives collide on the same layer-and-column cell without being documented in `known_collisions`, that every composition link and every assembly expression's symbols resolve to a real primitive, and that every rewrite rule's `relieves` list only names constraints actually declared in the constraint vocabulary.
- `migrate-html-to-yaml.mjs` is a one-shot, recovery-only tool that extracts the original inline JavaScript data out of `index.html` back into YAML; it's not part of the normal edit workflow and exists only for disaster recovery if the YAML were ever corrupted.

### The standalone visualization (`index.html`)

A single, self-contained HTML file (no build step needed to view it, just open it in a browser) presenting the catalog as an interactive, searchable, periodic-table-style grid: 8 rows for the abstraction layers, 18 columns for the roles, with each cell showing a primitive's symbol and a hover tooltip. Below the grid, a second section renders all 51 assemblies as cards showing their symbolic expressions, with a legend explaining the composition operators, and hover tooltips on individual symbols within an expression linking back to the primitive it refers to. This file is both the visual "front door" to the catalog for someone encountering it for the first time, and the literal build target of `build-html.mjs`.

### Downstream consumption: StaffML

As of this document, the one real, wired-in downstream consumer of the catalog outside `design-grammar/` itself is StaffML's `/framework` page (part of the `interviews/staffml/` sub-project). `interviews/staffml/scripts/sync-design-grammar.mjs` reads `design-grammar/grammar.yml` directly and generates `interviews/staffml/src/data/designGrammar.ts`, a TypeScript data module StaffML's React code imports from. This sync runs automatically as part of StaffML's own `predev` and `prebuild` npm lifecycle hooks, so any StaffML contributor building or running the app locally transitively regenerates this file without needing to know the Design Grammar project exists. The StaffML `/framework` page is, by its own code comment, "a pixel-port" of `design-grammar/index.html`'s visual design, reimplemented as a React page rather than embedding the standalone HTML file directly.

### The research paper

`design-grammar/paper/paper.tex` is a full, standalone LaTeX research paper, "A Design Grammar for ML Systems: Primitives, Constraints, and Rewrite Rules," presenting the formal five-tuple, the primitive catalog, and, as its central validation, five end-to-end worked derivations of real state-of-the-art systems from first principles using only the grammar, including a "dead-end analysis" showing a case (a million-token context window on an edge device) where the grammar's constraints reveal the design is infeasible rather than merely difficult. The paper's own primitive-catalog figure is generated directly from `grammar.yml` by a small Python script, rather than hand-drawn or copy-pasted from the HTML visualization, so the figure in the paper and the interactive catalog can never disagree.

### The build orchestration (`Makefile`)

A `Makefile` at the project root ties the whole pipeline together: `make html` regenerates `index.html`, `make validate` runs the structural validator, `make sync` invokes StaffML's own sync script directly (so the Design Grammar project can push a fresh generated file into StaffML without a contributor needing to know StaffML's own build commands), and `make all` runs the HTML build and the StaffML sync together. `make clean` is destructive and removes generated artifacts.

## Known issues

These are good starting points if you're looking for a first contribution.

- **There is no dedicated CI workflow that validates `design-grammar/` on its own.** No GitHub Actions workflow runs `design-grammar/scripts/validate.mjs` or `make validate` directly. The only indirect CI touchpoint is a comment in `book-validate-dev.yml` referencing a past fix to the project's entry in the monorepo's all-contributors tooling, unrelated to catalog validation itself. This means a structurally broken `grammar.yml` or `rewrite-rules.yml` (a bad symbol, an unresolved composition link) could be merged to `dev` without any CI check catching it; it would only surface later, when someone happened to run the validator locally or when StaffML's generated file broke something downstream.
- **The catalog's only confirmed downstream consumer is StaffML.** Despite living at the monorepo root as a shared resource, no other sub-project (the book, kits, slides) references `grammar.yml` or the generated HTML. If the intent is for this to be a genuinely shared, ecosystem-wide vocabulary, that adoption gap is worth understanding before investing further in the catalog's breadth.
- **`migrate-html-to-yaml.mjs` is a one-way, recovery-only tool but isn't clearly marked as dangerous in its own interface.** Running it against a corrupted or out-of-date `index.html` would silently regenerate `grammar.yml` from stale data, overwriting real edits. Its own comments warn about this, but there's no runtime guard (a confirmation prompt, a dry-run mode) preventing an accidental run.

## Project history

- **`grammar.yml` did not start as the source of truth.** The catalog originally lived entirely as inline JavaScript data inside `index.html`. `migrate-html-to-yaml.mjs` was written specifically to extract that inline data into the current YAML format once, after which the direction of generation flipped: YAML became canonical, and `index.html` became a build target regenerated from it via sentinel-comment injection rather than hand-edited directly.
- **The project reached v0.2 with 90 primitives and 51 assemblies** as of this document, tracked directly in the catalog's own version field and displayed in the standalone HTML visualization's header badge, giving future contributors a simple way to confirm whether their local checkout matches what's described here.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, real YAML entries, the validator's actual checks, and common contribution workflows for adding a new primitive, a new assembly, or a new rewrite rule. The "Known issues" list above is a reasonable place to find a first task, particularly wiring up a dedicated CI validation workflow, since that gap is concrete and self-contained.
