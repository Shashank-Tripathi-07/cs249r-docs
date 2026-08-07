# Slides: System Design

*This document describes how the 35 Beamer lecture decks actually build, from `.tex` source to a compiled PDF and a presenter-ready PPTX. It is written for a contributor adding a chapter deck or fixing a build failure. All facts below are sourced from `slides/Makefile`, `slides/assets/beamerthememlsys.sty`, and `slides/scripts/pdf2pptx.py`.*

## 1. Problem this system solves

A lecture deck has to compile reliably in CI, on a plain `ubuntu-latest` runner with no custom container, while carrying real branding (Harvard and MLSysBook logos, a shared color scheme) consistently across 35 independently-authored files, and it has to produce two genuinely different artifacts from the same source: a print-quality PDF and an editable-feeling presenter PPTX. The system solves this with one shared style file every deck inherits from, and a deliberately simple, boring build engine chosen specifically because the fancier alternative silently broke.

## 2. Dependencies and what each one actually does here

| Dependency | Role |
|---|---|
| `pdflatex` | The actual compile engine. Worth stating explicitly because it's not the obvious choice: the Makefile documents switching away from `xelatex` plus `fontspec` after it silently fell back to Liberation Sans on Ubuntu CI, producing blank-text PDFs on 2026-05-12. `pdflatex` with `helvet`/`courier` fonts is the fix, and it's what CI actually runs. |
| Metropolis (Beamer theme) | The base theme every deck builds on, loaded inside the shared style file via `\usetheme[progressbar=none, numbering=fraction, block=fill, sectionpage=none]{metropolis}`. |
| Inkscape | Converts chapter SVG assets to PDF (`inkscape --export-type=pdf`) before `pdflatex` can include them. |
| `poppler` (`pdftoppm`) plus `python-pptx` | The PPTX conversion path: rasterizes each compiled PDF page at 300 DPI, then places it full-bleed on a 16:9 slide. |

## 3. Component inventory

```
slides/
  assets/
    beamerthememlsys.sty   <- every deck's shared theme, branding, colors
    img/                    <- logo-mlsysbook.png, logo-harvard.png, etc.
  vol1/00_course_overview/ .. 16_conclusion/    (17 decks)
  vol2/00_course_overview/ .. 17_conclusion/    (18 decks)
    each with its own <chapter>.tex and images/
  scripts/pdf2pptx.py
  Makefile
```

Every deck is `\documentclass[aspectratio=169,12pt]{beamer}` and pulls in `\usepackage{../../assets/beamerthememlsys}`. Chapter folder numbers and titles in both volumes are named to mirror the book's own chapter breakdown, and each deck's header encodes that identity through a `\mlsyssetup{volume=..., chapter=..., chaptertitle=...}` macro used for title and footer branding. This coupling is deliberate and structural, not code-level: no deck was found to programmatically include or cross-reference the book's actual `.qmd` content, decks re-illustrate concepts (often reusing the same source imagery, like device photos) as self-contained LaTeX rather than importing anything.

## 4. Build data flow

```
1. make svgs
   find every *.svg under vol1/vol2 newer than its PDF
   inkscape --export-type=pdf   ->   matching .pdf next to each .svg

2. make build-<chapter>
   pdflatex -interaction=nonstopmode -halt-on-error   (run twice, standard
   LaTeX practice for cross-references and the table of contents to resolve)
   scans the .log for Overfull/Underfull box warnings
   copies the result into _build/vol{1,2}/

3. make pptx-vol1 / pptx-vol2
   depends on the build step above, then for each PDF:
   pdftoppm (poppler, 300 DPI)  ->  one raster image per slide
   python-pptx                  ->  places each image full-bleed on a
                                     16:9 slide

   Output is image-based, not editable text, by design (documented
   directly in pdf2pptx.py's own docstring), this is a presenter-mode
   artifact, not a source-of-truth alternative to the LaTeX.
```

CI (`slides-build-pdfs.yml`, `slides-validate-dev.yml`) runs this on plain `ubuntu-latest`, no custom container, unlike `kits` and `book`, which both depend on the shared Quarto Docker image. Slides don't need Quarto at all for the actual deck compile, only the portal wrapper (`slides-preview-dev.yml`/`slides-publish-live.yml`'s HTML index) touches Quarto.

## 5. Contributing

If you are adding a new chapter deck, copy an existing `.tex` file's header (`\mlsyssetup{...}` block and the `\usepackage{../../assets/beamerthememlsys}` line) exactly rather than reconstructing it, and match the existing chapter-numbering convention so the deck's identity macro stays consistent with the book's own chapter structure. If a deck's PDF is coming out with system-default fonts instead of the intended ones, check the compile engine first, this exact symptom is the documented `xelatex`-to-`pdflatex` regression, not a new bug.
