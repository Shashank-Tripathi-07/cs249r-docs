# Lecture Slides: Design

*This is the contributor-facing design document for the Lecture Slides sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `slides/` in that repo. It explains what the slides project is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Project history" at the end covers a real build-tooling decision that shaped the current setup, and "Known issues" lists documented gaps.*

## Problem

An instructor adopting the MLSysBook textbook for their own course needs lecture material, not just reading material. Writing 35 chapter-length slide decks from scratch, for both volumes of the book plus a TinyML track, is a large undertaking most adopting instructors won't do themselves, and slides that don't visually and pedagogically match the book they accompany create friction rather than removing it.

The Lecture Slides project is the textbook's ready-made answer to that: one Beamer deck per chapter, branded consistently, drop-in usable for a course built around the book, distributed as both a browsable web portal (for finding and previewing decks) and downloadable, editable source files (for an instructor who wants to adapt them).

## Goals

- One self-contained Beamer slide deck per textbook chapter: 17 decks for Volume I (Foundations), 18 for Volume II (At Scale), covering both volumes end to end.
- A consistent visual brand across every deck (a shared "Crimson" Beamer theme, Harvard-branded), with a small, common macro API so decks look and feel like one coherent product rather than 35 independently styled documents.
- Each chapter deck fully self-contained (its own directory, its own images), so an instructor can take exactly the one deck they need without pulling in the whole project.
- Easy re-theming: an instructor should be able to swap the shared theme file to restyle every deck without touching any individual chapter's `.tex` source.
- A browsable, public web portal describing every deck and linking to downloads, separate from the decks themselves, so someone deciding whether to adopt the material can evaluate it before committing to a full LaTeX toolchain.
- A PowerPoint export path alongside the native PDF, since not every instructor's environment or workflow is LaTeX-based.
- Coverage of the TinyML track as well, by packaging in an external, actively maintained TinyML courseware repository rather than duplicating that content here.
- Free and open source, matching the parent textbook's license, versioned alongside the rest of the `cs249r_book` monorepo.

## Non-goals

- Not a code-generation pipeline from the textbook's own chapter content. Slides are hand-authored LaTeX, topically aligned with book chapters by convention (matching numbering and titles), not mechanically derived from the book's `.qmd` source.
- Not a revealjs or other HTML-native slide format. The decks themselves are Beamer PDFs; Quarto is used only to build the surrounding web portal that describes and links to them, not to render the slides themselves.
- Not the source of the TinyML track's actual slide and reading content. That content lives in and is maintained by a separate, external repository, and this project only packages a snapshot of it at release time.
- Not editable, presentation-ready PowerPoint. The PPTX export is image-based (each PDF page rendered as a picture and placed on a slide), not an editable, native PowerPoint document with live text.

## Technology stack

| Technology | What it is | How the Lecture Slides project uses it |
|---|---|---|
| LaTeX Beamer, with the `metropolis` theme as a base | A LaTeX document class for presentations, and a modern, minimalist Beamer theme. | Every one of the 35 chapter decks is a Beamer `.tex` source file, styled through a shared, custom theme file layered on top of the base theme. |
| `pdflatex` | A LaTeX engine that compiles `.tex` source to PDF. | The current compilation engine for every deck; see "Project history" for why this project deliberately does not use `xelatex`, despite that being a common modern choice for font-rich documents. |
| Inkscape | A vector-graphics editor with a command-line export mode. | Converts every chapter's hand-drawn SVG diagrams to PDF before the LaTeX build, since `pdflatex` can't include SVG directly. |
| Quarto | A publishing system built on Pandoc. | Builds the public, browsable HTML portal at `mlsysbook.ai/slides/`, describing every deck and linking to downloads; it does not render the decks themselves. |
| Poppler (`pdftoppm`) and `python-pptx` | A PDF-rendering command-line tool, and a Python library for creating PowerPoint files. | Together they implement the PDF-to-PPTX export: each PDF page is rasterized to a high-resolution image via `pdftoppm`, then placed full-bleed on a 16:9 slide via `python-pptx`. |
| GNU Make | A build-automation tool. | Orchestrates per-chapter and whole-volume builds, SVG conversion, PPTX export, and an overflow-detection quality check, all through one `Makefile`. |

## Architecture

### One self-contained deck per chapter

Every chapter lives in its own directory under `slides/vol1/` or `slides/vol2/`, named to match the book's own chapter numbering and topic (for example, `vol1/05_nn_computation/`, `vol2/05_distributed_training/`). Each directory holds exactly one `.tex` source file and its own `images/` subdirectory. The only resources shared across every deck are the Beamer theme file and a small set of logo images, kept in a top-level `assets/` directory; everything else, including diagrams, is chapter-local. This is a deliberate design choice: an instructor who wants only the deck for one chapter can copy that one directory and have everything it needs, with no cross-chapter dependency to resolve.

### The shared theme and macro API

A single file, the custom Beamer theme (Crimson, Harvard-branded, white background), is what every deck includes to get its visual identity. On top of the base theme, the project defines a small, consistent macro API (deck-title macros, focus-slide macros, card-layout macros, and similar) that every chapter's `.tex` source calls into rather than writing raw Beamer layout code directly. The practical effect: an instructor who wants to re-theme every deck, say, for their own institution's branding, only needs to replace the one theme file; no individual chapter source needs to change, since none of them hardcode colors or layout directly.

### Two build outputs, two different tools

This project has an unusual split worth understanding clearly: the Quarto project (`_quarto.yml`) builds only an HTML **portal** site, navigation and description pages that link to and describe the decks. It is explicitly not a `revealjs` presentation format and does not render slide content itself. The actual slide decks are compiled separately, via the `Makefile`, using `pdflatex` directly on each chapter's `.tex` source. A contributor working on portal copy and navigation touches `.qmd` files and runs `quarto render` or `quarto preview`; a contributor working on actual slide content touches `.tex` files and runs `make`. These are two independent workflows that happen to live in the same directory.

### Building a deck: SVG conversion, then LaTeX

Because `pdflatex` cannot include SVG images directly, every chapter's hand-drawn SVG diagrams are converted to PDF via Inkscape's command-line export as a prerequisite step before any chapter can compile. The `Makefile` handles this automatically; a chapter build target depends on the SVG-conversion target, so running `make ch05` (for example) converts that chapter's SVGs and then compiles its LaTeX source, without a contributor needing to remember or invoke the conversion step separately.

### PowerPoint export

For instructors whose workflow isn't LaTeX-based, every deck can be exported to PPTX. This is not a native, editable export: a small Python script rasterizes each page of the compiled PDF to a high-resolution PNG via `pdftoppm`, then places that image full-bleed on a corresponding slide in a generated PowerPoint file via `python-pptx`. The result opens and presents correctly in PowerPoint, but the text on each slide is a picture, not editable text; an instructor who needs to edit content should work from the LaTeX source directly and recompile, not edit the PPTX.

### TinyML: packaged, not authored here

The TinyML track's slides and readings are not authored in this project. They live in and are actively maintained by a separate, external repository (the same HarvardX/edX TinyML courseware project referenced elsewhere in the MLSysBook ecosystem). This project's `tinyml/` directory holds only a copy of that external repository's own README for reference; the actual content is fetched fresh from the external repository and packaged into this project's release artifacts only at publish time, so this project never carries a stale, out-of-sync copy of content someone else is actively maintaining.

### Quality gate: overflow detection

Beyond the structural checks described in "Testing" below, the `Makefile` includes a dedicated quality target that builds every deck and fails if any slide has more than a small threshold of LaTeX "Overfull hbox" warnings, the standard LaTeX signal that content doesn't fit its intended box and is likely overflowing visibly off the slide. This is a lightweight, automatable proxy for "does this slide actually look right," without needing a human to visually review all 35 decks on every change.

### CI and deployment

Slide PDF and PPTX compilation, the portal site build, and structural validation (SVG well-formedness, matched Beamer frame begin/end pairs, a check for missing speaker notes) run through dedicated GitHub Actions workflows on every relevant change, gating a manual, confirmation-required production publish. The compiled decks and their PPTX exports are distributed as downloadable GitHub Release assets rather than embedded directly into the published portal site; the portal site itself, built from the Quarto project, is what's actually published to `mlsysbook.ai/slides/`.

## Known issues

These are good starting points if you're looking for a first contribution.

- **There is no automated visual regression check for slide content, only a text-overflow proxy.** The overflow-detection quality gate catches text that doesn't fit its box, but nothing catches a slide that compiles cleanly and still looks wrong (a misaligned image, an illegible color contrast, incorrect diagram scaling). This kind of issue currently depends on a human noticing.
- **The CI compilation engine and the local `Makefile`'s compilation engine have, at points, disagreed.** The project's own build tooling documents a real, previously-encountered bug where `xelatex` silently fell back to a different, unbranded font on a Linux CI runner when the intended font wasn't available, producing decks with blank or wrong-looking text with no build failure to flag it. The `Makefile` was switched to `pdflatex` specifically to avoid this class of failure; make sure any CI workflow changes you're making don't reintroduce a font-substitution risk by switching back.
- **The PPTX export is not an editable format**, only an image-per-slide rendering of the compiled PDF. An instructor expecting to directly edit exported PowerPoint text will be surprised; this is a real, documented limitation rather than a bug, but it's worth being explicit about in any contributor-facing or instructor-facing material you touch.

## Project history

- **The project switched its LaTeX compilation engine from `xelatex` (with `fontspec`) to `pdflatex` (with the `helvet`/`courier` packages) specifically because of a font-substitution bug caught on Ubuntu CI.** `xelatex` silently fell back to a substitute font (Liberation Sans) when the intended font wasn't installed on the CI runner, which produced decks that looked wrong, in some cases with effectively blank visible text, without the build itself ever failing or warning. Because the failure was silent, it shipped before being caught. Switching to `pdflatex` with explicitly bundled font packages removed the dependency on a system font actually being present, closing off that entire failure mode rather than just detecting it after the fact.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, the real `Makefile` targets, the theme and macro system, the PPTX export pipeline, and common contribution workflows for adding or editing a deck. The "Known issues" list above is a reasonable place to find a first task.
