# SocratiQ: Coding Style

*Part of the repo-wide [coding style comparison](../coding-style.md).*

Plain JavaScript, no TypeScript, no `tsconfig.json` exists in this directory. No ESLint config, no Prettier, no formatter of any kind. The only automation touching this codebase's correctness at all is `socratiq-bundle-drift.yml`, which rebuilds the committed `bundle.js` from source and fails if it's drifted, a build-artifact-integrity check, not a style or lint check (see [`ci-workflows.md`](ci-workflows.md) section 5).

Practical convention worth knowing since nothing enforces it automatically: the project's own `package.json` carries an inline `_comments` block auditing every runtime dependency by bundle size and justification (documented in [`dependency-map.md`](../dependency-map.md) section 3), the closest thing SocratiQ has to a written engineering norm, self-documentation of *why* something was added, applied to dependencies rather than to code style itself. If you're adding a new dependency here, matching that documentation habit is the closest thing to "the SocratiQ way" that exists.
