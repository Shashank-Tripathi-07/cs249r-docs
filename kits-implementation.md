# Hardware Kits: Implementation Reference

> **Status: as-built, contributor-facing.** Hardware Kits is a live, already-published Quarto site and PDF. This document is your map for reading and modifying the real source: file paths and real build commands pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](kits-design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| Lab content (`.qmd` pages) | Quarto. Nothing else for HTML preview. |
| The PDF build | The above, plus a LaTeX distribution (the PDF format is a custom `titlepage-pdf` format via `lualatex`) and Ghostscript for compression. |
| The VS Code extension | Node.js, npm, and the VS Code extension tooling. |

## Repository layout

```
cs249r_book/
  kits/
    _quarto.yml                  # Symlink -> config/_quarto-html.yml (default) or config/_quarto-pdf.yml
    Makefile
    index.qmd
    config/
      _quarto-html.yml            # Website project config (navbar, full sidebar per platform)
      _quarto-pdf.yml               # Book project config (chapter list, titlepage-pdf format)
      announcement.yml
    _extensions/
      mlsysbook-ext/titlepage/       # Shared cover/title-page extension (PDF only)
    contents/
      getting-started.qmd, ide-setup.qmd, platforms.qmd
      arduino/nicla_vision/
        nicla_vision.qmd             # Hub page
        setup/, image_classification/, object_detection/, kws/, motion_classification/
      seeed/
        xiao_esp32s3/
          xiao_esp32s3.qmd
          setup/, image_classification/, object_detection/, kws/, motion_classification/
        grove_vision_ai_v2/
          grove_vision_ai_v2.qmd
          setup_and_no_code_apps/, image_classification/, object_detection/
      raspi/
        raspi.qmd
        setup/, image_classification/, object_detection/, llm/, vlm/
      shared/
        shared.qmd
        kws_feature_eng/, dsp_spectral_features_block/     # Shared DSP theory, .qmd + .bib
    tex/                            # LaTeX includes for the PDF build
    filters/
      inject_parts.lua               # PDF-only Lua filter
    vscode-ext/
      package.json                    # "kits-workbench", publisher mlsysbook
      src/extension.ts
      src/commands/buildCommands.ts
      src/providers/{buildTreeProvider,infoTreeProvider,platformTreeProvider,runHistoryProvider}.ts
      snippets/kits-qmd.code-snippets
  .github/workflows/
    kits-validate-dev.yml
    kits-build-pdfs.yml
    kits-preview-dev.yml
    kits-publish-live.yml
```

---

## 1. The config-symlink switch and the `Makefile`

`kits/_quarto.yml` is a live symlink, not a real file. The `Makefile` manages it:

```makefile
all: html   # default target

html:    # symlink config/_quarto-html.yml -> _quarto.yml, then quarto render
build: html

pdf:     # symlink config/_quarto-pdf.yml -> _quarto.yml,
         # quarto render --to titlepage-pdf,
         # then RESTORE the HTML symlink afterward

preview: # symlink the HTML config, then quarto preview

clean:   # remove _build and .quarto

help:    # usage text
```

Note that `make pdf` restores the HTML symlink as its last step, so the repository's default state (what `_quarto.yml` points at when nothing is actively building) is always the HTML config. If you're writing a new Make target or CI step that touches `_quarto.yml`, follow the same restore-afterward pattern rather than leaving the PDF config symlinked in place.

---

## 2. `config/_quarto-html.yml`: the website project

Key sections: a navbar; a full sidebar enumerating every platform's lab progression (confirmed to match the actual `contents/` directory structure: hub page, then setup, image classification, object detection, and, for the two full-coverage microcontroller platforms, keyword spotting and motion classification); HTML theme and format settings; shared header/footer includes; a release-manifest meta tag; and `metadata-files` pulling in `../shared/config/navbar-common.yml`, `../shared/config/footer-common.yml`, and the local `config/announcement.yml`, the same shared-configuration pattern used by several sibling Quarto projects in this monorepo.

---

## 3. `config/_quarto-pdf.yml`: the book project

A Quarto **book** project (distinct from the website project above), defining the full chapter list per platform and part, a custom `titlepage-pdf` format built on `lualatex` and the `scrbook` LaTeX document class, LaTeX include files pulled from `tex/`, and a single Lua filter, `filters/inject_parts.lua`.

---

## 4. `_extensions/mlsysbook-ext/titlepage/`

The one Quarto extension this project uses, providing the custom LaTeX cover and title page for the PDF build only (`format: titlepage-pdf` in `config/_quarto-pdf.yml`). Contains Lua filters (`coverpage-theme.lua`, `titlepage-theme.lua`) and LaTeX partials for the cover, title page, author affiliation block, and header/footer date styling, plus its own fonts and images.

---

## 5. Lab content: real structure

Every platform hub page (for example, `contents/arduino/nicla_vision/nicla_vision.qmd`) introduces the platform and links into its labs. Each lab is its own subdirectory with a `.qmd` and an `images/` folder. A representative code sample, embedded directly in a lab page rather than as a standalone file:

```cpp
// From contents/seeed/xiao_esp32s3/image_classification/image_classification.qmd
// (illustrative shape; real labs walk through Arduino IDE + Edge Impulse setup
// and TensorFlow Lite Micro inference code inline)
```

Two top-level, cross-platform reference pages exist outside any single platform directory: `contents/ide-setup.qmd` (walks through installing the Arduino IDE and adding the board-manager URLs needed for the XIAO and Nicla Vision boards) and `contents/platforms.qmd` (an overview comparing all four platforms).

### Shared DSP/feature-engineering theory

`contents/shared/kws_feature_eng/` and `contents/shared/dsp_spectral_features_block/` each pair a `.qmd` with a matching `.bib`, and are referenced from every platform's own keyword-spotting lab rather than duplicated. If you're updating audio feature-engineering theory, edit it once here; don't copy it into a platform-specific lab directory.

---

## 6. The VS Code extension ("Kits Workbench")

`vscode-ext/package.json`: name `kits-workbench`, publisher `mlsysbook`, description "Build, preview, and navigate hardware deployment labs for Arduino, Raspberry Pi, and Seeed platforms." Activates on `workspaceContains:kits/Makefile`.

Contributes an activity-bar container "Hardware Kits" with four tree views (Platforms and Labs, Build, Recent Runs, Info and Health), markdown snippets for `.qmd` authoring, and commands including build HTML, build PDF, preview, clean, open a specific lab, open a platform's hub page, refresh each tree view, rerun the last command, reveal the terminal, and a health check. A setting controls whether the terminal auto-reveals on a build failure.

`src/extension.ts`: `activate()` locates the project root by finding `kits/Makefile` (warning if it can't), wires up the four tree data providers, registers a generic `kits.runAction` command that shells out via a terminal helper, and sets up a file-system watcher on `kits/contents/**/*.qmd` so the platform/lab tree view refreshes automatically as lab content is added, removed, or renamed. It does not talk to any hardware directly; every command ultimately runs the same `make`/Quarto invocations documented in section 1.

---

## 7. CI/CD implementation notes

### 7.1 `kits-validate-dev.yml`

Triggers on pull requests and pushes to `dev` touching `kits/**`, or manually. Jobs: `validate-content` (a Python check confirming every markdown image reference across `contents/**/*.qmd` resolves to a real file), `build-site` (`quarto render`, confirms `_build/index.html` exists), `build-pdf` (calls the reusable `kits-build-pdfs.yml`), `check-links` (non-blocking, via the shared `infra-link-check.yml`), and a `summary` job that fails overall if content validation, the site build, or the PDF build failed.

### 7.2 `kits-build-pdfs.yml`

A reusable workflow (`workflow_dispatch` and `workflow_call`). Runs inside the monorepo's shared Quarto Linux container image, symlinks the PDF config, runs `quarto render --to titlepage-pdf`, compresses the resulting PDF with Ghostscript at ebook quality via a shared compression script, and uploads it as an artifact (`Kits-PDF`, 90-day retention). Produces an artifact only; no deploy happens here directly.

### 7.3 `kits-preview-dev.yml`

Triggers on push to `dev` touching `kits/**`, or manually. Calls `kits-validate-dev.yml` and `kits-build-pdfs.yml`, then, once both succeed, renders the site, downloads and injects the built PDF into the site's downloads area, rewrites URLs for the dev-preview base path, and deploys via SSH to a separate dev-preview GitHub Pages repository.

### 7.4 `kits-publish-live.yml`

Manual only, requiring a typed `PUBLISH` confirmation, or `workflow_call`. Gated on the latest `kits-validate-dev.yml` run on `dev` being green (via the shared `infra-publish-guard.yml`). Bumps the project's version and tag (pattern `kits-v*`), builds the PDF, renders the site, injects the PDF and a build stamp, emits a release manifest, and deploys via `peaceiris/actions-gh-pages@v4` to the `gh-pages` branch. Deploy target: `mlsysbook.ai/kits/`. Finishes by creating the version tag and a draft GitHub Release.

---

## 8. Local development workflow

1. `cd kits`.
2. `make preview` for live-reloading HTML preview while editing lab content. No LaTeX toolchain needed for this.
3. `make html` for a one-shot HTML build (matches what `kits-validate-dev.yml`'s `build-site` job does).
4. `make pdf` if you need to check the PDF output; requires a working LaTeX distribution with `lualatex`. Remember this temporarily swaps the `_quarto.yml` symlink; the `Makefile` restores it afterward, but if a build is interrupted partway through, check `_quarto.yml`'s symlink target before assuming you're back on the HTML config.
5. `make clean` if your build state gets confusing.
6. For VS Code extension work specifically: `cd vscode-ext && npm install`, then use VS Code's own extension-development launch configuration to test against a real `kits/` workspace.

---

## 9. Common contribution workflows

### Editing an existing lab

1. Edit the lab's `.qmd` file directly. `make preview` for live feedback.
2. If you're adding a new image, put it in that lab's own `images/` subdirectory and reference it with a relative path; CI's `validate-content` job will fail the build if the reference doesn't resolve.
3. If your edit touches keyword-spotting content specifically, check whether the change actually belongs in the shared DSP/feature-engineering pages (`contents/shared/`) instead of the platform-specific lab, if it's theory rather than platform-specific instructions, it probably belongs in the shared location so every platform's KWS lab benefits.

### Adding a new lab to an existing platform

1. Create a new subdirectory under that platform's directory (for example, a new lab type under `contents/arduino/nicla_vision/`), following the existing lab directories' shape (a `.qmd` plus an `images/` folder).
2. Add it to that platform's hub page and to the sidebar navigation in `config/_quarto-html.yml`, and to the chapter list in `config/_quarto-pdf.yml` if it should appear in the PDF book too.
3. If the new lab needs a VS Code "open lab" entry point, check `vscode-ext/src/providers/platformTreeProvider.ts` to confirm it picks up the new content automatically via the file-system watcher rather than needing a hardcoded update.

### Adding a new hardware platform

1. Create a new top-level directory under `contents/` (following the existing `vendor/board-name/` pattern), with a hub page and whatever lab subset makes sense for that hardware's capabilities, don't assume every platform needs the full five-lab set the two full-coverage microcontroller platforms have; the Grove Vision AI V2 and Raspberry Pi both have platform-appropriate, narrower or broader sets instead.
2. Add the platform to both `config/_quarto-html.yml`'s sidebar and `config/_quarto-pdf.yml`'s chapter list.
3. Add a brief entry to `contents/platforms.qmd`, the cross-platform overview page.
4. If the new platform needs its own IDE or toolchain setup instructions beyond what `contents/ide-setup.qmd` already documents, add a platform-specific setup lab rather than overloading the shared setup page.

### Working on the VS Code extension

1. `cd vscode-ext && npm install`, then launch the extension development host from VS Code against a workspace containing a real `kits/` checkout.
2. Every command should ultimately shell out to the same `make`/Quarto invocations a terminal user would run; don't reimplement build logic directly in the extension.
3. If you're adding a new tree view or command, follow the existing pattern of one provider file per view under `src/providers/`, and register new commands alongside the existing ones in `src/commands/buildCommands.ts` or `src/extension.ts`.
