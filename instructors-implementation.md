# The Blueprint (Instructor Site): Implementation Reference

> **Status: as-built, contributor-facing.** The instructor site is a live, already-published Quarto site. This document is your map for reading and modifying the real source: file paths and real configuration pulled directly from the codebase at `dev` HEAD (`8fb87d81`, 2026-08-05). Read the [design doc](instructors-design.md) first for the "what and why"; this doc is the "where and how."

## Prerequisites

| To work on... | You need |
|---|---|
| Any page on this site | Quarto 1.4 or newer. Nothing else; there's no CLI, no build tooling, and no package manager involved. |
| The local link checker | Python 3 (required); Lychee (optional, for the external-link pass). |

## Repository layout

```
cs249r_book/
  instructors/
    _quarto.yml                # Site config: navbar, sidebar, theming, shared-config imports
    index.qmd                   # Landing page ("The AI Engineering Blueprint")
    getting-started.qmd
    course-map.qmd
    foundations-syllabus.qmd     # Volume I, 16-week syllabus
    scale-syllabus.qmd           # Volume II, 16-week syllabus
    tinyml-syllabus.qmd           # TinyML track, 10-12 week syllabus
    customization.qmd
    pedagogy.qmd
    assessment.qmd
    ta-guide.qmd
    faq.qmd
    404.qmd
    config/
      announcement.yml           # Site announcement banner content
    assets/                       # Images, styles (assets/styles/style.scss)
    check-links.sh                # Local internal + external link checker
  shared/
    config/
      navbar-common.yml           # Shared navbar, consumed via metadata-files
      footer-common.yml
      site-head.html
    release/
      release-pill.html
  .github/workflows/
    instructors-validate-dev.yml
    instructors-preview-dev.yml
    instructors-publish-live.yml
```

---

## 1. `_quarto.yml`: real configuration

```yaml
project:
  type: website
  output-dir: _build

website:
  title: "The Blueprint - Machine Learning Systems"
  description: "Course-in-a-box for teaching AI Engineering: syllabi, schedules, labs, and assessment."
  site-url: https://mlsysbook.ai/instructors/
  favicon: assets/images/logo.png
  page-navigation: true
  reader-mode: false
  back-to-top-navigation: true
  bread-crumbs: true

  navbar:
    # merged with shared/config/navbar-common.yml
    # local dropdown "The Blueprint":
    #   Blueprint Hub, Getting Started, ---,
    #   Foundations Syllabus, Scale Syllabus, TinyML Syllabus, ---,
    #   Pedagogy, Assessment & Grading, TA Guide, ---,
    #   Course Map, Customization, FAQ

  sidebar:
    style: floating
    search: true
    collapse-level: 2
    contents:
      - section: "Start Here"
        contents: [index.qmd, getting-started.qmd, course-map.qmd]
      - section: "Syllabi"
        contents: [foundations-syllabus.qmd, scale-syllabus.qmd, tinyml-syllabus.qmd, customization.qmd]
      - section: "Teaching & Assessment"
        contents: [pedagogy.qmd, assessment.qmd, ta-guide.qmd]
      - section: "Resources"
        contents:
          - faq.qmd
          - text: "TinyTorch Instructor Guide"
            href: "https://github.com/harvard-edge/cs249r_book/blob/dev/tinytorch/INSTRUCTOR.md"

metadata-files:
  - ../shared/config/navbar-common.yml
  - ../shared/config/footer-common.yml
  - config/announcement.yml

format:
  html:
    theme:
      light: [assets/styles/style.scss]
      dark: [assets/styles/style.scss, assets/styles/dark-mode.scss]
    respect-user-color-scheme: true
    toc: true
    toc-depth: 3
    code-copy: true
    anchor-sections: true
    include-in-header: ../shared/config/site-head.html
    include-after-body: ../shared/release/release-pill.html

execute:
  freeze: auto
```

The "TinyTorch Instructor Guide" sidebar entry is a real, external `href` pointing at a file in a different sub-project (`tinytorch/INSTRUCTOR.md`), not a local page, confirming that project-specific instructor content (TinyTorch's own grading workflow) intentionally isn't duplicated here.

---

## 2. Content pages: what's actually in each

| File | What it covers |
|---|---|
| `index.qmd` | Landing/hero page: "Everything an instructor needs to teach AI Systems, two semesters of syllabi, interactive labs, a build-from-scratch framework, hardware kits, and a complete assessment system." Also links to companion TinyML4D books. |
| `getting-started.qmd` | "From zero to teaching in one afternoon." An adoption checklist starting with choosing a track (Foundations Only, Scale Only, Full Sequence, or Quarter Version). Explicitly states this page is for instructors and TAs, and that students should be redirected elsewhere by their instructor. |
| `course-map.qmd` | Explains the "Four Pillars" (Read the textbook, Build in TinyTorch, Explore the interactive labs, Deploy to hardware kits) and how they connect week by week, plus an integration matrix. |
| `foundations-syllabus.qmd` | "AI Systems Foundations", Semester 1, single-machine engineering, 16 weeks / 32 lectures. Organized around "the Iron Law" framework, with lab and reading cadence and links to the corresponding Beamer slide decks. |
| `scale-syllabus.qmd` | "AI Engineering at Scale", Semester 2, distributed systems and fleets, 16 weeks. Covers three-dimensional parallelism, collective communication, fault tolerance, fleet orchestration. TinyTorch modules 09 through 20 are noted as optional here, unlike the Foundations syllabus. |
| `tinyml-syllabus.qmd` | "Tiny Machine Learning (TinyML)", 10 to 12 weeks, embedded ML deployment. References the HarvardX/edX TinyML Professional Certificate and Arduino Nicla Vision hardware; designed to pair with the Foundations syllabus. |
| `customization.qmd` | How to compress the 16-week syllabi into shorter formats, for example a mapped 10-week quarter version. |
| `pedagogy.qmd` | Learning-science rationale behind the labs; central example is "Predictive Processing (The Prediction Lock)", requiring a committed numeric prediction before running an instrument. |
| `assessment.qmd` | "Assessment and Grading." Opens with a three-tier model: Correctness 40%, Systems Thinking 30%, Mastery 30%. Includes rubrics, sample student work, and the AI Olympics capstone specification. |
| `ta-guide.qmd` | TA onboarding checklist: read chapters, complete the corresponding TinyTorch modules, run the labs, read the Pedagogy and Assessment pages, attend grading calibration, set up nbgrader. |
| `faq.qmd` | Common adoption questions: teaching Volume II without Volume I, GPU requirements, supported Python version, Google Colab use, prerequisites, and course-structure questions. |
| `404.qmd` | Custom not-found page. |

---

## 3. `config/announcement.yml`

Part of a documented, shared announcement-bar template pattern used across roughly nine Quarto sites in this monorepo (per its own header comment). Structure:

```yaml
website:
  announcement:
    icon: megaphone
    dismissable: true
    type: primary
    position: below-navbar
    content: >
      Instructor Hub, complete course materials, slides, exercises, and grading tools.
      [Vol I](...) [Vol II](...)
      Build with your students: [TinyTorch](...) [Hardware Kits](...) [Lecture Slides](...) [StaffML](...)
      [Subscribe to the newsletter](...)
```

---

## 4. `check-links.sh`

A local, manually-run link checker with two passes:

1. **Internal reference check** (always runs, requires only `python3`): an embedded Python routine globs every `.qmd` file, regex-matches Markdown links and image references, skips external URLs, anchors, and `mailto:` links, and reports any internal target that doesn't resolve to a real file on disk, with the offending file and line number.
2. **External URL check** (optional): if Lychee is installed, runs it against every `.qmd` file, accepting HTTP 200 and 403 responses and excluding localhost; if Lychee isn't installed, falls back to a Python routine that just extracts and counts unique external URLs across the site and suggests installing Lychee for a real check.

Usage: `cd instructors && ./check-links.sh`.

---

## 5. CI/CD implementation notes

### 5.1 `instructors-validate-dev.yml`

Triggers on `workflow_dispatch`, `workflow_call` (reused by the preview workflow and by the publish workflow's guard), and on pull requests and pushes to `dev` touching `instructors/**` or the workflow file itself. Jobs:

- `validate-content`: a Python check confirming every image reference in every `.qmd` file resolves to a real file, failing the job if not, the same logic `check-links.sh` runs locally, just enforced in CI.
- `build-site`: runs `quarto render` and confirms `_build/index.html` exists, reporting page count and build size.
- `check-links`: calls the shared, reusable `infra-link-check.yml` workflow (Lychee-based), non-blocking, since the site is small enough that its own comments note it's a candidate to become a blocking check later.
- `summary`: aggregates results; fails the overall job only if content validation or the site build failed, link-check failures are reported but don't block.

No deploy happens in this workflow; it's validation only.

### 5.2 `instructors-preview-dev.yml`

Triggers on push to `dev` touching `instructors/**`, or manually. Gated on `instructors-validate-dev.yml` passing. Runs `quarto render`, rewrites URLs for the dev-preview base path, updates the announcement banner with the current commit's info, validates `index.html` exists, then deploys via SSH by cloning a separate dev-preview repository, replacing that repository's instructor-site path with the freshly built output, and pushing. Deploy target: a GitHub Pages dev-preview repository, not the production `mlsysbook.ai` domain.

### 5.3 `instructors-publish-live.yml`

Manual only (`workflow_dispatch`, with a required `confirm: "PUBLISH"` input), or `workflow_call`. Jobs in order: `guard` (blocks unless the latest `instructors-validate-dev.yml` run on `dev` is green, via the shared `infra-publish-guard.yml` reusable workflow), `prepare` (version bump and tag, pattern `instructors-v*`, via the shared `_release-prepare.yml`), `build-and-deploy` (renders the site, validates `index.html` exists and that the page count meets a minimum threshold, emits a release manifest, then deploys via `peaceiris/actions-gh-pages@v4` to the `gh-pages` branch, `destination_dir: instructors`, with `keep_files: true`), and `create-tag` (creates and pushes the version tag and a draft GitHub Release, unless a site-only publish was requested). Deploy target: production, `mlsysbook.ai/instructors/`.

---

## 6. Local development workflow

1. `cd instructors && quarto preview`. No install step beyond having Quarto itself; there's no `package.json`, no Python environment, and no CLI to set up.
2. Edit the relevant `.qmd` file directly. The live preview reloads on save.
3. Before opening a PR, run `./check-links.sh` locally to catch broken internal references before CI does (and, if you have Lychee installed, to catch broken external links too).
4. If you're changing navigation structure, edit `_quarto.yml`'s `sidebar`/`navbar` sections directly; there's no generated or derived navigation config to worry about keeping in sync.

---

## 7. Common contribution workflows

### Editing an existing page

1. Edit the `.qmd` file directly, preview with `quarto preview`.
2. Run `./check-links.sh` before opening a PR.
3. If your edit changes what a reader should see in the sidebar (a new heading structure that should be reflected in navigation, for example), check whether `_quarto.yml`'s `sidebar` needs a matching update; Quarto doesn't automatically infer sidebar entries from page content in this project's configuration.

### Adding a new page

1. Create the `.qmd` file at the top level of `instructors/`, following the front-matter pattern of an existing page (title, subtitle) for consistency.
2. Add it to `_quarto.yml`'s `sidebar` (and `navbar`, if it should be reachable from the top-level dropdown) in the section that makes sense given the site's four-part structure (Start Here, Syllabi, Teaching and Assessment, Resources).
3. If it's a new syllabus or major content addition, also update `README.md`'s file-structure listing, this file has drifted out of date once already (see the design doc's "Known issues"), so keeping it current on every structural change avoids repeating that.

### Fixing the README file-structure drift

A small, self-contained first task: `README.md`'s documented file listing doesn't currently mention `tinyml-syllabus.qmd`. Add it to the listed structure, in the same style as the other syllabus files already documented there.
