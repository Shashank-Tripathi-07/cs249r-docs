# Instructors: System Design

*This document exists for consistency with the other system design docs in this set, but the honest finding is short: "The Blueprint" has no system to design. Confirmed by searching the whole `instructors/` tree for any `.py`, `.js`, `.mjs`, or `.ts` file, zero results. This is pure Quarto content with one shell script, not a codebase.*

## What's actually there

`instructors/` is a standard Quarto website project (`_quarto.yml` declares `project: type: website`, `output-dir: _build`), containing: `index.qmd`, `getting-started.qmd`, `course-map.qmd`, `pedagogy.qmd`, `assessment.qmd` (a documented three-tier assessment model, a weighted 100-point scale, and the AI Olympics capstone specification), `ta-guide.qmd` (TA prep checklist and lab/grading support), `customization.qmd`, `faq.qmd`, three syllabus variants (`tinyml-syllabus.qmd`, `foundations-syllabus.qmd`, `scale-syllabus.qmd`), and a `404.qmd`. Assets are SVG diagrams (`assessment-tiers`, `four-pillar-loop`, `lab-abc-flow`, `semester-timeline`) and two SCSS files for light and dark mode.

The only non-content file in the entire directory is `instructors/check-links.sh`, a shell script for link validation. There is no build script, no data pipeline, no custom Quarto filter, and no pre- or post-render hook, unlike `site/`, which has exactly this kind of scripting (`build_stats.py`, `inject_stats.py`) for its own live data. A `quarto render` here does the entire job.

## Why this is worth documenting as a non-finding

Every other project in this docs set has a "what does this depend on and how do the pieces connect" answer worth several pages. Instructors doesn't, and that's a legitimate, useful thing for a contributor to know before going looking for a CLI, a data pipeline, or a build script that isn't there. If you're here to change instructor-facing content, you're editing Quarto Markdown and nothing else. See [`ci-workflows.md`](ci-workflows.md) for the standard validate/preview/publish triad that builds and deploys it.
