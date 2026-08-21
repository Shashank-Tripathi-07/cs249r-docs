# Book: Prof. Vijay's Perspective

*The subset of [`../design_decisions.md`](../design_decisions.md) specific to the textbook, with the surrounding context you need if you're about to propose content or a build change here. Read [`design.md`](design.md) for what the project is; this is about what its maintainer has explicitly decided or ruled out, and why.*

## Scope was deliberately narrowed by the two-volume restructure

If you're proposing new chapter content, check it against the current volume framing first, even if a similar request was accepted years ago:

> "Volume I focuses on single machine ML systems foundations and Volume II focuses on distributed systems at scale... Meta learning and continual learning are important topics but they fall outside the core systems focus of both volumes."

A content request that had been open and accepted since 2023 (predating the two-volume restructure) was closed as out of scope once the restructure landed. "This was discussed as in-scope before" is not a guarantee it still is; the two-volume split is a real, applied filter on what belongs in the book now.

## Quiz learning objectives are intentionally loosely coupled to chapter objectives

If you notice a section's quiz learning objective doesn't word-for-word match its chapter's stated learning objectives, that's a deliberate design choice, not a drift bug to fix. The reasoning given: chapter objectives are broad, carefully revised goals, while quiz questions are section-scoped and test specific concepts, so one chapter objective naturally maps to several narrower quiz-question objectives. Vijay offered to tighten the coupling only if it would serve a concrete downstream use case, like curriculum-mapping tooling that needed the stricter link. Absent that need, don't submit a PR that just re-words quiz objectives to match chapter objectives more closely; it isn't considered a bug.

## Build and infrastructure decisions that affect content contributors

These aren't content decisions, but they affect how your content change actually gets built and published:

- The Quarto build was reworked around a **unified two-volume `_quarto.yml`**, replacing duplicated ad hoc HTML/PDF/EPUB configuration. If you're touching build config, work within that unified structure rather than adding a parallel one.
- CI build performance was solved through **full Docker containerization** (Linux and Windows), not incremental workflow-YAML fixes, citing an 80-90% build time reduction. Pre-built images live at `ghcr.io/harvard-edge/cs249r_book/`. If a build step is slow, the intended fix is usually "does this belong in the container," not "optimize this YAML step."
- **Figure and table captions must follow the format `**bold**: explanation`.** This has been enforced as a real convention, not a suggestion; a violation was flagged and fixed in the past.
- Repo clone-size bloat is being fixed at the source: **Git LFS for large binaries** (PDF/EPUB), and generated files like `corpus.json`/`bundle.js` moved to `.gitignore` since neither should be hand-edited or tracked. A second phase (a full git-history rewrite via `git lfs migrate import` and a force-push) is intentionally deferred as "a coordinated team rollout, not a code change," so don't attempt that rewrite unilaterally even if you spot the same bloat pattern elsewhere.

For everything not covered above, cross-project conventions, what's still genuinely undecided, see the full [`../design_decisions.md`](../design_decisions.md).
