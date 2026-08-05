# Glossary: The Technical Vocabulary Behind These Docs

*This is a plain-language reference for every recurring technical term used across the eleven project docs in this repository. It doesn't assume you already know the words. Each entry explains what the thing actually is, in everyday terms, then says specifically how it shows up in this ecosystem. Keep this open in a tab while you read any of the other docs; whenever you hit a term you don't recognize, it's probably here.*

## How to use this

Terms are grouped by domain (publishing tools, web/browser tech, cloud infrastructure, and so on), not strict alphabetical order, since most of these words make more sense next to their close relatives than sorted by letter. Use your browser's find (Ctrl+F / Cmd+F) if you're looking for one specific word.

---

## Publishing and documentation tools

**Quarto**
A tool that takes plain text files (mostly Markdown, with some extra syntax) and turns them into a finished website, PDF, or e-book. Nearly every project in this ecosystem (the book, kits, slides, the instructor site, the landing site) is "a Quarto project," meaning its content is written as `.qmd` files and Quarto is what builds them into the thing people actually see. Think of it like a much more powerful version of turning a Word document into a PDF, except it works from plain text and can output several formats from the same source.

**`.qmd` file**
A Quarto Markdown file. It's regular prose (like a `.md` file) with extra bits mixed in: special callout boxes, embedded code that can actually run and produce output, citations, and Quarto-specific formatting instructions. Every page of content in the Quarto-based projects is one of these.

**Pandoc**
The document-conversion engine Quarto is built on top of. You'll rarely touch it directly; it's the machinery underneath Quarto that actually understands how to turn Markdown into HTML, PDF, and so on.

**LaTeX**
A much older, much more manual typesetting system, mostly used for producing PDFs with precise control over layout, especially math and print-quality documents. Several projects use it directly (the research papers, the slide decks, the book's PDF output) rather than going through Quarto's simpler interface.

**Beamer**
A LaTeX add-on specifically for making presentation slides (as opposed to regular documents). The Lecture Slides project's 35 decks are all Beamer files.

**`pdflatex` and `xelatex`**
Two different programs that compile LaTeX source into a PDF. They're mostly interchangeable, but `xelatex` handles fonts more flexibly (useful for non-Latin scripts or unusual fonts) while `pdflatex` is older, more predictable, and doesn't depend on fonts being installed on the system in the same way. The slides project deliberately switched from `xelatex` to `pdflatex` after `xelatex` silently substituted a wrong font on a CI server with no error, producing slides that looked broken.

**TeX Live**
A complete, all-in-one bundle of LaTeX itself plus thousands of add-on packages. When a doc mentions "a full TeX Live install," it means the entire LaTeX ecosystem, not just the base compiler, since a real document usually needs dozens of extra packages beyond the core.

**EPUB**
A standard file format for e-books, the kind you'd load onto a Kindle or e-reader app. The book project builds this as one of its three output formats alongside HTML and PDF.

**`epubcheck`**
A validator that checks whether an EPUB file is actually well-formed and will open correctly in real e-readers, the same idea as a spell-checker, but for the file's internal structure rather than its prose.

**Markdown**
The lightweight text-formatting syntax you've probably already seen (`# heading`, `**bold**`, `[link](url)`). It's the base language every `.qmd` and `.md` file in this ecosystem is written in.

**Inkscape**
A vector-graphics editor, normally used interactively (like a simpler alternative to Adobe Illustrator), but used here from the command line to automatically convert SVG diagrams into PDF so LaTeX can include them, since LaTeX itself can't read SVG.

**Ghostscript**
A tool for processing PostScript and PDF files. In this ecosystem it's used specifically to compress already-built PDFs, shrinking file size for easier downloads without needing to rebuild the document from scratch.

---

## Web and browser technology

**WASM (WebAssembly)**
A format that lets code originally written in a language like C, Rust, or (via Pyodide) Python run inside a web browser at close to native speed. Normally a browser only understands JavaScript; WASM is how you get something else to run there too.

**Pyodide**
A full build of the standard Python interpreter (CPython), compiled to WebAssembly, so real Python code runs directly inside a browser tab with no server involved. This is what powers all 34 interactive labs, each one runs a genuine, unmodified copy of the `mlsysim` Python package on your own machine, inside the tab.

**`micropip`**
Pyodide's version of `pip` (Python's normal package installer). Since a browser can't reach out to the regular Python package index the same way your terminal can, `micropip.install(...)` is how a Pyodide-based page fetches and installs a Python package over plain HTTP instead.

**`sys.platform == "emscripten"`**
The specific check Python code uses to detect "am I currently running inside Pyodide/WASM, or on a normal computer?" Emscripten is the underlying compiler toolchain that produces WASM; Pyodide reports this platform name so code can branch its behavior (for example, "if in the browser, save to IndexedDB; otherwise, save to a normal file").

**IndexedDB**
A database built directly into every modern browser, for storing structured data locally on your machine, similar in spirit to a small SQL database, but browser-native and not synced anywhere by default. This is what the labs use to save your progress locally; it's also the exact subsystem involved in the `DesignLedger` bug discussed earlier in this conversation.

**Shadow DOM**
A browser feature that lets a piece of embedded content (like a widget) have its own completely isolated styles and internal structure, so it can never visually clash with or be broken by the page it's embedded in, and vice versa. Socratiq (the AI learning widget) is built entirely inside one, which is exactly why it can be dropped onto any page with a single script tag and never fight that page's own CSS.

**Service Worker**
A small script a website can register that runs in the background, separately from the page itself, most often used to cache content so a site can work offline or load faster on a repeat visit. StaffML uses one for this kind of offline API caching.

**Content Security Policy (CSP)**
A browser security setting a website can declare, restricting what kinds of scripts and resources are allowed to run on the page, a defense against certain attacks where malicious code gets injected into a site. You'll see this referenced when a doc explains why a particular kind of script (inline versus external) is or isn't allowed on a given page.

**XSS (cross-site scripting) and sanitization**
XSS is an attack where malicious script content gets inserted into a page and runs with the page's own trust and permissions. Sanitizing content (for example, via the `DOMPurify` library, used by Socratiq) means stripping out anything that could execute as code before displaying content that came from an untrusted source, like AI-generated text or user input.

**Vite**
A modern build tool for JavaScript and TypeScript projects: it bundles your source files into the small number of optimized files a browser actually loads, and gives you a fast local dev server with live-reload while you're editing. Socratiq uses it to produce its single-file, embeddable widget bundle.

**Bundler / bundling**
The general process a tool like Vite performs: taking many separate source files (and their dependencies) and combining them into one (or a few) files ready to ship to a browser, so the browser doesn't have to make dozens of separate requests.

**KaTeX**
A library for rendering math notation (the kind you'd write in LaTeX) directly on a web page, so equations show up properly formatted rather than as raw text like `$x^2$`.

**Mermaid**
A tool for turning plain-text descriptions into rendered diagrams (flowcharts, sequence diagrams) directly in a web page or Markdown file, without needing to draw them by hand in an image editor.

---

## Cloud infrastructure

**Cloudflare Worker**
A small piece of JavaScript or TypeScript that runs on Cloudflare's servers, distributed across many locations worldwide so it responds quickly no matter where a visitor is, rather than living in one single data center. StaffML uses three of these as its backend (one for question data, one for the AI interviewer, one for analytics); Socratiq's AI features also go through one.

**Cloudflare D1**
A managed database (SQLite, specifically, a lightweight but real SQL database) that Cloudflare hosts for you, paired with a Worker. It's where StaffML's question corpus actually lives on the server side.

**Cloudflare KV**
A simple key-value store (think: a big, fast dictionary of `key → value` pairs) also hosted by Cloudflare, used for things that need to be read and written quickly but don't need the structure of a full database, like rate-limit counters or cached results.

**CORS (Cross-Origin Resource Sharing)**
A browser security rule that, by default, blocks a web page from making requests to a different website's server than the one it was loaded from. A server has to explicitly say "yes, I'll accept requests from this other origin" via CORS headers, or the browser refuses to let the response through. Several docs discuss a project's CORS policy being "fail-closed," meaning if it's not sure whether to allow a request, it says no by default rather than yes.

**Rate limiting / token bucket**
A mechanism that caps how many requests a single user (or IP address) can make in a given time window, to prevent abuse or excessive cost. A "token bucket" is one common way to implement this: you get a refilling allowance of "tokens," and each request spends one.

**Circuit breaker**
A pattern for handling a failing remote service gracefully: after a service fails repeatedly, the calling code "opens the circuit" and stops even trying to call it for a while (instead of retrying and waiting out a timeout on every single request), then cautiously tries again after a cooldown period to see if it's recovered.

**OAuth-style login / OIDC**
A general pattern where you log into one service by authenticating through another (think "Sign in with Google"), rather than that service holding your password directly. OIDC (OpenID Connect) is a specific, standardized version of this. TinyTorch's community login and the PyPI publishing pipeline (which uses "OIDC Trusted Publishing" so no long-lived password/token needs to be stored) both work this way.

---

## Python packaging and tooling

**`pip`**
The standard tool for installing Python packages (`pip install <package>`). Almost every Python-based project in this ecosystem is installed this way, at least during development.

**Virtual environment (venv)**
An isolated, self-contained copy of Python and its installed packages, kept separate from your system-wide Python install, so different projects' dependencies don't conflict with each other. Nearly every setup guide in these docs starts with creating one.

**`uv`**
A newer, much faster alternative to `pip` (and several other Python tooling pieces combined) for installing packages and managing project dependencies. MLSys·im uses it as its primary supported tool.

**Wheel and sdist**
Two different packaged formats a Python project can be distributed in. A wheel is a pre-built, ready-to-install package; an sdist ("source distribution") is the raw source that gets built into a wheel at install time. When a doc says "the wheel contains only X, excluding tests and docs," it's talking about exactly what ends up inside that installable package.

**PyPI**
The Python Package Index, the official, public place `pip install <package>` fetches packages from by default. MLSys·im is published here (`pip install mlsysim` really works); TinyTorch, as one of the documented gaps, currently isn't, despite some of its own documentation claiming it is.

**Console script / entry point**
A configuration in a Python package that says "once this is installed, also make a command named `X` available directly in the terminal." This is how `pip install mlsysim` gives you a working `mlsysim` command, not just an importable library.

**`hatchling` and `setuptools`**
Two different tools ("build backends") that know how to package a Python project into an installable wheel or sdist. You mostly don't interact with them directly; a project just declares which one it uses in its configuration.

**Pydantic**
A Python library for defining the exact expected shape of your data (this field must be a number, this one must be one of these three specific strings, and so on) and automatically checking real data against that shape, rejecting anything that doesn't match. Several projects use it to validate configuration files and API data.

**Typer / `argparse` / Click**
Three different ways to build a command-line tool in Python (the kind you interact with by typing commands in a terminal). `argparse` is Python's built-in, most basic option; Typer and Click are more polished third-party alternatives. Different projects here made different choices, worth noting since one doc explicitly points out that TinyTorch's CLI is hand-built on plain `argparse` rather than a fancier framework, a deliberate choice, not an oversight.

---

## ML/data-science-specific tools

**Jupyter notebook**
An interactive, cell-by-cell document that mixes code, its output (including charts and images), and explanatory text, widely used for data science and teaching. Several projects here (TinyTorch's modules, the browser labs) revolve around notebooks in one form or another.

**`jupytext`**
A tool that keeps a Jupyter notebook and a plain text file in sync with each other, so you can edit either one and have the other stay up to date. This matters because notebooks (`.ipynb` files) are actually a chunky JSON format that's painful to review in a git diff; `jupytext` lets a project keep the reviewable, diffable plain-text version as the real source and generate the notebook from it.

**`nbdev`**
A toolkit (originally from the fastai project) that lets you write a real, installable code library as annotated notebook cells, then automatically "export" the tagged cells into an actual Python package. TinyTorch uses this: a student's notebook cells become the real, importable `tinytorch` package.

**`nbgrader`**
A tool built specifically for using Jupyter notebooks as graded assignments: an instructor writes one notebook with both the full solution and grading logic, and `nbgrader` can automatically strip the solutions out to produce the student-facing version, then automatically grade what a student submits.

**marimo**
A newer kind of Python notebook where the notebook itself is stored as a plain, ordinary `.py` file (not the chunky JSON format regular Jupyter notebooks use) and is "reactive," meaning if you change one cell, every other cell that depends on it automatically re-runs. The browser-based labs are all written as marimo notebooks.

**LinkML**
A schema language, a way of formally describing "here's exactly what shape a piece of data must have," written once in YAML, that can then generate matching code in several different target languages (Python validation classes, database table definitions, TypeScript types) automatically. StaffML's question format is defined this way, so its Python backend, its database, and its TypeScript frontend can never quietly disagree about what a "question" looks like.

---

## Core ML systems concepts

**FLOPs (floating-point operations)**
A basic unit for measuring how much raw computation a task requires, how many individual arithmetic operations (like multiplications and additions on decimal numbers) it takes. "This model requires 141 billion FLOPs to run once" is a way of quantifying its computational cost independent of what hardware it eventually runs on.

**Roofline model**
A simple, visual way of predicting whether a piece of computation will be limited by how fast a chip can do math ("compute-bound") or by how fast it can move data in and out of memory ("memory-bound"), by comparing the task's own compute-to-data ratio against the hardware's own peak compute-and-bandwidth numbers. MLSys·im's entire modeling engine is built around this idea, and it's also the conceptual basis for the "Iron Law of ML performance" the design grammar and the book both reference.

**Arithmetic intensity**
The specific ratio the roofline model compares: how many computations you do per byte of data moved. A high number means you're doing a lot of math for each piece of data fetched (good, likely compute-bound); a low number means you're mostly just shuffling data around without doing much with it (likely memory-bound).

**Memory bandwidth**
How fast data can move between a chip's memory and its compute units, typically measured in gigabytes or terabytes per second. This, not raw compute power, is often the actual bottleneck in real ML workloads.

**MFU / HFU (Model / Hardware FLOPs Utilization)**
A percentage measuring how much of a chip's theoretical peak computing power a real run actually achieved. A run at 100% MFU is using every bit of the hardware's math capability; in practice, real workloads are almost always well below that, and MFU is a standard way of quantifying exactly how far below.

**Quantization**
Reducing the numeric precision a model's numbers are stored and computed in (for example, going from 32-bit decimal numbers down to 8-bit ones), trading a small amount of accuracy for a large reduction in memory use and often faster computation.

**Sharding**
Splitting a large piece of data or a large model across multiple machines or devices, since it's too big to fit on just one. Several distributed-training techniques mentioned across these docs (ZeRO, FSDP) are specific strategies for doing this well.

**Tiling and fusion**
Two related optimization techniques: tiling breaks a big computation into small chunks that fit into a chip's fastest, smallest memory, processing one chunk at a time instead of the whole thing at once; fusion combines several separate computational steps into one, avoiding the cost of writing intermediate results out to slower memory between steps. FlashAttention (a well-known, real technique referenced in several docs) is built from exactly these two ideas.

**KV cache**
In a language model generating text one word at a time, this is a store of intermediate values ("keys" and "values") computed for earlier words, kept around so they don't have to be recomputed for every new word. It trades memory usage for a large speedup, but that memory cost is itself a real, often binding constraint on how much text a model can generate at once.

**PUE and WUE (Power/Water Usage Effectiveness)**
Standard metrics for how efficient a data center is: PUE is the ratio of total facility power to the power actually delivered to the computers themselves (a PUE of 1.1 means only 10% extra overhead for cooling and so on); WUE is the equivalent measure for water consumption, both used when estimating a workload's real-world environmental footprint.

**TCO (Total Cost of Ownership)**
The full real cost of running something over its lifetime, not just the sticker price, hardware purchase cost plus power, cooling, maintenance, and so on, combined.

---

## Testing, CI/CD, and release concepts

**CI/CD (Continuous Integration / Continuous Deployment)**
The general practice of automatically running checks (tests, validations) on every proposed change before it's allowed to merge, and automatically deploying approved changes rather than doing either step by hand. Nearly every project in this ecosystem uses GitHub Actions (see below) to do this.

**GitHub Actions / workflow**
GitHub's built-in system for automating tasks (running tests, building a site, deploying it) whenever something happens in the repository, like a new commit, a pull request, or a scheduled time. A "workflow" is one automated recipe, defined in a `.yml` file, describing what triggers it and what steps to run.

**`workflow_dispatch` vs. `workflow_call`**
Two different ways a workflow can be triggered. `workflow_dispatch` means a person can manually click a button to run it (used for anything requiring a deliberate, confirmed action, like publishing to production). `workflow_call` means another workflow can invoke it as a reusable building block, so common logic (like "build the site") doesn't have to be copy-pasted into every workflow that needs it.

**Matrix build**
Running the same set of steps multiple times automatically, once for each combination of some set of variables, for example, once per operating system times once per output format, without having to write that out by hand for every combination.

**Artifact (in CI)**
A file or set of files produced by one step of an automated workflow (like a compiled PDF or a built website) and saved so it can be downloaded, inspected, or handed off to a later step or a different workflow.

**`gh-pages` branch**
A specific, conventional branch name GitHub Pages (GitHub's free static website hosting) looks at to know what to actually publish. Deploying a site in this ecosystem usually means building it, then pushing the built output onto this branch.

**Pre-commit hook**
A check that runs automatically right before a `git commit` actually completes, so problems get caught on your own machine before you even push, rather than only being caught later in CI. The `pre-commit` tool is what most of these projects use to manage and run their set of these checks.

**Playwright**
A tool for automatically controlling a real web browser from code, used for testing: it can open a page, click things, and check what actually rendered, catching problems (like a page that looks broken only in a real browser, not in any simpler check) that a purely code-level test would miss. The labs' WASM smoke test and the book's math-rendering audit both use this.

**Headless browser**
A real web browser running without any visible window, used specifically for automation and testing (like via Playwright), since there's no need for a human to actually watch it.

**Semantic versioning**
A common convention for version numbers in the form `MAJOR.MINOR.PATCH` (like `0.1.2`), where each position means something specific: patch for small fixes, minor for new features that don't break anything existing, major for changes that do break existing usage.

**Provenance and hashing (SHA-256)**
"Provenance" here means a documented, traceable record of exactly where a piece of data or a result came from. SHA-256 is a specific, one-way mathematical function that turns any file into a short, unique-looking fingerprint; if the file changes even slightly, the fingerprint changes completely. Comparing fingerprints is a standard way to prove a file hasn't been tampered with, without needing to compare the whole file byte by byte.

---

## Git and repository concepts

**Monorepo**
A single repository (one git history, one place to clone) that contains many otherwise-independent projects, rather than each project living in its own separate repository. `harvard-edge/cs249r_book` is a monorepo; that's exactly why eleven very different-sounding projects (a textbook, a benchmark suite, an AI widget) all live under the same repository.

**Symlink (symbolic link)**
A special kind of file that doesn't contain real content itself, it just points at another file or directory elsewhere. Opening it transparently shows you the target's content instead. The Hardware Kits project uses one to switch which Quarto configuration is "active" without duplicating files; the book's own `binder` CLI naming collision (mentioned in its docs) also involves an unrelated directory that's really a redirect for a different project entirely.

**`git mv`**
The git-aware way to rename or move a file, as opposed to just using your operating system's move command. It tells git directly "this is the same file, just relocated," which keeps the file's history intact and shows up in GitHub as a clean rename rather than a deletion plus a brand-new file.

---

## Names and concepts specific to this ecosystem

These aren't general technical vocabulary, they're proper nouns and specific ideas invented within this project, worth defining since they show up constantly but won't be in any outside glossary.

**Binder** (two unrelated meanings, a genuine trap)
Inside `book/`, "Binder" is the book project's own command-line tool (`book/binder`) for building, checking, fixing, and formatting the textbook. Completely separately, a directory just named `binder/` at the very root of the repository holds unrelated configuration for mybinder.org (a third-party service for launching Jupyter notebooks in the cloud), and that one actually belongs to the TinyTorch project. Same word, two unconnected things.

**The Iron Law (of ML performance)**
A shorthand phrase, used by both MLSys·im and the Design Grammar project, for the core roofline-model idea: how long something takes is governed by the FLOPs required, divided by how much of the hardware's peak compute you actually manage to use.

**Design Grammar**
This project's own name for its catalog-and-rules system: a fixed vocabulary of ML systems building blocks ("primitives"), a small set of ways to combine them ("composition operators"), and a set of named transformations ("rewrite rules," like tiling or sharding) that solve specific constraints. The pitch is that named real-world techniques like FlashAttention aren't really new inventions, they're specific, derivable combinations of this smaller underlying vocabulary.

**Scenario (in MLSys·im)**
A specific, named, ready-to-run combination of a workload plus a hardware target plus some constraints (like a latency limit), that you can evaluate with one function call rather than assembling the pieces yourself every time.

**The Four Pillars**
A framing used specifically by the instructor site to describe how four otherwise-separate projects fit together in an actual course: **Read** the textbook, **Build** a framework from scratch in TinyTorch, **Explore** interactive labs, and **Deploy** to real hardware kits.
