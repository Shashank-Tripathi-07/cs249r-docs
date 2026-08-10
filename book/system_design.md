# Book: System Design

*This document describes how the Binder CLI actually works: its command dispatch, its content-validation check suite, and the real build chain from a `.qmd` chapter to HTML, PDF, and EPUB output. It is written for a contributor changing a validation check or the build pipeline itself. All facts below are sourced from `book/cli/main.py`, `book/cli/commands/validate.py`, `book/cli/commands/build.py`, and `.pre-commit-config.yaml`.*

## 1. Problem this system solves

A textbook this large needs one enforcement mechanism for its content rules, citation formatting, cross-reference integrity, prose style, figure structure, that a contributor sees identically whether they're running a pre-commit hook locally, using the VS Code extension, or watching CI. It also needs one build entry point that hides Quarto, pandoc, and the LaTeX toolchain behind a stable interface, so that switching a rendering detail doesn't mean updating three different invocation sites. Binder is both of those things: one CLI, one validation registry, one build command, with pre-commit, the VS Code extension, and CI all calling into it rather than reimplementing any of it themselves.

## 2. Dependencies and what each one actually does here

| Dependency | Role |
|---|---|
| `rich` | The entire CLI's console output, every banner, table, and panel across every command. |
| `pyyaml` | Parses `_quarto.yml` and its per-format variants (`_quarto-pdf-vol1.yml`, etc.) via `ConfigManager`. |
| `pypandoc`, `pybtex` | Bibliography and citation processing, used by the bib-check scopes in the validation suite. |
| `jsonschema` | Schema validation for structured content (grammar-style JSON checks). |
| `Pillow` | Backs `./binder fix images compress`. |
| `titlecase` | Powers the MIT Press headline-case and caption-style checks specifically. |
| `click` | Declared as a dependency, but worth flagging: the actual CLI hand-rolls its own dispatch in `cli/main.py` rather than using Click's decorator-based command tree, a real discrepancy between the declared dependency and what's observed driving the command surface. |

The Docker container's own dependency set, `book/tools/dependencies/requirements.txt`, is a materially different, larger list than the root `pyproject.toml`, not a subset. It adds `matplotlib`, `nltk`, `groq`, `yamllint`, `ghostscript`, `pre-commit`, `bibtexparser`, `pint`, `lxml`, `pydantic`, `scipy`, `seaborn`, and critically **`epubcheck`**, explicitly commented as backing `./binder check epub --scope epubcheck` (wraps the W3C epubcheck jar, falls back to a PATH binary, and surfaces a clear install hint if neither is found). One dated comment on the `attrs` pin documents a real regression: on Python 3.14, `attrs` stopped auto-installing transitively via `nbformat -> jsonschema`, breaking Jupyter-kernel-backed chapter rendering with a "Broken pipe" error, worth knowing if that pin is ever loosened.

## 3. Component inventory

```
book/binder  (entry point script)
      |
book/cli/main.py: MLSysBookCLI
      |
  one command object per family, each its own file under cli/commands/
      |
  BuildCommand    PreviewCommand   ValidateCommand   FormatCommand
  DoctorCommand   CleanCommand     BibCommand         RenderCommand
  AuditCommand    ReleaseCommand   NewsletterCommand  HeadingsCommand
  LayoutCommand   MaintenanceCommand  DebugCommand    ResetCommand
```

Command surface, from the CLI's own help text: `build [fmt] [chapter]`, `preview`, `check <group>`, `fix <topic> <action>`, `format <target>`, `audit chapter-pdf|html`, `release [--vol1|--vol2]`.

The validation suite lives in `ValidateCommand` (`cli/commands/validate.py`), and its own docstring states the design contract directly: every `book-*` pre-commit hook dispatches through `./book/binder check <group>` so there is one entry point, one error format, and one place to add a new check. Checks are registered in a `GROUPS` table mapping a group name (`refs`, `labels`, `headers`, `bib`, `footnotes`, `figures`, `prose`) to a list of `Scope` entries, each naming a `_run_<scope>` method: `Scope("cross-refs", "_run_refs")`, `Scope("citations", "_run_citations")`, `Scope("hygiene", "_run_bib_hygiene")`, and a large block of MIT-Press-specific prose scopes (contractions, above/below phrasing, percent-sign spacing). EPUB-specific checks live in a separate module, `cli/commands/_epub_checks.py`, PDF cross-reference verification in `_pdf_checks.py`.

## 4. Data flow: a `.qmd` chapter to HTML, PDF, and EPUB

```
1. BuildCommand maps the requested format to a Quarto --to target:
   html -> "html", pdf -> "titlepage-pdf", epub -> "epub"
                    |
2. ConfigManager.setup_symlink(format_type)
   swaps _quarto.yml to point at the format-specific config file
   (_quarto-pdf-vol1.yml, etc.), printed as
   "Linked _quarto.yml -> <config_name>"
                    |
3. quarto render --to=<target>
   run as a real subprocess; Binder drives Quarto, it never calls
   pandoc or the LaTeX toolchain directly, Quarto owns that itself
                    |
4. Post-render, format-specific verification:
   EPUB -> run_epubcheck_on() shells out to the epubcheck jar,
           diffs results against a committed epubcheck-baseline.json
   PDF  -> verify_volume_pdf() runs a post-build cross-reference scan
```

Step 3's Quarto render pulls in a chain of Pandoc Lua filters registered in `book/quarto/config/shared/pdf/filters.yml`, not enumerated above since Binder itself never calls them directly, Quarto does. PR #2002 (2026-08-10, Volume I's second publisher draft) added one: `book/quarto/filters/fallacy-pitfall.lua`, which handles the PDF-specific spacing and paragraph-indentation rules the publisher requires around Fallacy/Pitfall callout blocks. Worth knowing this filter chain exists and grows over time if a PDF render starts looking wrong in a way HTML doesn't, the bug may be in a filter here rather than in the source `.qmd`.

## 5. Error handling

Every validation check in the suite returns the same two dataclasses, defined once, not per-check:

```
ValidationIssue: file, line, code, message, severity="error",
                  context, suggestion
ValidationRunResult: name, description, files_checked,
                      issues: List[ValidationIssue], elapsed_ms
                      .passed property = (no issues)
```

Every `_run_*` method follows the identical pattern: scan the relevant `.qmd` files, regex-match a specific problem pattern (for example, a citation bracket appearing inside a fenced code block or raw HTML block, which the citation checker deliberately excludes from being treated as a real citation), append a `ValidationIssue` with a specific `code` string per hit (`citation_in_code`, `citation_in_raw`, `principle_missing_pri_id`), and return one `ValidationRunResult`. This uniform, JSON-serializable shape is what lets `./binder check <group> --json` and CI consume identical output regardless of which check produced it, a bibliography error and a prose-style error look the same at the data-structure level even though the regex logic behind them is completely different.

## 6. Cross-component connections

**Pre-commit dispatches through Binder, it doesn't reimplement anything.** `.pre-commit-config.yaml` states this as its own documented principle, and every `book-*` hook's `entry:` line is a literal Binder invocation: `./book/binder check refs`, `./book/binder check bib`, `./book/binder format python`, `./book/binder bib mechanical --pre-commit`. There is exactly one implementation of each check, called from two different trigger contexts (a local git hook and CI), not two.

**The VS Code extension shells out to Binder too.** "MLSysBook Workbench" (`book/vscode-ext/`) defines command palette entries whose actual command strings are literal Binder invocations (`./book/binder check refs`, `./book/binder bib sync`, `./book/binder doctor`), executed via Node's `child_process.spawn` in a named terminal, with the extension tracking last-command and last-failure state for its own UI. The extension is a GUI layer over the CLI, not a separate implementation of any check.

**design-grammar is not actually validated by `book-validate-dev.yml`, despite what an earlier version of this docs set claimed.** Checked directly: `book-validate-dev.yml`'s only mention of "design-grammar" anywhere in the file is a stale comment noting a manual re-trigger dated 2026-05-18, done after an unrelated fix to `projects.json` (the contributor-crediting config, which happens to have a `design-grammar` project key), not a real path filter or validation step. A search across `book/**/*.py` for any reference to design-grammar's schema or content found nothing. design-grammar genuinely has no CI validation of any kind, piggybacked or otherwise, see [`design-grammar/ci-workflows.md`](design-grammar/ci-workflows.md) for the corrected version of this finding.

## 7. Contributing

If you are adding a new content-validation rule, add a `Scope` entry to the appropriate group in `ValidateCommand.GROUPS` and implement one `_run_*` method returning a `ValidationRunResult`, don't build a separate script, that's exactly the fragmentation this design exists to prevent. If you're touching the build chain, remember Binder's job ends at invoking `quarto render`, Quarto itself owns the pandoc/LaTeX handoff, debugging a rendering failure usually means reading Quarto's own output, not Binder's.
