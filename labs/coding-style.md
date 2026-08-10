# Labs: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

No enforced Python style, the same situation as TinyTorch: `labs/pyproject.toml` has no `[tool.ruff]` or `[tool.black]` section, and nothing in the root `.pre-commit-config.yaml` reaches `labs/`.

What actually is enforced here is structural, not stylistic, and matters more in practice: `test_static.py`'s `test_wasm_bootstrap` check requires the Cell 0 environment-detection branch (`sys.platform == "emscripten"`) to exist **verbatim**, by literal string match, in every lab file. This isn't a style preference, copying the pattern from an existing lab exactly (as [`system_design.md`](labs/system_design.md) section 10 already advises) is a hard requirement checked by CI, in a codebase with no formatter or linter otherwise.

Beyond that one structural check, match the surrounding lab file's conventions by eye, same as TinyTorch. If you're adding a new lab, start from an existing one in the same volume rather than writing from scratch.
