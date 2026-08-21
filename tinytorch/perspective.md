# TinyTorch: Prof. Vijay's Perspective

*The subset of [`../design_decisions.md`](../design_decisions.md) specific to TinyTorch, with the surrounding context you need if you're about to contribute here. Read [`design.md`](design.md) for what the project is; this is about what its maintainer has explicitly decided or ruled out, and why, so you don't propose something already settled either way.*

## Solutions are visible on purpose, right now

If you notice that module source files under `src/` still contain full, working solutions rather than fill-in-the-blank stubs, that's not an oversight. Vijay has stated this twice, a first time in general terms and a second time more concretely:

> "The reason I haven't released the solution-free versions yet is intentional... we don't want learners implementing every single function. Some implementations are pedagogically valuable... while others are just boilerplate." (2025-12-17)

> "That's partly by design for where we want to vet the framework, till we know the solutions are rock solid." (2026-07-02)

The plan is a single, systematic pass across all 20 modules that decides which functions become fill-in-the-blank versus pre-provided, and strips `### BEGIN SOLUTION` / `### END SOLUTION` blocks wired into `tito module start`. It's deliberately not being done module by module. If you're fixing a bug and notice a module's solutions could be stripped as a side effect, that's out of scope for a bug-fix PR; the strip-down happens once, across everything, when the framework is judged stable.

## Where new algorithms/optimizers actually get accepted

Core modules (Module 07's optimizer ladder is the concrete example on record) are held to a "durable foundation" bar, not a novelty bar. Muon was rejected for core inclusion with this reasoning:

> "TinyTorch's job in these core modules is to build that durable foundation from scratch, so I try to be deliberate about what earns a slot... Muon is newer and still proving itself... I would rather let it settle before it goes into a foundations course."

The suggested path for a newer technique is an optional advanced example, not a core module addition. If you're proposing a new algorithm for a core module (01 through 13), expect the bar to be "has this settled into being a standard part of the field," not "is this good/interesting."

## Source of truth and branch target, stated directly

Two mechanics worth confirming explicitly rather than assuming from the file layout:

- `src/NN_name/NN_name.py` is the source of truth. The exported package under `tinytorch/tinytorch/` is generated and should never be hand-edited, confirmed by Vijay directly, not just inferred from the build pipeline in `implementation.md`.
- PRs target `dev`, not `main`. This was confirmed the hard way: a PR based on `main` couldn't merge cleanly into `dev`, and Vijay manually reapplied the change on `dev` himself (preserving the original author's attribution) rather than asking for a rebase. `dev` is the real integration branch.

## Command naming and expected behavior

- The correct contributor-facing export command is `tito dev export`, scoped to module authors. `tito src export` does not exist and was never intended to; a contributor proposed it under a mistaken assumption.
- Arena/leaderboard sync lag between `tito` publishing results and the community dashboard updating is expected, not a bug: "there's an expected lag... This is a known sync delay, not a bug." If a student reports this, it's a documentation gap (nobody has written this down for them), not a pipeline bug to chase.

## Versioning

TinyTorch's semantic versioning, `pyproject.toml`-as-source-of-truth, and `tinytorch-v*` tag scheme were built to explicitly mirror the book's own `book-v*` versioning, for consistency across the whole repo, not designed independently. See `design_decisions.md`'s "Versioning and release process" section for the source.

For everything not covered above (rejected approaches elsewhere in the repo, cross-cutting naming/CI conventions, what's still genuinely undecided), see the full [`../design_decisions.md`](../design_decisions.md).
