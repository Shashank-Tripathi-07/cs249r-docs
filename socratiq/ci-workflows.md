# SocratiQ: CI Workflows

*Part of the full [CI workflow inventory](../ci-workflows.md). This file covers only SocratiQ's own workflow; read the top-level doc first for the general validate/preview/publish pattern most projects follow, which this one does not.*

SocratiQ has exactly one workflow, `socratiq-bundle-drift.yml`, and it is a guard, not a deploy pipeline. SocratiQ is not an independently deployed site, it ships as a pre-built `bundle.js` embedded into the book's own build (`book/quarto/tools/scripts/socratiQ/bundle.js`), and this workflow's entire job is to rebuild that bundle from source (`npm run build:vite`) on any PR touching `socratiq/src_shadow/**`, the Vite config, `package.json`, or the lockfile, and fail if the committed bundle has drifted from what the source actually produces.

This is exactly the check that flagged real drift on two Dependabot PRs (`linkify-it` and `dompurify`) while reviewing this repo's open PRs. Both bumped a version Dependabot could update in `package.json`, but neither had any way to rebuild the bundle, so the check correctly caught that the shipped code would not have matched the new dependency version. If a SocratiQ dependency bump shows this check failing, that is not a false positive, it means `npm run build:vite` needs to be run locally and the regenerated `bundle.js` committed alongside the dependency change.
