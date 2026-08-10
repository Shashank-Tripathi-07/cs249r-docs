# Kits: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

Kits has no dependency manifest and no code style configuration of its own, it's Quarto content plus per-board embedded C++/MicroPython/Arduino sketches, not a linted codebase.

**Not covered by the book's prose validator.** `book/cli/commands/validate.py`'s `ValidateCommand` scopes (contractions, above/below references, percent-sign spacing, covered in [`book/coding-style.md`](../book/coding-style.md)) don't extend to `kits/`, confirmed directly, `kits/` doesn't appear anywhere in `validate.py`. Content here isn't held to the same MIT Press house style the book chapters are.

For the embedded code samples themselves (Arduino sketches, Raspberry Pi Python scripts, Seeed board firmware), there's no linter or formatter wired into CI. Match the existing style of the board family you're editing, Arduino/Nicla Vision code, Raspberry Pi Python scripts, and Seeed board code each have their own established conventions by precedent rather than by enforced rule.
