# Labs: Prof. Vijay's Perspective

*The subset of [`../design_decisions.md`](../design_decisions.md) specific to the labs, with the surrounding context you need if you're about to fix a lab bug or add hardware support. Read [`system_design.md`](system_design.md) for what the project is (labs share their product framing with `mlsysim/design.md`, there's no separate `design.md` here); this is about what its maintainer has explicitly decided or corrected, and why.*

## The widget-cascade bug scope was corrected, and a broad fix was reverted for it

If you're touching the marimo gated-cell pattern, know that the actual bug here is narrower than it first looked, and there's a specific wrong fix already tried and rejected.

An initial issue framed 32 of the 33 labs as violating a "widget-in-gated-cell" anti-pattern, implying a broad rewrite was needed. After deeper analysis, the real bug turned out to be only gated cells that leak *multiple* widgets, causing cascading undefined state; single-widget sequential-unlock gating "actually works correctly in practice." Scope was cut from 32 labs down to 14.

**PR #1349** attempted the broad fix, removing gating entirely, and was closed unmerged, explicitly called "the wrong direction (removed gates entirely rather than splitting widgets per cell)." The correct fix (per #1339, the canonical template) keeps gating intact and ensures each gated cell defines at most one widget. If you're looking at a lab with sequential-unlock gating and it seems to be "the anti-pattern," check first whether it's actually leaking multiple widgets from a single gated cell; if it's cleanly one widget per gate, it's not the bug this refactor was chasing, and removing the gating is the specific mistake already made once.

## Hardware/board scope follows the existing lab format, routed through the labs maintainer

Not a labs-specific finding on its own, but relevant if you're proposing a new board or device target: Vijay's stance (given in the context of a different lab track, kits) was that a new board is fine "as long as [it] is consistent with the format we are following," and that decisions about which boards the project actively supports (for example, moving away from the Arduino Nano 33 BLE Sense toward the Nicla Vision) are made deliberately, not left as an open door for any board a contributor wants to add. If proposing new hardware support for labs, match the existing lab structure and expect the choice of board itself to be a real design question, not just an implementation detail.

For everything not covered above, cross-project conventions, what's still genuinely undecided, see the full [`../design_decisions.md`](../design_decisions.md).
