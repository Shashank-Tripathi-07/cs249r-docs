# Lecture Slides: Implementation Reference

> **Status: as-built, contributor-facing.** The Lecture Slides project is a live, already-implemented collection of 35 chapter decks plus a web portal. This document is your map for reading and modifying the real source: file paths and representative build commands pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| A chapter's slide content (`.tex` source) | A LaTeX distribution providing `pdflatex`, plus Inkscape (for SVG-to-PDF diagram conversion) and GNU Make. |
| PPTX export | The above, plus `poppler-utils` (for `pdftoppm`), Python 3, and `python-pptx`/`Pillow`. |
| The web portal (`.qmd` pages) | Quarto. No LaTeX needed for portal-only work. |
| The overflow quality check | The full LaTeX/Inkscape/Make setup above; it builds every deck. |

## Repository layout

```
cs249r_book/
  slides/
    _quarto.yml                  # Web portal Quarto project (HTML only, not slide rendering)
    index.qmd, vol1.qmd, vol2.qmd, tinyml.qmd, teaching.qmd, 404.qmd
    tinyml-fundamentals.qmd, tinyml-applications.qmd, tinyml-deploying.qmd, tinyml-mlops.qmd
    assets/
      beamerthememlsys.sty        # The shared Crimson theme + macro API
      img/                          # Shared logos only
    vol1/
      00_course_overview/
      01_introduction/{01_introduction.tex, images/}
      ...
      16_conclusion/
    vol2/
      00_course_overview/
      01_introduction/ ... 17_conclusion/
    tinyml/
      README-edx-original.md       # Copy of the external courseware repo's README only
    mlsysim_tutorial.tex            # Standalone ISCA-tutorial deck (loose, not part of vol1/vol2)
    scripts/
      pdf2pptx.py                    # PDF -> PPTX exporter
    Makefile
    _build/                          # Portal HTML build output (Quarto)
  .github/workflows/
    slides-validate-dev.yml
    slides-preview-dev.yml
    slides-publish-live.yml
    slides-build-pdfs.yml
```

---

## 1. The `Makefile`: real targets

```makefile
LATEX := pdflatex
PDF2PPTX := python3 scripts/pdf2pptx.py

ch01: build-01_introduction
...
ch16: build-16_conclusion
v2ch01: build-v2-01_introduction
...
v2ch17: build-v2-17_conclusion

vol1:      # build every Volume I chapter
vol2:      # build every Volume II chapter
all:       # vol1 + vol2

svgs:      # convert every chapter's SVGs to PDF via Inkscape, prerequisite for LaTeX builds

pptx:          # export every built PDF to PPTX
pptx-vol1:
pptx-vol2:
pptx-ch01: ... pptx-ch16:
pptx-v2ch01: ... pptx-v2ch17:

check:     # build everything; fail if any deck exceeds MAX_OVERFLOW=10 "Overfull hbox" warnings

clean:     # remove build artifacts from source dirs and delete _build/
```

Build outputs land in `_build/vol1/` and `_build/vol2/`, keeping each chapter's own source directory free of generated files (`.aux`, `.log`, `.nav`, compiled PDFs). PPTX outputs land alongside the PDFs in the same `_build/vol{1,2}/` directories.

The engine choice (line near the top: `LATEX := pdflatex`) is deliberate; see the design doc's "Project history" for why `xelatex` was replaced.

---

## 2. A chapter deck: real structure

Every chapter directory, for example `slides/vol1/05_nn_computation/`, contains:

```
05_nn_computation.tex   # the Beamer source
images/                  # chapter-local diagrams, including hand-drawn SVGs
```

The `.tex` file includes the shared theme:

```latex
\documentclass{beamer}
\usetheme{mlsys}   % from assets/beamerthememlsys.sty
\mlsystitle{...}
\mlsysfocus{...}
% ... chapter content using the shared macro API ...
```

To restyle every deck at once, edit `assets/beamerthememlsys.sty` only; no individual chapter file references colors or layout directly, they call into the theme's macros.

---

## 3. `assets/beamerthememlsys.sty`: the shared theme

Defines the "Crimson" visual identity (Harvard crimson accents, white background, branded footer) on top of a base Beamer theme, plus the common macro API every deck's content is written against (title/focus/card-style macros, and similar). This is the single file to touch for a repo-wide restyle, and the file to read first if you're trying to understand what visual building blocks are available when authoring new slide content.

---

## 4. SVG diagrams and the Inkscape conversion step

Each chapter's hand-drawn diagrams live as SVG in that chapter's `images/` directory. `pdflatex` cannot include SVG directly, so `make svgs` (a prerequisite of every chapter build target) runs:

```
inkscape --export-type=pdf &lt;chapter&gt;/images/*.svg
```

producing a PDF sibling for every SVG, which the chapter's `.tex` source then includes via a normal LaTeX graphics command. You don't need to invoke this separately; `make ch05` (for example) depends on the SVG target and runs it automatically.

---

## 5. `scripts/pdf2pptx.py`: the PPTX exporter

```python
"""
Converts a PDF slide deck to PowerPoint by rendering each page to a
high-resolution PNG via pdftoppm (poppler), then placing it full-bleed
on a 16:9 slide. Image-based output, not editable text.
"""
# Dependencies: pdftoppm (poppler-utils), python-pptx, Pillow.
# CLI: single file (-o/--output), or batch mode (--output-dir, --dpi).
```

Default resolution is 300 DPI. Invoked by the `Makefile`'s `PDF2PPTX` variable, and by CI directly in both the artifact-build and the publish workflows. Because output is image-based, don't treat the generated PPTX as an editable source; edit the LaTeX and recompile instead, then re-export.

---

## 6. The web portal (`_quarto.yml` and `.qmd` pages)

`_quarto.yml` is a Quarto `website` project (not `revealjs`, not `beamer`), output directory `_build`, publishing to `mlsysbook.ai/slides/`. Its header comment is explicit about the HTML-only scope: the actual decks are built separately via the `Makefile`, not by `quarto render`.

Content pages:

- `index.qmd`: portal homepage.
- `vol1.qmd` / `vol2.qmd`: per-volume listing and description of every chapter deck.
- `tinyml.qmd` plus `tinyml-fundamentals.qmd`, `tinyml-applications.qmd`, `tinyml-deploying.qmd`, `tinyml-mlops.qmd`: overview and per-course-module pages for the packaged-in TinyML courseware.
- `teaching.qmd`: a teaching guide covering semester-length customization (16-week, 32-week, and shorter quarter-length plans).
- `404.qmd`: custom not-found page.

`mlsysim_tutorial.tex` is a standalone LaTeX Beamer file living loose at the `slides/` root (not under `vol1/`/`vol2/`, and not built or referenced by the `Makefile` or `_quarto.yml`). It's the ISCA-conference-tutorial deck for the separate MLSys·im project, using the `metropolis` theme rather than this project's own `beamerthememlsys.sty`. If you're looking for it in the normal chapter-deck build flow, you won't find it there; build it directly with your own LaTeX invocation if you need to touch it.

---

## 7. The TinyML track: how it's actually assembled

`slides/tinyml/` in this repository holds only `README-edx-original.md`, a copy of the external courseware repository's own README, kept for reference. The real TinyML slide and reading content is never stored in this repository. At publish time (see section 8 below), the production release workflow does a fresh, shallow clone of the external `tinyMLx/courseware` repository and zips its `edX/slides/` and `edX/readings/` directories directly into this project's GitHub Release assets. If you're trying to edit TinyML slide content, you're in the wrong repository; changes need to go to the upstream courseware project instead, and will show up here automatically on the next release.

---

## 8. CI/CD implementation notes

### 8.1 `slides-validate-dev.yml`

Triggers on pull requests and pushes to `dev` touching `slides/**`, and is reusable (`workflow_call`) so the preview workflow can invoke it as a gate. Jobs:

- `validate-svgs`: checks every SVG under `vol1/`/`vol2/` for well-formed XML.
- `build-site`: a Quarto HTML portal render sanity check.
- `validate-tex`: checks that every deck's `\begin{frame}`/`\end{frame}` pairs are balanced, and warns (non-blocking) on frames missing a `\note{}` speaker note.
- `check-links`: a non-blocking external link check over the portal's `.qmd` content.
- `summary`: aggregates the above.

No deck compilation happens here; that's a separate, heavier job (`slides-build-pdfs.yml`).

### 8.2 `slides-build-pdfs.yml`

Triggered on push to `dev` touching `.tex`/`.svg`/`assets/**`/the `Makefile`, or manually. Installs a full TeX Live distribution, Inkscape, and `poppler-utils`, runs `make svgs`, then loops over every chapter directory in both volumes compiling each deck (twice, the standard LaTeX practice for resolving cross-references and the table of contents within a document), converts each result to PPTX via `scripts/pdf2pptx.py`, and uploads two artifacts, `slides-vol1` and `slides-vol2`, with a 30-day retention. Produces artifacts only; nothing is deployed from this workflow directly.

### 8.3 `slides-preview-dev.yml`

Triggers on push to `dev` touching `slides/**`, gated on `slides-validate-dev.yml` passing. Renders the Quarto portal, rewrites URLs for the dev-preview base path, updates the announcement banner, and deploys via SSH to a separate dev-preview GitHub Pages repository, distinct from the production `mlsysbook.ai` domain.

### 8.4 `slides-publish-live.yml`

Manual only, requires typing `PUBLISH` to confirm, gated on the latest `slides-validate-dev.yml` run on `dev` being green. In order: bumps the project's version and tag (pattern `slides-v*`), compiles all 35 decks with `pdflatex` and converts each to PPTX, packages Volume I and Volume II PDF-plus-PPTX archives, does the fresh external clone of `tinyMLx/courseware` described in section 7 and zips its content in alongside, publishes or updates a GitHub Release tagged `slides-latest` with all of the above as downloadable assets, separately renders and deploys the Quarto portal itself to the `gh-pages` branch (`mlsysbook.ai/slides/`), and finally creates a versioned tag and draft release.

Note the split: the browsable portal site is deployed to `mlsysbook.ai/slides/` via `gh-pages`, while the actual compiled decks (PDF and PPTX, for both the textbook chapters and the packaged TinyML content) are distributed as GitHub Release assets, not embedded into the site itself.

---

## 9. Local development workflow

1. `cd slides` and confirm you have a LaTeX distribution, Inkscape, and GNU Make available.
2. For a single chapter: `make ch05` (Volume I) or `make v2ch05` (Volume II). This runs the SVG conversion prerequisite automatically, then compiles that one chapter.
3. For everything: `make all` (slow; compiles all 35 decks).
4. `make check` before opening a PR that touches slide content, to catch text-overflow issues the same way CI's build job would surface them, without waiting for CI.
5. If you need a PowerPoint version to test: `make pptx-ch05` (or the equivalent for the chapter you're working on).
6. For portal-only work (copy, navigation, the teaching guide), no LaTeX toolchain is needed: `quarto preview` from `slides/` is enough.
7. `make clean` if your build state gets confusing; it removes all generated artifacts and `_build/` without touching any source `.tex` or `.svg` file.

---

## 10. Common contribution workflows

### Editing an existing chapter's slide content

1. Edit the chapter's `.tex` file directly, using the shared macro API from `assets/beamerthememlsys.sty` rather than raw Beamer layout commands, so your slide stays visually consistent with the rest of the deck automatically.
2. If you're adding a new diagram, author it as SVG in that chapter's `images/` directory; `make svgs` will convert it for you on the next build.
3. `make ch&lt;NN&gt;` (or `make v2ch&lt;NN&gt;`) to build just that chapter and visually review the output PDF.
4. `make check` before opening a PR, to catch overflow issues across the whole set, not just the chapter you touched, in case a shared-theme change had knock-on effects elsewhere.

### Re-theming every deck

1. Edit `assets/beamerthememlsys.sty` only. Do not touch individual chapter `.tex` files for a pure visual change; if you find yourself needing to, that's a sign the macro API doesn't yet expose the customization point you need, worth raising as its own issue rather than hand-patching every chapter.
2. `make all` to rebuild everything and visually spot-check a representative sample of chapters across both volumes, since a theme change can interact differently with different chapters' content density.
3. `make check` to confirm the theme change didn't introduce new overflow warnings anywhere.

### Adding a new chapter deck

1. Create a new directory under `vol1/` or `vol2/` following the existing `NN_slug` naming convention, matching the corresponding book chapter's number and slug.
2. Add a corresponding `Makefile` target following the existing `chNN`/`v2chNN` pattern, and add the chapter to the relevant portal page (`vol1.qmd` or `vol2.qmd`) so it's discoverable.
3. Update the `slides-build-pdfs.yml` and `slides-publish-live.yml` workflows' chapter loop if they enumerate chapters explicitly rather than discovering them automatically, check the current implementation before assuming either way.

### Working on the web portal only

1. No LaTeX toolchain needed. `quarto preview` from `slides/` is sufficient.
2. Portal content lives in the top-level `.qmd` files; don't confuse this work with slide-content work, they're validated and deployed by different CI jobs and different parts of the publish workflow.
