# Slides: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

No dependency manifest, no style configuration. The content itself is LaTeX/Beamer, compiled with `pdflatex` (not `xelatex`, see [`system_design.md`](system_design.md)), a domain with its own typesetting conventions rather than a "coding style" in the linter sense.

The one piece of real code here is `slides/scripts/pdf2pptx.py`, the SVG-to-PDF-to-PPTX build script, and it has no dedicated style configuration either, no `pyproject.toml` exists for `slides/` at all. Match its existing conventions if you're editing it; nothing will check for you.
