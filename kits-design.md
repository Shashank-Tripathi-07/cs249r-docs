# Hardware Kits: Design

*This is the contributor-facing design document for Hardware Kits, a sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `kits/` in that repo. It explains what Hardware Kits is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](kits-implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05).*

## Problem

Reading about embedded ML deployment and actually deploying a model to a real, resource-constrained device are very different experiences. The textbook can explain why a quantized model matters, but a student who has never actually flashed a microcontroller, wired up a camera module, or watched a keyword-spotting model run on 250KB of RAM hasn't really encountered the constraints the book describes, only read about them. Building that hands-on bridge from scratch, for four different hardware platforms, each with its own toolchain, is a large amount of setup work most instructors and students won't do unprompted.

Hardware Kits exists to be that bridge: a set of concrete, hands-on labs, deploying real models (image classification, object detection, keyword spotting, motion classification) to real, named, purchasable hardware, with step-by-step instructions covering everything from IDE setup through a working deployment, for each of four different device platforms spanning very different resource envelopes.

## Goals

- Hands-on labs for four real hardware platforms spanning meaningfully different resource envelopes: an Arduino Nicla Vision (an STM32H7-based AI camera board), a Seeed XIAO ESP32S3 (a tiny ESP32-S3 with camera and WiFi), a Seeed Grove Vision AI V2 (a no-code AI vision module), and a Raspberry Pi (a full Linux single-board computer).
- Consistent lab coverage across the microcontroller-class platforms (Arduino Nicla Vision and Seeed XIAO ESP32S3): image classification, object detection, keyword spotting, and motion classification, so a student or instructor can meaningfully compare the same task across two different microcontroller platforms.
- Broader coverage on the Raspberry Pi specifically, since its Linux environment supports workloads the microcontroller platforms can't: image classification, object detection, and, uniquely to this platform, large language model and vision-language model labs.
- Shared theory content (digital signal processing and feature engineering for audio) factored out once and reused across every platform's keyword-spotting labs, rather than duplicated per platform.
- A published, browsable Quarto site (`mlsysbook.ai/kits`) plus a downloadable PDF, so the material is usable both as a live reference while working at a bench and as an offline document.
- An editor-integration layer (a VS Code extension) that wraps the underlying build commands in a discoverable UI, lowering the barrier for a contributor who doesn't want to memorize `make` targets and Quarto config-symlink switching.
- Free and open, licensed the same as the rest of the MLSysBook ecosystem, deployable and forkable by anyone.

## Non-goals

- Not a firmware or embedded-code repository. This project ships instructional Quarto documentation containing embedded code samples (Arduino sketches, Python scripts), not a buildable, versioned firmware codebase of its own; the actual "build" this project's tooling performs is a documentation build, not a device flash.
- Not a hardware store or a hardware compatibility database beyond the four platforms it actively documents; it's scoped to hands-on labs for specific, named boards, not a general embedded-ML hardware reference.
- Not a replacement for the vendor toolchains (Arduino IDE, Edge Impulse, and similar) it teaches; it's instructional content built around those existing tools, not an alternative to them.

## Technology stack

| Technology | What it is | How Hardware Kits uses it |
|---|---|---|
| Quarto | A publishing system built on Pandoc. | Builds the entire project: every lab is a `.qmd` page, rendered to both a public HTML site and a downloadable PDF from the same source content. |
| A shared Quarto extension for cover and title pages | A Quarto extension local to this monorepo's Quarto projects. | Provides the custom LaTeX-based cover and title page used only in the PDF build. |
| Arduino IDE, Arduino CLI, Edge Impulse, and TensorFlow Lite Micro | Real, external embedded-ML tools and toolchains. | Referenced and walked through as embedded code samples and setup instructions inside the lab content itself; this project doesn't reimplement any of them, it teaches a reader how to use them on specific hardware. |
| Ghostscript | A PostScript and PDF interpreter. | Compresses the built PDF for smaller downloads, as part of the CI build pipeline. |
| GNU Make | A build-automation tool. | Wraps the Quarto config-symlink switching this project's HTML and PDF builds need (see "Architecture") into simple `make html`/`make pdf`/`make preview` targets. |
| TypeScript and the VS Code Extension API | A typed superset of JavaScript, and the API for building Visual Studio Code extensions. | Powers "Kits Workbench," a sidebar extension wrapping the underlying `make`/Quarto commands in a discoverable tree-view UI. |
| GitHub Actions | GitHub's built-in CI/CD platform. | Runs content validation (every image reference must resolve to a real file), builds the site and PDF, and drives dev-preview and production-publish deployments. |

## Architecture

### One content tree, two build outputs

Lab content lives once, under `kits/contents/`, organized by platform: `arduino/nicla_vision/`, `seeed/xiao_esp32s3/`, `seeed/grove_vision_ai_v2/`, and `raspi/`, plus a `shared/` directory for content genuinely reused across platforms. Two separate Quarto configuration files, one for HTML output and one for PDF output, both point at this same content tree; a small mechanism (see below) switches which one is actually active at build time, so there's exactly one source of truth for lab content regardless of which output format you're building.

### The config-symlink switch

Quarto expects a single, canonically-named configuration file (`_quarto.yml`) at the project root; this project needs two different configurations (one for the website build, one for the PDF book build) from the same content. The resolution is a symlink: `_quarto.yml` is not a real file but a symlink that gets pointed at either `config/_quarto-html.yml` or `config/_quarto-pdf.yml` depending on which build you're running. The `Makefile` (see the implementation reference) automates this switch so a contributor running `make html` or `make pdf` never needs to manage the symlink by hand, and always restores the HTML config as the default afterward.

### Lab structure per platform

Each platform directory follows a consistent shape: a hub page introducing the platform, then one subdirectory per lab. On the two microcontroller platforms with full coverage (Arduino Nicla Vision, Seeed XIAO ESP32S3), that's setup, image classification, object detection, keyword spotting, and motion classification, in that order, mirroring a natural teaching progression from environment setup through increasingly involved deployment tasks. The Grove Vision AI V2 (a no-code module) has a narrower set focused on its own no-code workflow, setup, image classification, and object detection. The Raspberry Pi, being a full Linux machine rather than a microcontroller, has its own broader set: setup, image classification, object detection, and, uniquely, large language model and vision-language model labs that wouldn't be possible on the more constrained platforms.

### Shared theory content

Digital signal processing and audio feature-engineering theory, the conceptual groundwork every platform's keyword-spotting lab needs, is written once under `kits/contents/shared/` and referenced from each platform's own keyword-spotting lab, rather than being duplicated and risking drifting out of sync across platforms.

### Instructional content, not firmware

Every device-facing code sample (roughly 56 C++ blocks and 154 Python blocks across the current lab content, alongside shell commands for toolchain setup) lives as a fenced code block embedded directly inside the `.qmd` lab pages. There is no standalone `.ino`, `.py`, or firmware source tree anywhere in this project; the code a reader sees on the page is meant to be copied out and used with the vendor's own toolchain (the Arduino IDE, for example), not compiled or built by this project's own tooling. This project's build system only ever builds documentation, never firmware.

### The VS Code extension

"Kits Workbench" is a thin, deliberate wrapper: it doesn't talk to any hardware and doesn't reimplement any build logic. It shells out to the exact same `make`/Quarto commands a contributor would run from a terminal, surfaced instead as a sidebar tree view (platforms and labs, build actions, recent run history, and an info/health panel) with commands for building HTML, building PDF, previewing, and health-checking the environment. It activates automatically when it detects the project's `Makefile` in the open workspace, and watches the lab content tree to keep its own navigation view in sync as files are added or removed.

### CI and deployment

Content validation (confirming every image reference in every lab page resolves to a real file), a site-build sanity check, and a non-blocking external link check all run on every relevant pull request and push. A separate reusable workflow builds and compresses the PDF as an artifact, consumed both by the dev-preview and production-publish deployment workflows, so the PDF and the HTML site are always built from the same validated content and released together rather than drifting independently.

## Known issues

No specific, confirmed gaps were surfaced during research beyond what's inherent to the project's current scope (four platforms, a fixed lab set per platform). If you find a real one while contributing, this is a good section to add it to.

## Project history

No specific historical incidents or past-bug narratives were surfaced during research for this project, unlike several of its sibling sub-projects. This section will be a good place to record one if a real incident occurs.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](kits-implementation.md) is where you'll actually work: it has the file map, the real `Makefile` targets and config-switching mechanism, the VS Code extension's actual command set, local setup steps, and common contribution workflows for adding a new lab or a new platform.
