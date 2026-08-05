# CS249r docs

Contributor-facing design and implementation documentation for sub-projects of [`harvard-edge/cs249r_book`](https://github.com/harvard-edge/cs249r_book) (the "Machine Learning Systems" course repository).

New here? Start with [`ecosystem-map.md`](ecosystem-map.md) for how all eleven projects below actually connect to each other (shared infrastructure, deployment order, real dependencies versus apparent ones), then drill into whichever project you're contributing to. Keep [`glossary.md`](glossary.md) open alongside whatever you're reading; it defines every recurring technical term in plain language, from Quarto and Pyodide to the roofline model and CORS.

## [`book/`](book/)

The core two-volume textbook, plus its custom build/validate/publish tooling (the "Binder" CLI).

- [`design.md`](book/design.md)
- [`implementation.md`](book/implementation.md)

## [`staffml/`](staffml/)

Interview-prep question bank and practice app.

- [`design.md`](staffml/design.md)
- [`implementation.md`](staffml/implementation.md)

## [`tinytorch/`](tinytorch/)

Hands-on course where students build an ML framework from scratch.

- [`design.md`](tinytorch/design.md)
- [`implementation.md`](tinytorch/implementation.md)

## [`mlsysim/`](mlsysim/)

First-principles analytical modeling framework for ML systems, also the physics engine behind the browser-based interactive labs.

- [`design.md`](mlsysim/design.md)
- [`implementation.md`](mlsysim/implementation.md)

## [`kits/`](kits/)

Hands-on embedded ML labs for real devices (Arduino, Seeed, Raspberry Pi).

- [`design.md`](kits/design.md)
- [`implementation.md`](kits/implementation.md)

## [`mlperf-edu/`](mlperf-edu/)

A locally executable, quality-gated benchmark specification adapted from MLPerf's own discipline for classroom use.

- [`design.md`](mlperf-edu/design.md)
- [`implementation.md`](mlperf-edu/implementation.md)

## [`socratiq/`](socratiq/)

An embeddable, AI-powered learning widget for static HTML pages.

- [`design.md`](socratiq/design.md)
- [`implementation.md`](socratiq/implementation.md)

## [`design-grammar/`](design-grammar/)

A formal vocabulary and rewrite-rule catalog for deriving ML systems techniques from first principles.

- [`design.md`](design-grammar/design.md)
- [`implementation.md`](design-grammar/implementation.md)

## [`slides/`](slides/)

35 Beamer decks (Volumes I and II) plus packaged TinyML courseware.

- [`design.md`](slides/design.md)
- [`implementation.md`](slides/implementation.md)

## [`instructors/`](instructors/)

"The Blueprint": syllabi, pedagogy, assessment, and TA guidance for adopting instructors.

- [`design.md`](instructors/design.md)
- [`implementation.md`](instructors/implementation.md)

## [`site/`](site/)

The ecosystem's public front door: home, about, community, newsletter, and mini-games.

- [`design.md`](site/design.md)
- [`implementation.md`](site/implementation.md)

---

Start with each project's `design.md`, then use its `implementation.md` as your map once you're ready to make a change. All documents describe their project as of `dev` HEAD (commit `8fb87d81`) in the main repository.
