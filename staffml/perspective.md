# StaffML: Prof. Vijay's Perspective

*The subset of [`../design_decisions.md`](../design_decisions.md) specific to StaffML, with the surrounding context you need if you're about to propose a feature here. Read [`design.md`](design.md) for what the project is; this is about what its maintainer has explicitly decided or expects, and why.*

## Discuss significant features before opening a PR

This is the one StaffML-specific process norm on record, and it's stated more explicitly here than anywhere else in the repo. A collaborator told a contributor directly:

> "I'd like you to ask for permission from prof. before building a PR. Going straight for a PR without discussing a feature can lead to the PR rejection."

Vijay's own follow-up in the same thread reinforces this in practice: he asks clarifying scope questions before green-lighting anything, rather than reacting to a finished PR. This doesn't mean every StaffML change needs sign-off first (routine bug fixes and small contributions merge normally, as the broader PR history shows), but it does mean a new feature or a meaningful scope addition (a new question format, a new mode, a new content section) should be raised as an issue or discussion before you put implementation time into a PR. Opening the PR first, in StaffML specifically, is the thing that's been flagged as risking rejection.

A dedicated contributor Discord server was proposed twice as a StaffML/community-adjacent idea and ultimately declined, not by a hard veto but by the proposer self-closing it after Vijay's tepid "I do agree it'd be nice... but I just worry about maintenance" response. If you're tempted to re-propose community infrastructure like this, the stated blocker was maintenance overhead at current scale, not the idea itself; a proposal that addresses who maintains it long-term would be worth raising.

For everything not covered above, cross-project conventions, what's still genuinely undecided, see the full [`../design_decisions.md`](../design_decisions.md).
