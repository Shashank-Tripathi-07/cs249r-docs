# The Blueprint (Instructor Site): Design

*This is the contributor-facing design document for "The Blueprint," the instructor site sub-project of `harvard-edge/cs249r_book` (the "Machine Learning Systems" repository), living at `instructors/` in that repo. It explains what the instructor site is, why it exists, how its pieces fit together, and what every technology in the stack is for. Read this before your first contribution; read [the implementation reference](implementation.md) when you're ready to touch code. Both documents describe the project as it actually exists on `dev` HEAD (commit `8fb87d81`, 2026-08-05). "Known issues" at the end lists a documented content-drift gap worth knowing about.*

## Problem

Adopting a textbook for a course is only the first step; an instructor still needs a syllabus, an assessment strategy, a plan for training teaching assistants, and a way to adapt sixteen weeks of material to whatever length and emphasis their own program actually has. Without a dedicated resource for that, every adopting instructor either builds all of it from scratch or informally asks the textbook's authors, neither of which scales.

The Blueprint is the MLSysBook's answer: a complete, public "course-in-a-box" for teaching the material as a real university course, two full semesters of week-by-week syllabi, a documented assessment model, pedagogy notes explaining the reasoning behind the labs, a teaching-assistant onboarding guide, and explicit guidance for compressing the material into a shorter course.

## Goals

- Two complete, week-by-week semester syllabi, one for Volume I (single-machine systems foundations) and one for Volume II (distributed systems at scale), each mapping specific weeks to specific chapters, labs, and assessments.
- A separate, shorter syllabus for a TinyML-focused course, for instructors who want to teach that track specifically rather than the full two-semester sequence.
- A documented, explicit assessment model (a stated weighting across correctness, systems thinking, and mastery dimensions) with rubrics and sample student work, so grading is consistent and its rationale is visible rather than ad hoc.
- Pedagogy documentation explaining the learning-science reasoning behind the course's distinctive teaching devices (for example, committing to a numeric prediction before running an experiment), so an instructor adopting the material understands why a lab is built the way it is, not just what to run.
- A teaching-assistant guide covering onboarding, lab facilitation, and grading calibration, so TA quality and consistency doesn't depend entirely on informal mentorship from the instructor.
- Explicit customization guidance for adapting the full sixteen-week sequence to shorter formats (a ten-week quarter, a seminar), rather than leaving that adaptation work entirely to the adopting instructor.
- A course-component map explaining how the four pillars of the ecosystem, reading the textbook, building a framework from scratch in TinyTorch, running the interactive labs, and deploying to real hardware kits, fit together week by week, since an instructor unfamiliar with the whole ecosystem needs a way to see how the pieces connect.
- A public FAQ addressing the most common practical questions instructors have when first evaluating whether to adopt the material.
- Free and open, published as a public website with no access gating, since the goal is to lower the barrier to adoption, not restrict who can see the material.

## Non-goals

- Not an authentication-gated instructor portal. Despite being organized for an instructor audience, there is no login, password, or access control anywhere in this site; it's fully public, and its own getting-started page explicitly tells students who land here that their instructor will direct them to the actual course materials elsewhere.
- Not the place TinyTorch's own instructor-facing grading documentation lives. That content (the nbgrader-based release-tier workflow, environment setup, per-module grading rubric) lives in TinyTorch's own `INSTRUCTOR.md`, linked from here rather than duplicated here.
- Not a build tool or automation surface of its own. Unlike the book, kits, or TinyTorch sub-projects, this is a plain Quarto content site with no accompanying CLI or VS Code extension; the entire "toolchain" is Quarto itself plus one small link-checking shell script.

## Technology stack

| Technology | What it is | How the instructor site uses it |
|---|---|---|
| Quarto | A publishing system built on Pandoc. | Builds the entire site: every page is a `.qmd` file, rendered to the static HTML published at `mlsysbook.ai/instructors/`. |
| A shared, ecosystem-wide Quarto configuration layer (`shared/config/`) | YAML and HTML fragments shared across several sibling Quarto projects in this monorepo. | Supplies the common navbar and footer content, so this site's navigation stays visually and structurally consistent with the book, kits, and slides sites without duplicating that configuration locally. |
| Bash, plus Python (inline) | Shell scripting, and Python run from within a shell script. | `check-links.sh` is a small, locally-runnable link-checking utility: a Python routine embedded in the script validates every internal markdown link and image reference actually resolves to a real file, and, if Lychee is installed, a second pass checks external URLs. |
| Lychee | A fast, external link-checking command-line tool. | Used both by the local `check-links.sh` script (optionally) and by the CI validation workflow, as a non-blocking check for broken external links across the site's content. |
| GitHub Actions | GitHub's built-in CI/CD platform. | Runs content validation (every image reference must resolve), a site-build sanity check, and non-blocking external link checking on every relevant change, and drives the separate dev-preview and production-publish deployment workflows. |

## Architecture

### Content organization

The site is organized into four sections, reflected directly in its sidebar navigation: "Start Here" (the landing page, a getting-started checklist, and a course-component map explaining how the ecosystem's four pillars, reading, building, exploring, and deploying, connect week by week), "Syllabi" (the Foundations, Scale, and TinyML syllabi, plus a customization guide), "Teaching and Assessment" (pedagogy notes, the assessment model, and the TA guide), and "Resources" (an FAQ, plus an external link out to TinyTorch's own instructor guide rather than a duplicate copy of it).

### The two main syllabi

The Foundations syllabus covers single-machine systems engineering (Volume I of the textbook) as a sixteen-week, thirty-two-lecture sequence, organized around what the course itself calls "the Iron Law" framework, and paired with TinyTorch module work at each stage. The Scale syllabus covers distributed systems and fleet-level engineering (Volume II) as its own sixteen-week sequence, covering topics like three-dimensional parallelism, collective communication, and fault tolerance, and does not pair with TinyTorch module work the way the Foundations syllabus does, since Volume II's TinyTorch modules (09 through 20) are treated as optional rather than core to that semester. A separate, shorter TinyML syllabus (ten to twelve weeks) exists for instructors specifically teaching the embedded/TinyML track, referencing the HarvardX/edX TinyML Professional Certificate materials and Arduino Nicla Vision hardware, and is designed to pair with the Foundations syllabus rather than stand fully alone.

### The customization guide

Not every adopting institution runs sixteen-week semesters. The customization guide documents concretely how to compress the full syllabus into shorter formats, for example a ten-week quarter, by mapping which weeks of content to combine or drop, rather than leaving an adopting instructor to guess which material is safe to cut.

### Pedagogy documentation

This section exists to make the reasoning behind the course's teaching devices visible, not just their mechanics. Its central example is what the course itself calls "the Prediction Lock": before running a lab instrument or experiment, a student is required to commit to a specific numeric prediction of the outcome. This is grounded in a specific learning-science mechanism (predictive processing and cognitive dissonance): a wrong prediction, once revealed, creates a stronger and more memorable learning moment than simply observing a result cold. Documenting this here means an instructor adapting or extending the labs understands which design choices are pedagogically load-bearing and shouldn't be casually simplified away.

### Assessment model

A three-tier weighting is documented explicitly: correctness, systems thinking, and mastery each contribute a defined share of a student's grade, rather than grading being collapsed into a single "did it work" pass/fail signal. This section also collects grading rubrics and sample student work at different quality levels, giving a new instructor or TA calibration reference points rather than having to invent grading standards from scratch.

### The TA guide

A structured onboarding path for teaching assistants: read the relevant textbook chapters, complete the corresponding TinyTorch modules themselves (so a TA has actually built what they're grading, not just read about it), run the labs themselves, read the pedagogy and assessment documentation, attend a grading-calibration session, and set up the nbgrader-based grading tooling TinyTorch provides. This exists specifically so TA quality doesn't depend entirely on informal, instructor-led mentorship, which doesn't scale past a single course offering.

### No access gating, by design

Despite being organized entirely around an instructor's needs, this is a fully public Quarto site with no authentication anywhere in it. The getting-started page is explicit that it's written for instructors and TAs, and that a student who lands here should be redirected by their own instructor to the actual course materials (the textbook, labs, and TinyTorch) instead. This is a content and navigation choice, not an access-control one: openness here is deliberate, lowering the barrier for a prospective instructor to evaluate the material before committing to adopt it.

## Known issues

- **The site's own README lists a file structure that's slightly out of date.** It doesn't mention `tinyml-syllabus.qmd`, even though that file exists, is wired into the site's navigation, and is one of the three syllabi described above. This is a small but real content-drift gap: anyone updating the README's file-structure documentation should add the missing entry.

## Contributing

Once you understand the shape of the project from this document, the [implementation reference](implementation.md) is where you'll actually work: it has the file map, the real Quarto navigation configuration, the link-checking tooling, local setup steps, and common contribution workflows for adding or editing a page. The README drift noted above is a small, self-contained first task if you want one.
