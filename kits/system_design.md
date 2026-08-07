# Kits: System Design

*This document describes how the Hardware Kits labs actually build, from a `.qmd` lab file to a deployed HTML site and a downloadable PDF. It is written for a contributor adding a new hardware lab or fixing a PDF build failure. Read [`design.md`](design.md) first for the pedagogical framing; this document only covers mechanics. All facts below are sourced from `kits/`'s real Makefile, workflow files, and content structure.*

## 1. Problem this system solves

A hardware lab has to teach a real, purchasable device, Arduino Nicla Vision, Seeed's Grove Vision AI, a Raspberry Pi, with content specific enough to be actually followable (real product photos, real purchase links, real per-modality exercise tables) while still fitting into the same Quarto-and-PDF publishing pipeline the rest of this ecosystem uses. Kits solves this by being a self-contained sibling of `book/`, its own Quarto project with its own extensions and filters, rather than a subdirectory rendered by the book's own tooling, while still reusing exactly one piece of book infrastructure where it genuinely makes sense: PDF compression.

## 2. Dependencies and what each one actually does here

Kits has no `pyproject.toml` or `requirements.txt` of its own. It is pure Quarto content plus a Makefile, not a separately-dependent codebase. The two real external touchpoints:

| Dependency | Role |
|---|---|
| Quarto (via the shared `ghcr.io/harvard-edge/cs249r_book/quarto-linux` container) | Renders `.qmd` labs into HTML and, via a custom `titlepage-pdf` output format from the `mlsysbook-ext/titlepage` extension, into PDF. |
| `book/quarto/publish/compress_pdf.py` | The one script kits actually imports from the book project, run in CI to Ghostscript-compress the built PDF at `ebook` quality before it ships as a downloadable artifact. |

## 3. Component inventory

```
kits/contents/
  arduino/nicla_vision/
    image_classification/  object_detection/  kws/  motion_classification/  setup/
  seeed/
    grove_vision_ai_v2/    xiao_esp32s3/
  raspi/
    image_classification/  object_detection/  llm/  vlm/  setup/
  shared/
    dsp_spectral_features_block.qmd, kws_feature_eng.qmd  (cross-device building blocks)
```

Content is organized by hardware vendor, then by device, then by lab task, the opposite axis from the book's chapter-by-concept structure. Each lab folder carries its own `.qmd`, its own `.bib`, and its own `images/` subfolder with real product photography, not stock illustrations. `kits/contents/shared/` holds the handful of building blocks (signal-processing feature extraction, keyword-spotting feature engineering) genuinely reused across more than one device's labs, so a shared DSP concept isn't copy-pasted per vendor.

## 4. Build data flow

```
                    kits/contents/**/*.qmd
                             |
              symlink config/_quarto-html.yml -> _quarto.yml
                             |
              quarto render (HTML)                quarto render --to titlepage-pdf
                             |                              |
                    kits/_build/ (HTML site)      _build/pdf/Hardware-Kits.pdf
                                                             |
                                            compress_pdf.py --quality ebook
                                             (borrowed from book/quarto/publish/)
                                                             |
                                            Kits-PDF artifact (90-day retention)
                                                             |
              kits-publish-live.yml downloads it, injects a build stamp,
              copies it into _build/assets/downloads/, deploys the whole
              site to gh-pages under vars.DEV_KITS_PATH
```

The PDF build runs inside the same shared Linux container every other Quarto-and-LaTeX project in this repo uses, `ghcr.io/harvard-edge/cs249r_book/quarto-linux`, the same container documented in `book/system_design.md`'s Docker section. Kits doesn't have its own container definition, it borrows the book's.

## 5. Hardware content, what "hands-on" actually means here

There are no `.ino` files or raw firmware sketches anywhere under `kits/`. Labs are Edge Impulse and Python-workflow-centric: a student works through a `.qmd` with embedded Python code blocks for on-device or companion tooling, real per-modality exercise tables (vision, sound, IMU), and real purchase links to the actual hardware (`store.arduino.cc` and equivalents). This is a deliberate scope choice, the labs teach the ML systems workflow around a real device (data collection, model deployment, on-device inference), not embedded C firmware development.

## 6. Contributing

If you are adding a new device or lab, follow the existing `vendor/device/task/` directory shape exactly, and put anything genuinely reusable across devices (a signal-processing technique, a feature-engineering approach) into `kits/contents/shared/` rather than duplicating it per vendor. If you are touching the PDF pipeline, remember it depends on a script imported from `book/`, a change to `compress_pdf.py`'s interface needs to be checked against both projects, not just the book's own PDF build.
