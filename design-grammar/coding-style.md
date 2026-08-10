# design-grammar: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

Plain JavaScript build scripts (`scripts/sync-design-grammar.mjs` and similar), no TypeScript, no `tsconfig.json`, no ESLint, no Prettier, and per [`ci-workflows.md`](ci-workflows.md), no CI validation of any kind, the project's own `npm run validate` script exists but nothing in CI currently runs it.

`grammar.yml` and `rewrite-rules.yml` are the actual source of truth this project maintains (90 primitives, 8 layers, 5 roles, 8 rewrite-rule constraints, see [`stats.md`](../stats.md)), and their own internal convention is more important than any JavaScript style question: each entry follows a consistent schema (`id`, `sym`, `name`, `role`, `layer`, `col`, `year`, `description`, `composition_links`, `rationale`), documented directly in `grammar.yml`'s own header comments. If you're editing these YAML files, matching that schema exactly matters far more than any JavaScript formatting question, since `sync-design-grammar.mjs` generates StaffML's `/framework` page directly from this file's structure.
