# PR History

*A complete, data-derived record of every merged pull request in `harvard-edge/cs249r_book`, 1208 of them as of 2026-08-21, pulled directly from the GitHub API (`pullRequests(states: MERGED)`, paginated in full, not the search API's 1,000-result cap). Sub-project and change-type labels below are assigned by a title-keyword heuristic, not by hand, so treat borderline classifications as approximate; the raw numbers (counts, dates, line totals) are exact. This document answers "what actually happened and when," the per-project `system_design.md`/`ci-workflows.md` docs answer "how does it work now." Read those for mechanics, this one for history.*

## 1. Headline numbers

- **1208 merged PRs** across **36 months**, 2023-09-15 to 2026-08-14
- **89 distinct contributors** with at least one merged PR
- Busiest single month: **2026-04**, 261 PRs merged
- 203 of the 1208 (16.8%) are dependency bumps (mostly Dependabot), 240 (19.9%) are explicitly-tagged `fix:` commits

## 2. Contributor leaderboard

| Rank | Contributor | Merged PRs | Share |
|---|---|---|---|
| 1 | profvjreddi | 327 | 27.1% |
| 2 | hzeljko | 225 | 18.6% |
| 3 | dependabot | 201 | 16.6% |
| 4 | Shashank-Tripathi-07 | 98 | 8.1% |
| 5 | farhan523 | 62 | 5.1% |
| 6 | didier-durand | 34 | 2.8% |
| 7 | Mjrovai | 29 | 2.4% |
| 8 | kai4avaya | 21 | 1.7% |
| 9 | jasonjabbour | 19 | 1.6% |
| 10 | Naeemkh | 15 | 1.2% |
| 11 | eliasab16 | 13 | 1.1% |
| 12 | minhdang26403 | 12 | 1.0% |
| 13 | V0XNIHILI | 10 | 0.8% |
| 14 | Sara-Khosravi | 10 | 0.8% |
| 15 | 18jeffreyma | 9 | 0.7% |
| 16 | foundingnimo | 9 | 0.7% |
| 17 | zishenwan | 4 | 0.3% |
| 18 | VThuong99 | 4 | 0.3% |
| 19 | oamazonasgabriel | 4 | 0.3% |
| 20 | asgalon | 4 | 0.3% |
| 21 | octo-patch | 4 | 0.3% |
| 22 | uchendui | 3 | 0.2% |
| 23 | colbybanbury | 3 | 0.2% |
| 24 | AndreaMattiaGaravagno | 3 | 0.2% |
| 25 | marcozennaro | 2 | 0.2% |
| 26 | andreamurillomtz | 2 | 0.2% |
| 27 | agnusmaximus | 2 | 0.2% |
| 28 | srivatsankrishnan | 2 | 0.2% |
| 29 | vitasam | 2 | 0.2% |
| 30 | JaredP94 | 2 | 0.2% |

A few things worth reading correctly here rather than at face value:

- **`profvjreddi`'s 327** includes both PRs he authored and reviewed like anyone else, and merge commits/direct pushes GitHub attributes to him as the PR author when he lands a branch himself (several "Merge X into dev" and "chore(paper): regenerate..." entries in the largest-PRs table below are this pattern, not hand-authored diffs).
- **`hzeljko`'s 225** is almost entirely book content, TikZ figure updates and chapter-PDF layout passes, a real, sustained content-production role distinct from the code-fix contributors below it.
- **`dependabot`'s 201** is automated version bumps, not a human contributor, included here because the ranking is drawn from raw merge data, not because it's comparable to the humans around it.
- **`Shashank-Tripathi-07` at #4 with 98** is the highest-ranked contributor who is neither a dedicated book-content contributor, nor a bot, the top human bug-fix/code contributor in the repo's history by this metric.

## 3. Monthly timeline

```
2023-09  #  6
2023-10  ###  14
2023-11  ###  13
2023-12  ########  33
2024-01  #  1
2024-02  #  4
2024-03  #  5
2024-04  #  4
2024-05  #####  22
2024-06  #########  37
2024-07  #  3
2024-08  #########  36
2024-09  #######  27
2024-10  ##  9
2024-11  #####  22
2024-12  ###  11
2025-01  ###########  44
2025-02  #########  37
2025-03  ##########  40
2025-04  ####  17
2025-05  ##  8
2025-06  ########  33
2025-07  ####  16
2025-08  #####  21
2025-09  #  2
2025-10  #######  30
2025-11  ########  32
2025-12  ###  13
2026-01  #####  19
2026-02  ######  25
2026-03  ###########  43
2026-04  #################################################################  261
2026-05  ###########################################  171
2026-06  ################  64
2026-07  ########  33
2026-08  #############  52
```

### What the two big spikes actually were

**April 2026 (261 PRs)** is not one single milestone landing, it's the point where sustained, broad maintenance activity kicked in simultaneously across nearly every sub-project: StaffML (62 PRs), TinyTorch (49), MLSys·im (28), Book (21), Labs (19), CI/Infra (17), all in the same month. `profvjreddi` personally merged 112 PRs that month; `dependabot` landed 84; this is also the month `Shashank-Tripathi-07` became a heavy contributor (27 PRs), the onboarding point visible directly in the data.

**May 2026 (171 PRs)** shifts weight toward Book (39, largely the chapter-by-chapter PDF layout refinement pass) and a very high Dependabot month (92 PRs), alongside continued Labs/TinyTorch/Kits fix volume, much of it the ESP32-spec and dark-mode-contrast correction passes.

**August 2026 (52 PRs and still counting as of this update)** is smaller than the spring spikes but skews heavily toward TinyTorch: 25 of the month's PRs are `Shashank-Tripathi-07` (19 of those specifically TinyTorch), mostly fixes to `tito` CLI commands (crashes on malformed state files, mislabeled/nonexistent commands, milestone-status and module-completion reporting bugs), found by driving the actual CLI surface rather than reading source. The rest is routine Dependabot activity across SocratiQ, StaffML, and MLPerf EDU.

## 4. By sub-project

| Sub-project | Merged PRs | Share |
|---|---|---|
| Other/root | 428 | 35.4% |
| Book | 287 | 23.8% |
| StaffML | 127 | 10.5% |
| TinyTorch | 102 | 8.4% |
| Labs | 55 | 4.6% |
| MLSys·im | 46 | 3.8% |
| Dependencies | 39 | 3.2% |
| CI/Infra | 36 | 3.0% |
| SocratiQ | 28 | 2.3% |
| Kits | 18 | 1.5% |
| MLPerf EDU | 16 | 1.3% |
| Slides | 10 | 0.8% |
| Instructors | 6 | 0.5% |
| design-grammar | 6 | 0.5% |
| Site | 4 | 0.3% |

"Other/root" (428, the largest single bucket) is not a dumping ground for uncategorized work, it's real: root-level tooling, cross-cutting fixes that don't name a sub-project in the title, early-history PRs from before this repo adopted per-project title prefixes, and generic titles like "Update chapters" or "Fix typo" that a keyword heuristic genuinely cannot place. Book being the single largest *named* category (287, 23.8%) reflects the two-volume textbook's sheer content surface relative to the code sub-projects.

## 5. By change type

| Type | Count | Share |
|---|---|---|
| unlabeled (no conventional-commit prefix, mostly pre-2025 history and book content PRs) | 708 | 58.6% |
| fix (explicit `fix:`/`fix(...)` prefix) | 240 | 19.9% |
| dependency bump (`build(deps...`/`bump ...` prefix, or authored by dependabot regardless of its own prefix) | 203 | 16.8% |
| feat (explicit `feat:`/`feat(...)` prefix) | 25 | 2.1% |
| docs (explicit `docs:`/`docs(...)` prefix) | 11 | 0.9% |
| chore (explicit `chore:`/`chore(...)` prefix, excluding dependabot's own `chore(deps): bump` titles, counted as dependency bumps instead) | 10 | 0.8% |
| refactor (explicit `refactor:`/`refactor(...)` prefix) | 8 | 0.7% |
| test (explicit `test:`/`test(...)` prefix) | 3 | 0.2% |

The high "unlabeled" count (708, 58.6%) is a real signal about the repo's history, not a heuristic failure: conventional-commit prefixes (`fix(scope): ...`) only became a consistently enforced convention partway through this project's life. Filtering the enriched dataset by date confirms the split cleanly, PRs from 2023 and most of 2024 are overwhelmingly unlabeled, while 2026 PRs are overwhelmingly prefixed. This regeneration also revised the "fix" count downward from an earlier snapshot (307 to 240): the earlier figure appears to have counted titles containing "fix" more loosely than a strict `fix:`/`fix(...)` prefix match; this pass uses the strict prefix match consistently, matching the label already used in this table.

## 6. The 20 largest merged PRs by lines changed

| Lines changed | PR | Author | Title |
|---|---|---|---|
| 655,575 | [#1348](https://github.com/harvard-edge/cs249r_book/pull/1348) | profvjreddi | feat(vault): v0.9.0 — YAML source of truth + release pipeline + CC-BY-NC-4.0 corpus |
| 545,899 | [#1426](https://github.com/harvard-edge/cs249r_book/pull/1426) | profvjreddi | refactor(vault): schema v1.0 — self-contained YAML, flat-by-track, single source of truth |
| 471,194 | [#1436](https://github.com/harvard-edge/cs249r_book/pull/1436) | profvjreddi | chore(paper): regenerate figures + stats + macros from v1.0 corpus |
| 157,731 | [#973](https://github.com/harvard-edge/cs249r_book/pull/973) | profvjreddi | Merge dev improvements into main |
| 77,895 | [#1394](https://github.com/harvard-edge/cs249r_book/pull/1394) | kai4avaya | feat: add socratiq directory (excluding node_modules and dist) |
| 33,803 | [#1136](https://github.com/harvard-edge/cs249r_book/pull/1136) | profvjreddi | Fix optimizer gradient bug and CI improvements |
| 25,887 | [#1308](https://github.com/harvard-edge/cs249r_book/pull/1308) | profvjreddi | Merge periodic-table paper polish (passes 2-16) into dev |
| 21,390 | [#1427](https://github.com/harvard-edge/cs249r_book/pull/1427) | profvjreddi | feat(vault): phase 2 — apply 813 LLM-reviewed zone/level reclassifications |
| 18,367 | [#716](https://github.com/harvard-edge/cs249r_book/pull/716) | kai4avaya | update sr fixed quiz and sr - this is a fix for the github issues from 2/12 |
| 15,408 | [#697](https://github.com/harvard-edge/cs249r_book/pull/697) | profvjreddi | Updates to chapters |
| 14,798 | [#972](https://github.com/harvard-edge/cs249r_book/pull/972) | hzeljko | Update a Tikz figure in chapter-14 |
| 14,730 | [#669](https://github.com/harvard-edge/cs249r_book/pull/669) | profvjreddi | Pushing updated chapters till 12 |
| 14,603 | [#1546](https://github.com/harvard-edge/cs249r_book/pull/1546) | profvjreddi | fix(socratiq): bump vite-plugin-singlefile to ^2.3.0 (unblocks Bundle Drift) |
| 13,086 | [#704](https://github.com/harvard-edge/cs249r_book/pull/704) | kai4avaya | Fixing the quiz bug when generating quiz getting offline error |
| 12,593 | [#1434](https://github.com/harvard-edge/cs249r_book/pull/1434) | profvjreddi | fix(vault): make chains robust for public release |
| 11,176 | [#639](https://github.com/harvard-edge/cs249r_book/pull/639) | profvjreddi | 636 Improve AI training chapter |
| 10,537 | [#1190](https://github.com/harvard-edge/cs249r_book/pull/1190) | hzeljko | Update training chapter and add missing color definition |
| 10,022 | [#566](https://github.com/harvard-edge/cs249r_book/pull/566) | jasonjabbour | Update Chapter 17 Branch |
| 8,499 | [#285](https://github.com/harvard-edge/cs249r_book/pull/285) | profvjreddi | Getting ready to publish |
| 8,346 | [#82](https://github.com/harvard-edge/cs249r_book/pull/82) | Gjain234 | first draft of sustainable ai chapter |

Read this table as "scale of generated/bulk content," not "scale of engineering effort." The top three (#1348, #1426, #1436, all `profvjreddi`, 655K/546K/471K lines combined) are the StaffML vault's YAML-source-of-truth rewrite and its corpus/figure regeneration, machine-applied reclassifications and generated artifacts, not hand-written diffs at that size. The same caveat applies to most of this list: PDF/figure regeneration commits, large corpus rewrites, and big multi-chapter merges dominate a lines-changed ranking by construction, a single-line bug fix with a five-paragraph root-cause explanation (see the `mlperf-edu` and `tinytorch` fix series referenced throughout the per-project docs) carries more real engineering weight than its line count would ever suggest.

## 7. Complete chronological record

Every one of the 1208 merged PRs, grouped by year, oldest to newest within each year. This is the full record the stats above were computed from.

<details>
<summary><strong>2023</strong>, 66 merged PRs</summary>

| Date | PR | Author | Category | Title |
|---|---|---|---|---|
| 2023-09-15 | [#1](https://github.com/harvard-edge/cs249r_book/pull/1) | profvjreddi | Other/root | Topic/setup |
| 2023-09-16 | [#3](https://github.com/harvard-edge/cs249r_book/pull/3) | uchendui | Other/root | Updated copyright |
| 2023-09-16 | [#4](https://github.com/harvard-edge/cs249r_book/pull/4) | profvjreddi | Other/root | Lunchbox |
| 2023-09-17 | [#5](https://github.com/harvard-edge/cs249r_book/pull/5) | profvjreddi | Other/root | Fixed merge conflicts  |
| 2023-09-17 | [#6](https://github.com/harvard-edge/cs249r_book/pull/6) | profvjreddi | Other/root | Resolved a merge conflict, not sure how it slipped |
| 2023-09-27 | [#7](https://github.com/harvard-edge/cs249r_book/pull/7) | ShvetankPrakash | Other/root | Add section for emerging hardware trends |
| 2023-10-02 | [#14](https://github.com/harvard-edge/cs249r_book/pull/14) | jessicaquaye | Other/root | Updated .gitignore for JetBrains IDEs |
| 2023-10-06 | [#17](https://github.com/harvard-edge/cs249r_book/pull/17) | Mjrovai | Other/root | Exercises 2 and 4 |
| 2023-10-06 | [#19](https://github.com/harvard-edge/cs249r_book/pull/19) | Mjrovai | Other/root | Exercises 2 and 4 |
| 2023-10-06 | [#20](https://github.com/harvard-edge/cs249r_book/pull/20) | Mjrovai | Other/root | Exercises 2 and 4 |
| 2023-10-10 | [#15](https://github.com/harvard-edge/cs249r_book/pull/15) | ishapira1 | Other/root | made changes |
| 2023-10-17 | [#32](https://github.com/harvard-edge/cs249r_book/pull/32) | marcozennaro | Book | New version of the AI for Good chapter |
| 2023-10-20 | [#36](https://github.com/harvard-edge/cs249r_book/pull/36) | Naeemkh | Other/root | Add PR template |
| 2023-10-22 | [#40](https://github.com/harvard-edge/cs249r_book/pull/40) | Naeemkh | Other/root | Add Github actions |
| 2023-10-24 | [#28](https://github.com/harvard-edge/cs249r_book/pull/28) | DivyaAmirtharaj | Other/root | Updated formatting for ml-frameworks |
| 2023-10-26 | [#41](https://github.com/harvard-edge/cs249r_book/pull/41) | marcozennaro | Other/root | Added link to the tinymledu webpage |
| 2023-10-29 | [#42](https://github.com/harvard-edge/cs249r_book/pull/42) | Mjrovai | Other/root | Adding Hands-On Exercises |
| 2023-10-29 | [#44](https://github.com/harvard-edge/cs249r_book/pull/44) | Mjrovai | Book | Update kws_nicla.qmd |
| 2023-10-30 | [#37](https://github.com/harvard-edge/cs249r_book/pull/37) | 18jeffreyma | Book | TinyML Model Optimization chapter |
| 2023-10-30 | [#43](https://github.com/harvard-edge/cs249r_book/pull/43) | profvjreddi | Book | Draft of the Benchmarking AI chapter - still pending some changes.  |
| 2023-11-02 | [#45](https://github.com/harvard-edge/cs249r_book/pull/45) | 18jeffreyma | Other/root | WIP |
| 2023-11-03 | [#48](https://github.com/harvard-edge/cs249r_book/pull/48) | Mjrovai | Other/root | Adding Exercise Motion/Anomaly Detection |
| 2023-11-07 | [#50](https://github.com/harvard-edge/cs249r_book/pull/50) | happyappledog | Book | Added a medical example in AI for Good chapter |
| 2023-11-07 | [#51](https://github.com/harvard-edge/cs249r_book/pull/51) | Mjrovai | Book | Adding DSP Chapter. |
| 2023-11-08 | [#46](https://github.com/harvard-edge/cs249r_book/pull/46) | alxrod | Other/root | On Device Learning Ready For Group Review |
| 2023-11-10 | [#54](https://github.com/harvard-edge/cs249r_book/pull/54) | Ekhao | Book | Updates to Embedded Systems Chapter |
| 2023-11-15 | [#53](https://github.com/harvard-edge/cs249r_book/pull/53) | jzhou1318 | Other/root | AI HW Acceleration |
| 2023-11-16 | [#60](https://github.com/harvard-edge/cs249r_book/pull/60) | andreamurillomtz | CI/Infra | Added ML life cycle image to AI Workflow chapter |
| 2023-11-17 | [#62](https://github.com/harvard-edge/cs249r_book/pull/62) | jasonlyik | Book | Add NeuroBench to benchmarking chapter |
| 2023-11-19 | [#63](https://github.com/harvard-edge/cs249r_book/pull/63) | andreamurillomtz | Other/root | Added image to embedded systems comparing microprocessors vs microcontrollers |
| 2023-11-22 | [#57](https://github.com/harvard-edge/cs249r_book/pull/57) | arbass22 | Other/root | Added AIOps page |
| 2023-11-27 | [#78](https://github.com/harvard-edge/cs249r_book/pull/78) | eliasab16 | Other/root | add 5 visuals; fix some formatting |
| 2023-11-30 | [#66](https://github.com/harvard-edge/cs249r_book/pull/66) | eliasab16 | Book | add security and privacy chapter initial draft |
| 2023-12-05 | [#85](https://github.com/harvard-edge/cs249r_book/pull/85) | V0XNIHILI | Other/root | Fix typo (ondevice -> on-device) |
| 2023-12-05 | [#93](https://github.com/harvard-edge/cs249r_book/pull/93) | zishenwan | Other/root | Adding fig to embedded_ml cloud ml |
| 2023-12-07 | [#59](https://github.com/harvard-edge/cs249r_book/pull/59) | agnusmaximus | Other/root | AI Training - Max |
| 2023-12-07 | [#89](https://github.com/harvard-edge/cs249r_book/pull/89) | Mjrovai | Other/root | Adding Exercises - Frameworks and dl_primer |
| 2023-12-07 | [#97](https://github.com/harvard-edge/cs249r_book/pull/97) | zishenwan | Other/root | Adding figures for embedded_ai, ai_workflow, data_engineering chapters |
| 2023-12-07 | [#98](https://github.com/harvard-edge/cs249r_book/pull/98) | eliasab16 | Other/root | Added visualization and fixed some formatting. |
| 2023-12-07 | [#99](https://github.com/harvard-edge/cs249r_book/pull/99) | V0XNIHILI | Other/root | Dark mode + fix typos/grammer |
| 2023-12-07 | [#100](https://github.com/harvard-edge/cs249r_book/pull/100) | V0XNIHILI | Other/root | Fix syntax highlighting |
| 2023-12-07 | [#101](https://github.com/harvard-edge/cs249r_book/pull/101) | eliasab16 | Other/root | Changed images location |
| 2023-12-07 | [#102](https://github.com/harvard-edge/cs249r_book/pull/102) | Mjrovai | Other/root | Including exercises on Framework |
| 2023-12-08 | [#82](https://github.com/harvard-edge/cs249r_book/pull/82) | Gjain234 | Book | first draft of sustainable ai chapter |
| 2023-12-08 | [#91](https://github.com/harvard-edge/cs249r_book/pull/91) | skmur | Book | responsible ai chapter draft |
| 2023-12-09 | [#103](https://github.com/harvard-edge/cs249r_book/pull/103) | srivatsankrishnan | Other/root | Pruning support gpus |
| 2023-12-10 | [#109](https://github.com/harvard-edge/cs249r_book/pull/109) | Naeemkh | Other/root | enhance contribution |
| 2023-12-10 | [#111](https://github.com/harvard-edge/cs249r_book/pull/111) | Naeemkh | Other/root | add license file |
| 2023-12-11 | [#112](https://github.com/harvard-edge/cs249r_book/pull/112) | V0XNIHILI | Other/root | Make sure EPUB generation work |
| 2023-12-11 | [#114](https://github.com/harvard-edge/cs249r_book/pull/114) | Naeemkh | Other/root | add code of conduct |
| 2023-12-11 | [#115](https://github.com/harvard-edge/cs249r_book/pull/115) | Naeemkh | Other/root | fix the email addresses |
| 2023-12-11 | [#116](https://github.com/harvard-edge/cs249r_book/pull/116) | srivatsankrishnan | Other/root | Iss27 future trends additions |
| 2023-12-11 | [#119](https://github.com/harvard-edge/cs249r_book/pull/119) | V0XNIHILI | Book | Remove duplicate sentence from optimizations.qmd about lottery ticket hypothesis |
| 2023-12-11 | [#120](https://github.com/harvard-edge/cs249r_book/pull/120) | V0XNIHILI | Other/root | Remove .swp swap file |
| 2023-12-11 | [#124](https://github.com/harvard-edge/cs249r_book/pull/124) | V0XNIHILI | Book | Fix two missing references in optimizations.qmd |
| 2023-12-11 | [#127](https://github.com/harvard-edge/cs249r_book/pull/127) | V0XNIHILI | Other/root | Add references to ResNet-SE and ResNeXt papers |
| 2023-12-11 | [#128](https://github.com/harvard-edge/cs249r_book/pull/128) | V0XNIHILI | Book | Add references to mentioned datasets in efficient ai chapter |
| 2023-12-11 | [#130](https://github.com/harvard-edge/cs249r_book/pull/130) | agnusmaximus | Other/root | Training |
| 2023-12-12 | [#131](https://github.com/harvard-edge/cs249r_book/pull/131) | V0XNIHILI | Other/root | Add references and fix square brackets |
| 2023-12-13 | [#138](https://github.com/harvard-edge/cs249r_book/pull/138) | Naeemkh | Other/root | add version to ga |
| 2023-12-14 | [#140](https://github.com/harvard-edge/cs249r_book/pull/140) | Naeemkh | Other/root | Iss139 windows |
| 2023-12-14 | [#141](https://github.com/harvard-edge/cs249r_book/pull/141) | Naeemkh | Other/root | [WIP] Windows build |
| 2023-12-15 | [#142](https://github.com/harvard-edge/cs249r_book/pull/142) | Naeemkh | Other/root | add badges |
| 2023-12-18 | [#145](https://github.com/harvard-edge/cs249r_book/pull/145) | jaywonchung | Book | Additions for Chapter 17 Sustainable AI |
| 2023-12-19 | [#146](https://github.com/harvard-edge/cs249r_book/pull/146) | eliasab16 | Book | Optimizations chapter figures |
| 2023-12-20 | [#149](https://github.com/harvard-edge/cs249r_book/pull/149) | profvjreddi | Other/root | Added missing figure |

</details>

<details>
<summary><strong>2024</strong>, 181 merged PRs</summary>

| Date | PR | Author | Category | Title |
|---|---|---|---|---|
| 2024-01-02 | [#152](https://github.com/harvard-edge/cs249r_book/pull/152) | vitasam | Book | Typo fixed in _Installing the OpenMV IDE_ chapter |
| 2024-02-02 | [#158](https://github.com/harvard-edge/cs249r_book/pull/158) | eliasab16 | Other/root | figure references - part 1 (first 10 chapters) |
| 2024-02-02 | [#159](https://github.com/harvard-edge/cs249r_book/pull/159) | JaredP94 | Other/root | Fix rendering of incorrectly formatted references, figures, videos + Add unresolved reference |
| 2024-02-02 | [#160](https://github.com/harvard-edge/cs249r_book/pull/160) | eliasab16 | Other/root | 81 figure references/part 2 |
| 2024-02-03 | [#162](https://github.com/harvard-edge/cs249r_book/pull/162) | JaredP94 | Other/root | Fixed video renderings + Formatting consistency + Citation updates/additions |
| 2024-03-11 | [#167](https://github.com/harvard-edge/cs249r_book/pull/167) | Naeemkh | Other/root | fix hardcoded path |
| 2024-03-13 | [#169](https://github.com/harvard-edge/cs249r_book/pull/169) | Mjrovai | Other/root | Correct links |
| 2024-03-15 | [#164](https://github.com/harvard-edge/cs249r_book/pull/164) | eliasab16 | Slides | Link slides in chapters |
| 2024-03-18 | [#172](https://github.com/harvard-edge/cs249r_book/pull/172) | profvjreddi | Other/root | Error fix |
| 2024-03-28 | [#178](https://github.com/harvard-edge/cs249r_book/pull/178) | eliasab16 | Slides | added more slides |
| 2024-04-04 | [#179](https://github.com/harvard-edge/cs249r_book/pull/179) | mpstewart1 | Other/root | Edits for chapters 1-4 |
| 2024-04-05 | [#161](https://github.com/harvard-edge/cs249r_book/pull/161) | shanzehbatool | Other/root | adding web scraping colab exercise |
| 2024-04-23 | [#182](https://github.com/harvard-edge/cs249r_book/pull/182) | eliasab16 | Other/root | added videos to chapters |
| 2024-04-23 | [#184](https://github.com/harvard-edge/cs249r_book/pull/184) | shanzehbatool | Other/root | adding more colabs |
| 2024-05-05 | [#190](https://github.com/harvard-edge/cs249r_book/pull/190) | BrunoScaglione | Other/root | Changed Data Diversity and Quality section |
| 2024-05-05 | [#192](https://github.com/harvard-edge/cs249r_book/pull/192) | BrunoScaglione | Other/root | changed word "algorithms" to "models" |
| 2024-05-09 | [#193](https://github.com/harvard-edge/cs249r_book/pull/193) | eliasab16 | Other/root | added short captions for the videos |
| 2024-05-11 | [#177](https://github.com/harvard-edge/cs249r_book/pull/177) | profvjreddi | Book | Robust AI chapter |
| 2024-05-15 | [#197](https://github.com/harvard-edge/cs249r_book/pull/197) | profvjreddi | Book | 187 Proofread all the .qmd files |
| 2024-05-17 | [#204](https://github.com/harvard-edge/cs249r_book/pull/204) | profvjreddi | Other/root | Updated instructions for contributions to `dev` branch |
| 2024-05-17 | [#205](https://github.com/harvard-edge/cs249r_book/pull/205) | eliasab16 | Other/root | fixed figure captions and references |
| 2024-05-18 | [#207](https://github.com/harvard-edge/cs249r_book/pull/207) | profvjreddi | Other/root | 206 add section cross references |
| 2024-05-18 | [#208](https://github.com/harvard-edge/cs249r_book/pull/208) | profvjreddi | Book | 202 Add a conclusion chapter to the book |
| 2024-05-18 | [#209](https://github.com/harvard-edge/cs249r_book/pull/209) | profvjreddi | Other/root | Incorporating feedback from Yanjing |
| 2024-05-18 | [#210](https://github.com/harvard-edge/cs249r_book/pull/210) | profvjreddi | Other/root | Fix links to references |
| 2024-05-18 | [#211](https://github.com/harvard-edge/cs249r_book/pull/211) | profvjreddi | Other/root | Kai widget |
| 2024-05-19 | [#212](https://github.com/harvard-edge/cs249r_book/pull/212) | eliasab16 | Slides | fixed some slides links |
| 2024-05-19 | [#213](https://github.com/harvard-edge/cs249r_book/pull/213) | kai4avaya | Other/root | Kai widget |
| 2024-05-23 | [#217](https://github.com/harvard-edge/cs249r_book/pull/217) | profvjreddi | Other/root | WIP on 199-tag-prof-song-han-as-contributor |
| 2024-05-25 | [#218](https://github.com/harvard-edge/cs249r_book/pull/218) | profvjreddi | Slides | Adding Song Han's as contributor for his slides. |
| 2024-05-25 | [#219](https://github.com/harvard-edge/cs249r_book/pull/219) | profvjreddi | Other/root | Getting ready for release |
| 2024-05-25 | [#220](https://github.com/harvard-edge/cs249r_book/pull/220) | profvjreddi | Other/root | Spelling fix |
| 2024-05-25 | [#221](https://github.com/harvard-edge/cs249r_book/pull/221) | profvjreddi | Other/root | 180 Acknowledge funding resources  |
| 2024-05-30 | [#226](https://github.com/harvard-edge/cs249r_book/pull/226) | Allenkzl | Book | Add XIAO ESP32S3 Sense in hardware list and add a book about XIAO in book list. |
| 2024-05-30 | [#227](https://github.com/harvard-edge/cs249r_book/pull/227) | kai4avaya | Other/root | Fixed paper search. It's slower now, but much more accurate. Perhaps, speeding it up will be next step |
| 2024-05-31 | [#236](https://github.com/harvard-edge/cs249r_book/pull/236) | nx6xe23 | Book | Fixed minor markdown issue in text and url highlighting in Data Engineering chapter  |
| 2024-06-01 | [#234](https://github.com/harvard-edge/cs249r_book/pull/234) | profvjreddi | Other/root | Latest updates  |
| 2024-06-01 | [#237](https://github.com/harvard-edge/cs249r_book/pull/237) | Sara-Khosravi | Other/root | Improved grammar and readability of the introduction section |
| 2024-06-01 | [#238](https://github.com/harvard-edge/cs249r_book/pull/238) | profvjreddi | Other/root | Merge pull request #234 from harvard-edge/dev |
| 2024-06-01 | [#242](https://github.com/harvard-edge/cs249r_book/pull/242) | profvjreddi | Labs | 225 PDF build - need to fix how slides/labs/exercises show up |
| 2024-06-01 | [#243](https://github.com/harvard-edge/cs249r_book/pull/243) | profvjreddi | Other/root | Getting ready for release |
| 2024-06-02 | [#245](https://github.com/harvard-edge/cs249r_book/pull/245) | Naeemkh | Other/root | Drop running actions on PR. |
| 2024-06-02 | [#246](https://github.com/harvard-edge/cs249r_book/pull/246) | profvjreddi | Other/root | Added confetti.js code and registered |
| 2024-06-03 | [#247](https://github.com/harvard-edge/cs249r_book/pull/247) | kai4avaya | Other/root | Kai widget: updated the papers and markdown |
| 2024-06-04 | [#248](https://github.com/harvard-edge/cs249r_book/pull/248) | serco425 | Other/root | iss2-adding-BNN-info |
| 2024-06-04 | [#249](https://github.com/harvard-edge/cs249r_book/pull/249) | profvjreddi | Book | 241 Table rendering in Chapter 7 is messed up |
| 2024-06-04 | [#250](https://github.com/harvard-edge/cs249r_book/pull/250) | kai4avaya | Other/root | Kai widget: updated issue with disappearing menu and fixed 's' search key down |
| 2024-06-04 | [#251](https://github.com/harvard-edge/cs249r_book/pull/251) | kai4avaya | Other/root | updated papers and markdown p spacing |
| 2024-06-05 | [#252](https://github.com/harvard-edge/cs249r_book/pull/252) | kai4avaya | Other/root | Kai widget: updated issue with darkmode |
| 2024-06-08 | [#254](https://github.com/harvard-edge/cs249r_book/pull/254) | Sara-Khosravi | Other/root | edit-data-engineering |
| 2024-06-10 | [#255](https://github.com/harvard-edge/cs249r_book/pull/255) | profvjreddi | Other/root | Getting ready for release |
| 2024-06-11 | [#260](https://github.com/harvard-edge/cs249r_book/pull/260) | profvjreddi | Other/root | 76 Break chapters into subfiles |
| 2024-06-11 | [#261](https://github.com/harvard-edge/cs249r_book/pull/261) | profvjreddi | Other/root | 253 Videos need cross-references |
| 2024-06-11 | [#262](https://github.com/harvard-edge/cs249r_book/pull/262) | profvjreddi | Other/root | Updated video callouts and minor fixes |
| 2024-06-11 | [#263](https://github.com/harvard-edge/cs249r_book/pull/263) | profvjreddi | Other/root | Fixed a bug where the dev build was broken and pushed into main |
| 2024-06-11 | [#264](https://github.com/harvard-edge/cs249r_book/pull/264) | profvjreddi | Other/root | Fix merge error causing build failures |
| 2024-06-12 | [#266](https://github.com/harvard-edge/cs249r_book/pull/266) | kai4avaya | Other/root | updated gen ai page // fixed bundles |
| 2024-06-13 | [#268](https://github.com/harvard-edge/cs249r_book/pull/268) | kai4avaya | Other/root | update to gen ai page. keep text, flicker topics |
| 2024-06-15 | [#271](https://github.com/harvard-edge/cs249r_book/pull/271) | kai4avaya | Other/root | suggested queries, capitalizations |
| 2024-06-15 | [#274](https://github.com/harvard-edge/cs249r_book/pull/274) | YangZhou1997 | Other/root | a batch of typo and format fixes |
| 2024-06-15 | [#278](https://github.com/harvard-edge/cs249r_book/pull/278) | profvjreddi | Other/root | Fixed formatting issue |
| 2024-06-18 | [#275](https://github.com/harvard-edge/cs249r_book/pull/275) | profvjreddi | Other/root | 270 Support multiple hardware vendors  |
| 2024-06-18 | [#280](https://github.com/harvard-edge/cs249r_book/pull/280) | colbybanbury | Book | Trim and update benchmarking.qmd |
| 2024-06-18 | [#281](https://github.com/harvard-edge/cs249r_book/pull/281) | profvjreddi | Other/root | 279 citation missing |
| 2024-06-18 | [#283](https://github.com/harvard-edge/cs249r_book/pull/283) | profvjreddi | Labs | 282 create overview section for labs |
| 2024-06-18 | [#284](https://github.com/harvard-edge/cs249r_book/pull/284) | profvjreddi | Book | Revert "Trim and update benchmarking.qmd" |
| 2024-06-18 | [#285](https://github.com/harvard-edge/cs249r_book/pull/285) | profvjreddi | Other/root | Getting ready to publish |
| 2024-06-19 | [#286](https://github.com/harvard-edge/cs249r_book/pull/286) | profvjreddi | Other/root | Getting ready to publish |
| 2024-06-19 | [#287](https://github.com/harvard-edge/cs249r_book/pull/287) | profvjreddi | Other/root | Getting ready to publish! |
| 2024-06-20 | [#290](https://github.com/harvard-edge/cs249r_book/pull/290) | profvjreddi | Other/root | Update all the references |
| 2024-06-21 | [#288](https://github.com/harvard-edge/cs249r_book/pull/288) | profvjreddi | Other/root | Update README.md |
| 2024-06-21 | [#293](https://github.com/harvard-edge/cs249r_book/pull/293) | emmanuel2406 | Other/root | Fixed video 3.1 link |
| 2024-06-26 | [#298](https://github.com/harvard-edge/cs249r_book/pull/298) | colbybanbury | Other/root | adds wake vision colab as an exercise |
| 2024-07-30 | [#306](https://github.com/harvard-edge/cs249r_book/pull/306) | FinAminToastCrunch | Other/root | Iss304 add references |
| 2024-07-30 | [#316](https://github.com/harvard-edge/cs249r_book/pull/316) | jasonjabbour | Book | chapter 3 revisions |
| 2024-07-30 | [#318](https://github.com/harvard-edge/cs249r_book/pull/318) | Naeemkh | Other/root | fix a bug with user_full_name |
| 2024-08-04 | [#333](https://github.com/harvard-edge/cs249r_book/pull/333) | profvjreddi | Other/root | 310 Updated PDF to follow Edward Tufte style |
| 2024-08-05 | [#334](https://github.com/harvard-edge/cs249r_book/pull/334) | profvjreddi | Other/root | Updated Credit -> Source: and fixed formatting style to be consistent |
| 2024-08-05 | [#336](https://github.com/harvard-edge/cs249r_book/pull/336) | profvjreddi | Other/root | 335 Use Grid tables for proper formatting of bullets |
| 2024-08-05 | [#337](https://github.com/harvard-edge/cs249r_book/pull/337) | profvjreddi | Other/root | 326 PDF video links are broken |
| 2024-08-05 | [#341](https://github.com/harvard-edge/cs249r_book/pull/341) | profvjreddi | Other/root | 331 Marginnotes needs to be left aligned, but they seem to be justified  |
| 2024-08-05 | [#342](https://github.com/harvard-edge/cs249r_book/pull/342) | profvjreddi | Other/root | 326 PDF video links are broken |
| 2024-08-06 | [#343](https://github.com/harvard-edge/cs249r_book/pull/343) | jasonjabbour | Book | 321 student feedback chapter 4 |
| 2024-08-08 | [#344](https://github.com/harvard-edge/cs249r_book/pull/344) | jasonjabbour | Book | 322 student feedback chapter 6 |
| 2024-08-14 | [#350](https://github.com/harvard-edge/cs249r_book/pull/350) | profvjreddi | Book | 346 2 typos in frameworks.qmd |
| 2024-08-15 | [#313](https://github.com/harvard-edge/cs249r_book/pull/313) | Sara-Khosravi | Book | Enhancements and Revisions to efficient_ai.qmd with Professor Vijay's Feedback |
| 2024-08-15 | [#317](https://github.com/harvard-edge/cs249r_book/pull/317) | Sara-Khosravi | Other/root | Edited the Privacy and Security Section (First 26 Pages) |
| 2024-08-15 | [#349](https://github.com/harvard-edge/cs249r_book/pull/349) | jasonjabbour | Book | 324 student feedback chapter 7 |
| 2024-08-15 | [#352](https://github.com/harvard-edge/cs249r_book/pull/352) | profvjreddi | Other/root | 320 Add date to YML |
| 2024-08-15 | [#356](https://github.com/harvard-edge/cs249r_book/pull/356) | jasonjabbour | Book | Chapter 8 Sarah Updates |
| 2024-08-17 | [#360](https://github.com/harvard-edge/cs249r_book/pull/360) | profvjreddi | Book | 358 Typos in "data_engineering.qmd" |
| 2024-08-19 | [#365](https://github.com/harvard-edge/cs249r_book/pull/365) | profvjreddi | Other/root | 351 Fix section header spacing |
| 2024-08-21 | [#367](https://github.com/harvard-edge/cs249r_book/pull/367) | colbybanbury | Book | Add Wake Vision to zoo_datasets.qmd |
| 2024-08-21 | [#370](https://github.com/harvard-edge/cs249r_book/pull/370) | profvjreddi | Other/root | 368 Some typos and a suggestion to look for more |
| 2024-08-22 | [#357](https://github.com/harvard-edge/cs249r_book/pull/357) | jasonjabbour | Book | 353 student feedback chapter eight |
| 2024-08-22 | [#371](https://github.com/harvard-edge/cs249r_book/pull/371) | profvjreddi | Other/root | 369 Missing format ...maybe |
| 2024-08-22 | [#373](https://github.com/harvard-edge/cs249r_book/pull/373) | profvjreddi | Other/root | 372 Slowdown after changing "header-includes.tex" |
| 2024-08-22 | [#375](https://github.com/harvard-edge/cs249r_book/pull/375) | profvjreddi | Other/root | 374 CO2 (2 as a subscript) |
| 2024-08-22 | [#379](https://github.com/harvard-edge/cs249r_book/pull/379) | jasonjabbour | Other/root | Update Branch 353 |
| 2024-08-23 | [#381](https://github.com/harvard-edge/cs249r_book/pull/381) | profvjreddi | Book | 380 Self-evident in hw_acceleration.qmd |
| 2024-08-25 | [#390](https://github.com/harvard-edge/cs249r_book/pull/390) | profvjreddi | Other/root | 386 Duplicate title |
| 2024-08-25 | [#391](https://github.com/harvard-edge/cs249r_book/pull/391) | profvjreddi | Book | 389 Typo in efficient_ai.qmd |
| 2024-08-25 | [#392](https://github.com/harvard-edge/cs249r_book/pull/392) | profvjreddi | Other/root | 388 Superfluous hyphens |
| 2024-08-25 | [#393](https://github.com/harvard-edge/cs249r_book/pull/393) | profvjreddi | Other/root | 387 Please update info... |
| 2024-08-25 | [#394](https://github.com/harvard-edge/cs249r_book/pull/394) | profvjreddi | Book | 385 Bad link in hw_acceleration.qmd / Section 10.6 |
| 2024-08-25 | [#396](https://github.com/harvard-edge/cs249r_book/pull/396) | profvjreddi | Book | 395 Typo: Chapter 13.2.2 |
| 2024-08-26 | [#399](https://github.com/harvard-edge/cs249r_book/pull/399) | Sara-Khosravi | Other/root | Edit privacy security  |
| 2024-08-26 | [#401](https://github.com/harvard-edge/cs249r_book/pull/401) | jasonjabbour | Book | Student Feedback Chapter 9 |
| 2024-08-26 | [#403](https://github.com/harvard-edge/cs249r_book/pull/403) | profvjreddi | Book | 402 Note on first 4 chapter of "benchmarking.qmd" |
| 2024-08-29 | [#376](https://github.com/harvard-edge/cs249r_book/pull/376) | Mjrovai | Labs | 366 labs raspi support |
| 2024-08-29 | [#418](https://github.com/harvard-edge/cs249r_book/pull/418) | profvjreddi | Book | 417 Notes In "ondevice_learning.qmd" |
| 2024-08-30 | [#420](https://github.com/harvard-edge/cs249r_book/pull/420) | profvjreddi | Book | 419 Notes on the first part of "ops.qmd" |
| 2024-09-01 | [#416](https://github.com/harvard-edge/cs249r_book/pull/416) | Mjrovai | Labs | 366 labs raspi support |
| 2024-09-01 | [#422](https://github.com/harvard-edge/cs249r_book/pull/422) | Mjrovai | Labs | 366 labs raspi support - format and text review |
| 2024-09-01 | [#423](https://github.com/harvard-edge/cs249r_book/pull/423) | profvjreddi | Other/root | 421 Add Model Serving section to MLOps |
| 2024-09-01 | [#425](https://github.com/harvard-edge/cs249r_book/pull/425) | profvjreddi | Book | 424 Last part of "ops.qmd" |
| 2024-09-02 | [#429](https://github.com/harvard-edge/cs249r_book/pull/429) | profvjreddi | Other/root | 427 Incomplete or awkward sentence in the Industrial IoT section |
| 2024-09-03 | [#430](https://github.com/harvard-edge/cs249r_book/pull/430) | jasonjabbour | Book | Chapter 10 Student Feedback |
| 2024-09-04 | [#433](https://github.com/harvard-edge/cs249r_book/pull/433) | profvjreddi | Book | 431 Notes in "privacy_security.qmd" |
| 2024-09-04 | [#435](https://github.com/harvard-edge/cs249r_book/pull/435) | profvjreddi | Other/root | 432 Images in PDF version without reference when on the left |
| 2024-09-05 | [#437](https://github.com/harvard-edge/cs249r_book/pull/437) | profvjreddi | Book | 436 Some points in responsible_ai.qmd |
| 2024-09-08 | [#439](https://github.com/harvard-edge/cs249r_book/pull/439) | profvjreddi | Book | 438 Points to review in "sustainable_ai.qmd" |
| 2024-09-09 | [#441](https://github.com/harvard-edge/cs249r_book/pull/441) | profvjreddi | Book | 440 First part of "robust_ai.qmd" |
| 2024-09-11 | [#443](https://github.com/harvard-edge/cs249r_book/pull/443) | profvjreddi | Book | 442 Observations in the second part of "robust_ai.qmd" |
| 2024-09-11 | [#444](https://github.com/harvard-edge/cs249r_book/pull/444) | Mjrovai | Other/root | Upload the Object Detection Lab |
| 2024-09-11 | [#446](https://github.com/harvard-edge/cs249r_book/pull/446) | profvjreddi | Book | 445 Notes in "ai_for_good.qmd" |
| 2024-09-12 | [#448](https://github.com/harvard-edge/cs249r_book/pull/448) | profvjreddi | Book | 447 In "conclusion.qmd" |
| 2024-09-14 | [#451](https://github.com/harvard-edge/cs249r_book/pull/451) | bilgeacun | Other/root | Proofreading the sustainability section - fixing typos |
| 2024-09-17 | [#415](https://github.com/harvard-edge/cs249r_book/pull/415) | fatimajshah | Other/root | First 9 chapters in correct format |
| 2024-09-17 | [#454](https://github.com/harvard-edge/cs249r_book/pull/454) | Mjrovai | Labs | 366 labs raspi support |
| 2024-09-18 | [#453](https://github.com/harvard-edge/cs249r_book/pull/453) | Sara-Khosravi | Other/root | Edit2 privacy security |
| 2024-09-18 | [#458](https://github.com/harvard-edge/cs249r_book/pull/458) | profvjreddi | Other/root | 455 Link for Video 3.1 on 3 DL Prime is broken |
| 2024-09-18 | [#459](https://github.com/harvard-edge/cs249r_book/pull/459) | profvjreddi | Other/root | 457 Clean-up PR template  |
| 2024-09-19 | [#461](https://github.com/harvard-edge/cs249r_book/pull/461) | profvjreddi | Kits | 452 Notes in "seeed/xiao_esp32s3/image_classification/image_classification.qmd" |
| 2024-09-19 | [#462](https://github.com/harvard-edge/cs249r_book/pull/462) | profvjreddi | Labs | 449 Some notes on "Labs" |
| 2024-09-20 | [#460](https://github.com/harvard-edge/cs249r_book/pull/460) | profvjreddi | Labs | 456 Other notes on labs |
| 2024-09-20 | [#464](https://github.com/harvard-edge/cs249r_book/pull/464) | profvjreddi | Other/root | 463 2 notes and 2 warnings |
| 2024-09-29 | [#472](https://github.com/harvard-edge/cs249r_book/pull/472) | Sara-Khosravi | Other/root | privacy security changes- add few lines |
| 2024-09-29 | [#473](https://github.com/harvard-edge/cs249r_book/pull/473) | Mjrovai | Other/root | Including the Raspberry Pi SLM lab |
| 2024-10-21 | [#478](https://github.com/harvard-edge/cs249r_book/pull/478) | Sara-Khosravi | Other/root | Privacy & Security Update - Secure Boot Enhancements |
| 2024-10-25 | [#479](https://github.com/harvard-edge/cs249r_book/pull/479) | AbenezerKb | Book | rename images file name for privacy and security chapter figures |
| 2024-10-25 | [#480](https://github.com/harvard-edge/cs249r_book/pull/480) | profvjreddi | Other/root | 475 Some notes in llm and other generic issues |
| 2024-10-28 | [#486](https://github.com/harvard-edge/cs249r_book/pull/486) | profvjreddi | Other/root | 485 Only 2 notes |
| 2024-10-28 | [#487](https://github.com/harvard-edge/cs249r_book/pull/487) | profvjreddi | Other/root | 481 I was too hasty and drastic... |
| 2024-10-30 | [#491](https://github.com/harvard-edge/cs249r_book/pull/491) | profvjreddi | Other/root | 471 Go through all figures |
| 2024-10-30 | [#495](https://github.com/harvard-edge/cs249r_book/pull/495) | profvjreddi | Labs | 494 Split content into labs / core theory concepts |
| 2024-10-30 | [#496](https://github.com/harvard-edge/cs249r_book/pull/496) | profvjreddi | Other/root | 492 Table empty in PDF |
| 2024-10-31 | [#489](https://github.com/harvard-edge/cs249r_book/pull/489) | aryatschand | MLPerf EDU | Add MLPerf Power to benchmarking |
| 2024-11-01 | [#499](https://github.com/harvard-edge/cs249r_book/pull/499) | profvjreddi | Other/root | 498 Some new issues |
| 2024-11-03 | [#503](https://github.com/harvard-edge/cs249r_book/pull/503) | profvjreddi | Other/root | 488 Problems about "Parts" |
| 2024-11-03 | [#504](https://github.com/harvard-edge/cs249r_book/pull/504) | profvjreddi | Other/root | 501 One section is mistakenly bold |
| 2024-11-03 | [#505](https://github.com/harvard-edge/cs249r_book/pull/505) | profvjreddi | Other/root | 474 Bad rendering for some links |
| 2024-11-03 | [#507](https://github.com/harvard-edge/cs249r_book/pull/507) | profvjreddi | Other/root | 506 TOC spacing is messed up -- too close to titles when section numbers are big in count |
| 2024-11-06 | [#511](https://github.com/harvard-edge/cs249r_book/pull/511) | profvjreddi | Other/root | 510 Create a reading guide for learners |
| 2024-11-12 | [#515](https://github.com/harvard-edge/cs249r_book/pull/515) | Sara-Khosravi | Book | Final edit to Security Chapter - Today's Changes Only |
| 2024-11-12 | [#516](https://github.com/harvard-edge/cs249r_book/pull/516) | Sara-Khosravi | Book | Edit security chapter f |
| 2024-11-12 | [#517](https://github.com/harvard-edge/cs249r_book/pull/517) | vitasam | Other/root | README updated with quarto rendering to EPUB |
| 2024-11-12 | [#518](https://github.com/harvard-edge/cs249r_book/pull/518) | jasonjabbour | Book | Student feedback Chapter 11 |
| 2024-11-12 | [#519](https://github.com/harvard-edge/cs249r_book/pull/519) | jasonjabbour | Book | Chapter 12 Student Feedback |
| 2024-11-15 | [#520](https://github.com/harvard-edge/cs249r_book/pull/520) | profvjreddi | Other/root | 468 Rewrite introduction |
| 2024-11-15 | [#521](https://github.com/harvard-edge/cs249r_book/pull/521) | profvjreddi | Other/root | Getting ready for major v0.3 release |
| 2024-11-16 | [#522](https://github.com/harvard-edge/cs249r_book/pull/522) | jasonjabbour | Book | Student Feedback Chapter 13 |
| 2024-11-17 | [#523](https://github.com/harvard-edge/cs249r_book/pull/523) | jasonjabbour | Book | Student Feedback Chapter 15 |
| 2024-11-17 | [#526](https://github.com/harvard-edge/cs249r_book/pull/526) | jasonjabbour | Book | Student Feedback Chapter 16 |
| 2024-11-18 | [#484](https://github.com/harvard-edge/cs249r_book/pull/484) | kai4avaya | Other/root | updating quiz widget |
| 2024-11-18 | [#527](https://github.com/harvard-edge/cs249r_book/pull/527) | jasonjabbour | Book | Student Feedback Chapter 14 |
| 2024-11-18 | [#528](https://github.com/harvard-edge/cs249r_book/pull/528) | jasonjabbour | Book | Student Feedback Chapter 19 |
| 2024-11-25 | [#532](https://github.com/harvard-edge/cs249r_book/pull/532) | profvjreddi | Other/root | 531 A warning and a few typos |
| 2024-11-27 | [#538](https://github.com/harvard-edge/cs249r_book/pull/538) | profvjreddi | Other/root | 536 Badge images not visible in pdf |
| 2024-11-27 | [#539](https://github.com/harvard-edge/cs249r_book/pull/539) | profvjreddi | Other/root | 537 Sub-titles marked as normal text in bold |
| 2024-12-04 | [#550](https://github.com/harvard-edge/cs249r_book/pull/550) | profvjreddi | SocratiQ | 549 typo "browserAchi" in socratiq.qmd   |
| 2024-12-05 | [#547](https://github.com/harvard-edge/cs249r_book/pull/547) | Mjrovai | Other/root | Gen ai lab chapters |
| 2024-12-05 | [#551](https://github.com/harvard-edge/cs249r_book/pull/551) | profvjreddi | Other/root | 482 Add Mobile ML section to ML Systems |
| 2024-12-23 | [#557](https://github.com/harvard-edge/cs249r_book/pull/557) | profvjreddi | Book | 554 Rewrite chapter 3 with a ML systems focus |
| 2024-12-24 | [#560](https://github.com/harvard-edge/cs249r_book/pull/560) | Mjrovai | Other/root | Primer chapters review |
| 2024-12-24 | [#561](https://github.com/harvard-edge/cs249r_book/pull/561) | profvjreddi | Other/root | Updated purpose for all chapters - first draft |
| 2024-12-29 | [#564](https://github.com/harvard-edge/cs249r_book/pull/564) | profvjreddi | Book | 559 New chapter 4 - DL architectures -- MAJOR CHANGES |
| 2024-12-30 | [#565](https://github.com/harvard-edge/cs249r_book/pull/565) | profvjreddi | Other/root | 563 Some points in ml_systems and dl_primer. |
| 2024-12-30 | [#566](https://github.com/harvard-edge/cs249r_book/pull/566) | jasonjabbour | Book | Update Chapter 17 Branch |
| 2024-12-30 | [#567](https://github.com/harvard-edge/cs249r_book/pull/567) | Naeemkh | Other/root | Add badge labels |
| 2024-12-31 | [#568](https://github.com/harvard-edge/cs249r_book/pull/568) | profvjreddi | Other/root | 555 Figure 2.7 is not rendered correctly (dev version only) |

</details>

<details>
<summary><strong>2025</strong>, 293 merged PRs</summary>

| Date | PR | Author | Category | Title |
|---|---|---|---|---|
| 2025-01-02 | [#571](https://github.com/harvard-edge/cs249r_book/pull/571) | jasonjabbour | Book | Chapter 18 (old chapter 17) Feedback Revisions |
| 2025-01-02 | [#573](https://github.com/harvard-edge/cs249r_book/pull/573) | jasonjabbour | Other/root | Conclusion Revisions |
| 2025-01-02 | [#574](https://github.com/harvard-edge/cs249r_book/pull/574) | profvjreddi | Book | 570 Improve/rewrite chapter 5  |
| 2025-01-02 | [#575](https://github.com/harvard-edge/cs249r_book/pull/575) | profvjreddi | Book | 572 Bad link in \cs249r_book\index.qmd and others |
| 2025-01-03 | [#578](https://github.com/harvard-edge/cs249r_book/pull/578) | profvjreddi | Book | 577 Notes in _quarto and in dnn_architectures.qmd |
| 2025-01-03 | [#579](https://github.com/harvard-edge/cs249r_book/pull/579) | profvjreddi | Other/root | Preparing major v0.3.0 release |
| 2025-01-03 | [#582](https://github.com/harvard-edge/cs249r_book/pull/582) | profvjreddi | Other/root | 581 2 typos |
| 2025-01-03 | [#584](https://github.com/harvard-edge/cs249r_book/pull/584) | profvjreddi | Other/root | 583 PDF formatting needs to be improved |
| 2025-01-05 | [#587](https://github.com/harvard-edge/cs249r_book/pull/587) | zishenwan | Other/root | Improvement on Ch3 (DL Primer): add code snapshot for 3.5 training and 3.6 inference, fix equation typo |
| 2025-01-05 | [#588](https://github.com/harvard-edge/cs249r_book/pull/588) | zishenwan | Other/root | Improvement on Ch4 (DNN Architectures): add visualization figures and tool links |
| 2025-01-08 | [#591](https://github.com/harvard-edge/cs249r_book/pull/591) | profvjreddi | Book | 589 Chapter 1, PDF format |
| 2025-01-08 | [#592](https://github.com/harvard-edge/cs249r_book/pull/592) | profvjreddi | Other/root | 590 Some sparse notes |
| 2025-01-11 | [#595](https://github.com/harvard-edge/cs249r_book/pull/595) | profvjreddi | Book | 593 Revise chapter 6 - Data Engr. |
| 2025-01-11 | [#598](https://github.com/harvard-edge/cs249r_book/pull/598) | profvjreddi | Book | 597 Warning in data_engineering.qmd |
| 2025-01-12 | [#596](https://github.com/harvard-edge/cs249r_book/pull/596) | hzeljko | Other/root | 594 improve pdf rendering |
| 2025-01-12 | [#601](https://github.com/harvard-edge/cs249r_book/pull/601) | profvjreddi | CI/Infra | 599 Feedback on Chapter 5: AI Workflow |
| 2025-01-12 | [#602](https://github.com/harvard-edge/cs249r_book/pull/602) | profvjreddi | Book | 600 Feedback on Chapter 6: Data Engineering |
| 2025-01-13 | [#607](https://github.com/harvard-edge/cs249r_book/pull/607) | profvjreddi | Other/root | 606 Typos in some files |
| 2025-01-13 | [#609](https://github.com/harvard-edge/cs249r_book/pull/609) | Mjrovai | Book | Update vlm.qmd |
| 2025-01-13 | [#610](https://github.com/harvard-edge/cs249r_book/pull/610) | hzeljko | Other/root | 594 improve pdf rendering |
| 2025-01-16 | [#617](https://github.com/harvard-edge/cs249r_book/pull/617) | EddieJ03 | Other/root | Clarification to parameter storage bound for RNNs |
| 2025-01-16 | [#618](https://github.com/harvard-edge/cs249r_book/pull/618) | hzeljko | Other/root | 594 improve pdf rendering |
| 2025-01-17 | [#616](https://github.com/harvard-edge/cs249r_book/pull/616) | profvjreddi | Book | 611 Update AI Frameworks chapter |
| 2025-01-17 | [#621](https://github.com/harvard-edge/cs249r_book/pull/621) | profvjreddi | Other/root | 620 Typos "×" in dnn_architectures |
| 2025-01-20 | [#623](https://github.com/harvard-edge/cs249r_book/pull/623) | hzeljko | Other/root | Summary |
| 2025-01-20 | [#631](https://github.com/harvard-edge/cs249r_book/pull/631) | arighosh05 | Book | Updated dnn_architectures.qmd to fix typo in 4.1 |
| 2025-01-21 | [#626](https://github.com/harvard-edge/cs249r_book/pull/626) | mmaz | Book | Edits and additional citations for the data engineering chapter |
| 2025-01-21 | [#633](https://github.com/harvard-edge/cs249r_book/pull/633) | hzeljko | Other/root | Issue #632 |
| 2025-01-21 | [#634](https://github.com/harvard-edge/cs249r_book/pull/634) | profvjreddi | Other/root | 603 Rewrite AI for Good |
| 2025-01-21 | [#635](https://github.com/harvard-edge/cs249r_book/pull/635) | profvjreddi | Book | 629 Chapter engineering.qmd. |
| 2025-01-23 | [#638](https://github.com/harvard-edge/cs249r_book/pull/638) | 18jeffreyma | Other/root | initial new content (wip) |
| 2025-01-23 | [#640](https://github.com/harvard-edge/cs249r_book/pull/640) | hzeljko | Other/root | Update Cover page |
| 2025-01-23 | [#643](https://github.com/harvard-edge/cs249r_book/pull/643) | profvjreddi | Book | 641 Suggestions and typos in ai_for_good.qmd |
| 2025-01-23 | [#644](https://github.com/harvard-edge/cs249r_book/pull/644) | Naeemkh | Other/root | Update upload-artifact version to 4 |
| 2025-01-25 | [#654](https://github.com/harvard-edge/cs249r_book/pull/654) | uchendui | Other/root | Added explicit version for quarto to use for rendering. This should f… |
| 2025-01-26 | [#656](https://github.com/harvard-edge/cs249r_book/pull/656) | hzeljko | Book | Chapter 7 |
| 2025-01-27 | [#639](https://github.com/harvard-edge/cs249r_book/pull/639) | profvjreddi | Book | 636 Improve AI training chapter |
| 2025-01-27 | [#650](https://github.com/harvard-edge/cs249r_book/pull/650) | profvjreddi | Book | 647 Update AI efficiency chapter |
| 2025-01-27 | [#658](https://github.com/harvard-edge/cs249r_book/pull/658) | uchendui | Other/root | Added inkscape installation |
| 2025-01-28 | [#657](https://github.com/harvard-edge/cs249r_book/pull/657) | hzeljko | Other/root | Update _quarto.yml |
| 2025-01-28 | [#659](https://github.com/harvard-edge/cs249r_book/pull/659) | profvjreddi | Other/root | 652 Create "Resources" placeholder |
| 2025-01-29 | [#660](https://github.com/harvard-edge/cs249r_book/pull/660) | profvjreddi | Other/root | 655 Fix .callout blocks titles |
| 2025-01-29 | [#661](https://github.com/harvard-edge/cs249r_book/pull/661) | profvjreddi | Other/root | 651 Write a script to find duplicate definitions |
| 2025-01-30 | [#663](https://github.com/harvard-edge/cs249r_book/pull/663) | profvjreddi | Other/root | 662 "computation needs" block in 4.3.4 System Implications |
| 2025-02-01 | [#664](https://github.com/harvard-edge/cs249r_book/pull/664) | 18jeffreyma | Other/root | Jeff/ai training minor updates |
| 2025-02-01 | [#665](https://github.com/harvard-edge/cs249r_book/pull/665) | profvjreddi | Book | Chapter 8 |
| 2025-02-02 | [#667](https://github.com/harvard-edge/cs249r_book/pull/667) | hzeljko | Other/root | Updated figure 8.8 |
| 2025-02-02 | [#668](https://github.com/harvard-edge/cs249r_book/pull/668) | hzeljko | Book | Chapter 9 |
| 2025-02-02 | [#669](https://github.com/harvard-edge/cs249r_book/pull/669) | profvjreddi | Other/root | Pushing updated chapters till 12 |
| 2025-02-02 | [#670](https://github.com/harvard-edge/cs249r_book/pull/670) | hzeljko | Book | Update efficient_ai.qmd - problem with the 'name path' option |
| 2025-02-02 | [#671](https://github.com/harvard-edge/cs249r_book/pull/671) | profvjreddi | Book | 666 Update Benchmarking chapter 12 |
| 2025-02-03 | [#672](https://github.com/harvard-edge/cs249r_book/pull/672) | profvjreddi | Book | Release updates from PDF rendering + benchmarking chapter |
| 2025-02-04 | [#673](https://github.com/harvard-edge/cs249r_book/pull/673) | hzeljko | Book | Updated chapter-19 |
| 2025-02-04 | [#675](https://github.com/harvard-edge/cs249r_book/pull/675) | profvjreddi | Other/root | 674 Update all bibs and check for DOI validity |
| 2025-02-04 | [#678](https://github.com/harvard-edge/cs249r_book/pull/678) | profvjreddi | Other/root | Addressing feedback from Jeff |
| 2025-02-05 | [#679](https://github.com/harvard-edge/cs249r_book/pull/679) | hzeljko | Book | Chapter-12 |
| 2025-02-05 | [#683](https://github.com/harvard-edge/cs249r_book/pull/683) | profvjreddi | Other/root | 682 Need a Textbook changelog to track updates |
| 2025-02-05 | [#684](https://github.com/harvard-edge/cs249r_book/pull/684) | hzeljko | Other/root | Summary - chapter1 |
| 2025-02-07 | [#692](https://github.com/harvard-edge/cs249r_book/pull/692) | hzeljko | Book | Updated and fixed ml_systems.qmd |
| 2025-02-07 | [#693](https://github.com/harvard-edge/cs249r_book/pull/693) | atcheng2 | Book | Add in FastML science graph to benchmarking challenges chapter |
| 2025-02-07 | [#694](https://github.com/harvard-edge/cs249r_book/pull/694) | aryatschand | MLPerf EDU | add mlperf power trends |
| 2025-02-07 | [#695](https://github.com/harvard-edge/cs249r_book/pull/695) | profvjreddi | Other/root | 686 Benchmarking visualization additions (Arya + Andy) |
| 2025-02-08 | [#697](https://github.com/harvard-edge/cs249r_book/pull/697) | profvjreddi | Other/root | Updates to chapters |
| 2025-02-09 | [#699](https://github.com/harvard-edge/cs249r_book/pull/699) | hzeljko | Book | Updated ch3: dl_primer.qmd |
| 2025-02-09 | [#700](https://github.com/harvard-edge/cs249r_book/pull/700) | hzeljko | Book | Updated ch4: dnn_architectures.qmd |
| 2025-02-09 | [#701](https://github.com/harvard-edge/cs249r_book/pull/701) | hzeljko | CI/Infra | Updated ch5: workflow.qmd |
| 2025-02-14 | [#704](https://github.com/harvard-edge/cs249r_book/pull/704) | kai4avaya | Other/root | Fixing the quiz bug when generating quiz getting offline error |
| 2025-02-14 | [#707](https://github.com/harvard-edge/cs249r_book/pull/707) | kai4avaya | Other/root | updating to dev - I am testing to ensure we remove the previous inkmode build error |
| 2025-02-16 | [#703](https://github.com/harvard-edge/cs249r_book/pull/703) | hzeljko | Book | Updated ch6: data_engineering.qmd |
| 2025-02-16 | [#706](https://github.com/harvard-edge/cs249r_book/pull/706) | hzeljko | Book | Updated chapter 7: frameworks.qmd |
| 2025-02-16 | [#708](https://github.com/harvard-edge/cs249r_book/pull/708) | kai4avaya | Other/root | Widget quiz testing to see if i can remove the previous changelog errors  |
| 2025-02-16 | [#710](https://github.com/harvard-edge/cs249r_book/pull/710) | kai4avaya | Other/root | dev issue git action bug resolution |
| 2025-02-16 | [#712](https://github.com/harvard-edge/cs249r_book/pull/712) | profvjreddi | Book | 702 update the ai acceleration chapter |
| 2025-02-17 | [#715](https://github.com/harvard-edge/cs249r_book/pull/715) | profvjreddi | Other/root | 713 Fix repeated Acronyms |
| 2025-02-17 | [#716](https://github.com/harvard-edge/cs249r_book/pull/716) | kai4avaya | Other/root | update sr fixed quiz and sr - this is a fix for the github issues from 2/12 |
| 2025-02-19 | [#714](https://github.com/harvard-edge/cs249r_book/pull/714) | 18jeffreyma | Other/root | minor efficiency edits |
| 2025-02-19 | [#718](https://github.com/harvard-edge/cs249r_book/pull/718) | hzeljko | Book | Updated chapter 11 |
| 2025-02-19 | [#719](https://github.com/harvard-edge/cs249r_book/pull/719) | hzeljko | Book | Updated chapter 12 |
| 2025-02-21 | [#720](https://github.com/harvard-edge/cs249r_book/pull/720) | hzeljko | Book | Updated chapter 11 |
| 2025-02-21 | [#722](https://github.com/harvard-edge/cs249r_book/pull/722) | hzeljko | Book | Update ch11: hw_acceleration.qmd |
| 2025-02-25 | [#721](https://github.com/harvard-edge/cs249r_book/pull/721) | hzeljko | Book | Updated chapter-8 |
| 2025-03-01 | [#726](https://github.com/harvard-edge/cs249r_book/pull/726) | profvjreddi | Book | 709 Improve Chapter 10 Model Optimization |
| 2025-03-03 | [#727](https://github.com/harvard-edge/cs249r_book/pull/727) | hzeljko | Labs | Updated arduino/nicla_vision LABS part |
| 2025-03-04 | [#728](https://github.com/harvard-edge/cs249r_book/pull/728) | TheHiddenLayer | Other/root | Finish unfinished sentence. |
| 2025-03-05 | [#729](https://github.com/harvard-edge/cs249r_book/pull/729) | profvjreddi | Other/root | Fixing workflows to match pre-commit |
| 2025-03-05 | [#731](https://github.com/harvard-edge/cs249r_book/pull/731) | hzeljko | Book | Updated chapter 10: optimizations.qmd |
| 2025-03-05 | [#732](https://github.com/harvard-edge/cs249r_book/pull/732) | hzeljko | Book | Update chapter 2: |
| 2025-03-06 | [#733](https://github.com/harvard-edge/cs249r_book/pull/733) | 18jeffreyma | Other/root | some epoch ai figures inserted |
| 2025-03-06 | [#735](https://github.com/harvard-edge/cs249r_book/pull/735) | hzeljko | Book | Update chapter 3: dl_primer.qmd |
| 2025-03-07 | [#736](https://github.com/harvard-edge/cs249r_book/pull/736) | hzeljko | Book | Update chapter 7: frameworks.qmd |
| 2025-03-07 | [#737](https://github.com/harvard-edge/cs249r_book/pull/737) | hzeljko | Book | Update chapter 9: efficient_ai.qmd |
| 2025-03-07 | [#738](https://github.com/harvard-edge/cs249r_book/pull/738) | profvjreddi | Other/root | Use docker for build |
| 2025-03-08 | [#739](https://github.com/harvard-edge/cs249r_book/pull/739) | hzeljko | Book | Update ch1: introduction.qmd |
| 2025-03-08 | [#740](https://github.com/harvard-edge/cs249r_book/pull/740) | hzeljko | CI/Infra | Update ch5: workflow |
| 2025-03-11 | [#742](https://github.com/harvard-edge/cs249r_book/pull/742) | hzeljko | Book | Chapter 17 |
| 2025-03-11 | [#743](https://github.com/harvard-edge/cs249r_book/pull/743) | hzeljko | Book | Chapter 8 |
| 2025-03-13 | [#745](https://github.com/harvard-edge/cs249r_book/pull/745) | 18jeffreyma | Other/root | fixed all broken links, fixes #741 |
| 2025-03-13 | [#746](https://github.com/harvard-edge/cs249r_book/pull/746) | 18jeffreyma | Other/root | added model optimization feedback fixes |
| 2025-03-13 | [#748](https://github.com/harvard-edge/cs249r_book/pull/748) | hzeljko | Book | Update chapter 3: dl_primer |
| 2025-03-16 | [#751](https://github.com/harvard-edge/cs249r_book/pull/751) | profvjreddi | Book | 749 Improve chapter 18 robust AI |
| 2025-03-16 | [#752](https://github.com/harvard-edge/cs249r_book/pull/752) | profvjreddi | Other/root | Fix broken links |
| 2025-03-17 | [#753](https://github.com/harvard-edge/cs249r_book/pull/753) | Mjrovai | Book | Update setup.qmd |
| 2025-03-17 | [#754](https://github.com/harvard-edge/cs249r_book/pull/754) | hzeljko | Book | Update ch10: optimizations.qmd |
| 2025-03-18 | [#755](https://github.com/harvard-edge/cs249r_book/pull/755) | profvjreddi | Other/root | footnote keys streamlining |
| 2025-03-19 | [#759](https://github.com/harvard-edge/cs249r_book/pull/759) | profvjreddi | Other/root | 756 scaling laws in efficient ai |
| 2025-03-19 | [#760](https://github.com/harvard-edge/cs249r_book/pull/760) | profvjreddi | Other/root | 757 Duplicate references in bib files |
| 2025-03-20 | [#762](https://github.com/harvard-edge/cs249r_book/pull/762) | profvjreddi | Book | 761 Little typo in efficient_ai.qmd |
| 2025-03-20 | [#765](https://github.com/harvard-edge/cs249r_book/pull/765) | profvjreddi | Book | 763 Update definitions in each chapter |
| 2025-03-22 | [#768](https://github.com/harvard-edge/cs249r_book/pull/768) | profvjreddi | Book | 766 Update MLOps chapter |
| 2025-03-22 | [#769](https://github.com/harvard-edge/cs249r_book/pull/769) | profvjreddi | Other/root | 767 Setup vale formatting checks |
| 2025-03-23 | [#770](https://github.com/harvard-edge/cs249r_book/pull/770) | profvjreddi | Other/root | 764 Some typos with "Source " |
| 2025-03-23 | [#773](https://github.com/harvard-edge/cs249r_book/pull/773) | profvjreddi | Other/root | 772 Hypenation setup |
| 2025-03-25 | [#774](https://github.com/harvard-edge/cs249r_book/pull/774) | profvjreddi | Other/root | 771 Setup automatic spellchecking |
| 2025-03-25 | [#775](https://github.com/harvard-edge/cs249r_book/pull/775) | hzeljko | Book | Update chapter 18: robust_ai.qmd |
| 2025-03-25 | [#778](https://github.com/harvard-edge/cs249r_book/pull/778) | hzeljko | Book | Update chapter 17_sustainable_ai |
| 2025-03-27 | [#780](https://github.com/harvard-edge/cs249r_book/pull/780) | profvjreddi | Other/root | 779 A few notes |
| 2025-03-28 | [#781](https://github.com/harvard-edge/cs249r_book/pull/781) | profvjreddi | Other/root | Find unreferenced labels |
| 2025-03-28 | [#783](https://github.com/harvard-edge/cs249r_book/pull/783) | profvjreddi | Other/root | Created a stats counter for figures/tables etc.  |
| 2025-03-31 | [#784](https://github.com/harvard-edge/cs249r_book/pull/784) | hzeljko | Book | Updated chapter 13_ops |
| 2025-03-31 | [#786](https://github.com/harvard-edge/cs249r_book/pull/786) | profvjreddi | Book | 785 Typo in efficient_ai.qmd |
| 2025-03-31 | [#787](https://github.com/harvard-edge/cs249r_book/pull/787) | hzeljko | Labs | Update LABS part 2_seeed_xiao_esp32s3 |
| 2025-04-01 | [#788](https://github.com/harvard-edge/cs249r_book/pull/788) | TheHiddenLayer | Other/root | swap dimension order for W^L (in  |
| 2025-04-07 | [#794](https://github.com/harvard-edge/cs249r_book/pull/794) | hzeljko | Kits | 3labs raspi |
| 2025-04-07 | [#795](https://github.com/harvard-edge/cs249r_book/pull/795) | hzeljko | Other/root | 4labs shared |
| 2025-04-08 | [#796](https://github.com/harvard-edge/cs249r_book/pull/796) | hzeljko | Book | Updated chapter-1 |
| 2025-04-09 | [#797](https://github.com/harvard-edge/cs249r_book/pull/797) | hzeljko | Other/root | Update chapter2_ml_systems |
| 2025-04-09 | [#798](https://github.com/harvard-edge/cs249r_book/pull/798) | Mjrovai | Labs | Labs review - Nicla Setup and Image Classification |
| 2025-04-10 | [#800](https://github.com/harvard-edge/cs249r_book/pull/800) | hzeljko | Book | Update chapter-3_dl_primer |
| 2025-04-10 | [#801](https://github.com/harvard-edge/cs249r_book/pull/801) | Mjrovai | Labs | Labs review |
| 2025-04-10 | [#802](https://github.com/harvard-edge/cs249r_book/pull/802) | profvjreddi | Book | 799 Some notes in hw_acceleration.qmd |
| 2025-04-11 | [#803](https://github.com/harvard-edge/cs249r_book/pull/803) | Mjrovai | Labs | Labs review - Nicla - Object Detection  |
| 2025-04-12 | [#806](https://github.com/harvard-edge/cs249r_book/pull/806) | hzeljko | Book | Update chapter-4_dnn_architectures |
| 2025-04-12 | [#807](https://github.com/harvard-edge/cs249r_book/pull/807) | hzeljko | Book | Update chapter-5_workflow |
| 2025-04-13 | [#808](https://github.com/harvard-edge/cs249r_book/pull/808) | hzeljko | Book | Update chapter-6_data_engineering |
| 2025-04-14 | [#809](https://github.com/harvard-edge/cs249r_book/pull/809) | hzeljko | Book | Update chapter-7>frameworks |
| 2025-04-16 | [#811](https://github.com/harvard-edge/cs249r_book/pull/811) | hzeljko | Book | Update chapter-8_training |
| 2025-04-16 | [#812](https://github.com/harvard-edge/cs249r_book/pull/812) | hzeljko | Book | Update chapter-9_efficient |
| 2025-04-26 | [#817](https://github.com/harvard-edge/cs249r_book/pull/817) | hzeljko | Book | Update chapter-10_optimization |
| 2025-05-03 | [#820](https://github.com/harvard-edge/cs249r_book/pull/820) | profvjreddi | Book | 813 Improve on-device learning chapter |
| 2025-05-12 | [#822](https://github.com/harvard-edge/cs249r_book/pull/822) | hzeljko | Other/root | Update chapter_14: ondevice_learning |
| 2025-05-27 | [#825](https://github.com/harvard-edge/cs249r_book/pull/825) | profvjreddi | Book | 824 Revise Security &  Privacy chapter |
| 2025-05-27 | [#827](https://github.com/harvard-edge/cs249r_book/pull/827) | profvjreddi | Book | PDF Build hangs on dataengineering.qmd  |
| 2025-05-29 | [#830](https://github.com/harvard-edge/cs249r_book/pull/830) | hzeljko | Book | Chapter 4_dnn_architectures_labeling and referencing code blocks |
| 2025-05-30 | [#834](https://github.com/harvard-edge/cs249r_book/pull/834) | hzeljko | Other/root | Update ch_7_frameworks_Labeling |
| 2025-05-31 | [#837](https://github.com/harvard-edge/cs249r_book/pull/837) | hzeljko | Other/root | Update chapter_4_dnn_architectires |
| 2025-05-31 | [#838](https://github.com/harvard-edge/cs249r_book/pull/838) | hzeljko | Other/root | Update chapter_8_training |
| 2025-06-01 | [#839](https://github.com/harvard-edge/cs249r_book/pull/839) | hzeljko | Other/root | Update chapter_10_optimizations |
| 2025-06-01 | [#840](https://github.com/harvard-edge/cs249r_book/pull/840) | aethernavshulkraven-allain | Book | Update hw_acceleration.qmd |
| 2025-06-02 | [#823](https://github.com/harvard-edge/cs249r_book/pull/823) | 18jeffreyma | Other/root | (WIP) Jeff/issue 776 |
| 2025-06-02 | [#842](https://github.com/harvard-edge/cs249r_book/pull/842) | hzeljko | Other/root | Update chapter_11_hw_acceleration |
| 2025-06-02 | [#843](https://github.com/harvard-edge/cs249r_book/pull/843) | hzeljko | Other/root | Update chapter_13_ops |
| 2025-06-02 | [#844](https://github.com/harvard-edge/cs249r_book/pull/844) | hzeljko | Other/root | Updated chapter_14_ondevice_learning |
| 2025-06-02 | [#845](https://github.com/harvard-edge/cs249r_book/pull/845) | profvjreddi | Book | 829 Updated Responsible AI chapter |
| 2025-06-04 | [#846](https://github.com/harvard-edge/cs249r_book/pull/846) | hzeljko | Other/root | Updated chapter_15_privacy_security |
| 2025-06-04 | [#847](https://github.com/harvard-edge/cs249r_book/pull/847) | hzeljko | Other/root | Updated chapter_16_responsible_ai |
| 2025-06-05 | [#849](https://github.com/harvard-edge/cs249r_book/pull/849) | profvjreddi | Other/root | Grammar clean-up |
| 2025-06-06 | [#835](https://github.com/harvard-edge/cs249r_book/pull/835) | Mjrovai | Labs | Adding a new Device: Seeed Grove AI Vision V2 Labs |
| 2025-06-06 | [#850](https://github.com/harvard-edge/cs249r_book/pull/850) | hzeljko | Other/root | Updated chapter_19_ai_for_good |
| 2025-06-06 | [#851](https://github.com/harvard-edge/cs249r_book/pull/851) | hzeljko | Other/root | Revert accidental direct commit to `dev` (8ad9b11) |
| 2025-06-06 | [#852](https://github.com/harvard-edge/cs249r_book/pull/852) | hzeljko | Other/root | Update _quarto.yml: fix-titlepage-alignment |
| 2025-06-06 | [#853](https://github.com/harvard-edge/cs249r_book/pull/853) | hzeljko | Other/root | Update chapter_15_privacy_security_figure 15_12 has been rearranged  |
| 2025-06-07 | [#854](https://github.com/harvard-edge/cs249r_book/pull/854) | hzeljko | Other/root | Update chapter_15_privacy_security_table_15_6_fix |
| 2025-06-07 | [#855](https://github.com/harvard-edge/cs249r_book/pull/855) | hzeljko | Other/root | Update chapter_6_data_engineering_figure_6_2 |
| 2025-06-09 | [#857](https://github.com/harvard-edge/cs249r_book/pull/857) | hzeljko | Other/root | Update header-inlcudes.tex_footnote_sounter_fiix |
| 2025-06-09 | [#858](https://github.com/harvard-edge/cs249r_book/pull/858) | hzeljko | Other/root | Update chapter_6_data_engineering_fig_6_8 |
| 2025-06-09 | [#859](https://github.com/harvard-edge/cs249r_book/pull/859) | hzeljko | Other/root | Updated chapter_1_introduction |
| 2025-06-09 | [#860](https://github.com/harvard-edge/cs249r_book/pull/860) | hzeljko | Other/root | Updated chapter_3_dl_primer |
| 2025-06-09 | [#861](https://github.com/harvard-edge/cs249r_book/pull/861) | hzeljko | Other/root | Updated chapter_13_ops |
| 2025-06-10 | [#862](https://github.com/harvard-edge/cs249r_book/pull/862) | hzeljko | Book | Update chapter-15_privacy_security |
| 2025-06-10 | [#863](https://github.com/harvard-edge/cs249r_book/pull/863) | hzeljko | Other/root | Update chapter_19_ai_for_good |
| 2025-06-10 | [#864](https://github.com/harvard-edge/cs249r_book/pull/864) | hzeljko | SocratiQ | Update_socratiq |
| 2025-06-12 | [#865](https://github.com/harvard-edge/cs249r_book/pull/865) | hzeljko | Other/root | Enable EPUB build in Quarto |
| 2025-06-16 | [#866](https://github.com/harvard-edge/cs249r_book/pull/866) | hzeljko | Other/root | Redesigned part heading and added custom callout blocks (Question & A… |
| 2025-06-21 | [#869](https://github.com/harvard-edge/cs249r_book/pull/869) | hzeljko | Other/root | Implement PDF part summaries |
| 2025-06-21 | [#870](https://github.com/harvard-edge/cs249r_book/pull/870) | profvjreddi | Other/root | 867 Generate unique Section IDs |
| 2025-06-23 | [#873](https://github.com/harvard-edge/cs249r_book/pull/873) | hzeljko | Other/root | Redefine question and answer callouts to match Quarto defaults |
| 2025-06-23 | [#876](https://github.com/harvard-edge/cs249r_book/pull/876) | profvjreddi | Book | Enhances book structure for backmatter |
| 2025-06-27 | [#878](https://github.com/harvard-edge/cs249r_book/pull/878) | hzeljko | Slides | Create new callouts for slides/videos/exercises |
| 2025-06-28 | [#880](https://github.com/harvard-edge/cs249r_book/pull/880) | hzeljko | Other/root | Customize TOC part formatting, callout block colors and font size |
| 2025-07-01 | [#881](https://github.com/harvard-edge/cs249r_book/pull/881) | profvjreddi | Other/root | Format quizzes using LUA filter instead of python script |
| 2025-07-02 | [#884](https://github.com/harvard-edge/cs249r_book/pull/884) | hzeljko | Other/root | LUA filter for quizzes has been fixed |
| 2025-07-04 | [#886](https://github.com/harvard-edge/cs249r_book/pull/886) | hzeljko | Other/root | Counters for callot blocks in html |
| 2025-07-11 | [#889](https://github.com/harvard-edge/cs249r_book/pull/889) | profvjreddi | Other/root | Removes resources sections from chapters |
| 2025-07-11 | [#890](https://github.com/harvard-edge/cs249r_book/pull/890) | hzeljko | Other/root | Fix: Ensure full insertion of quizzes into rendered output |
| 2025-07-11 | [#892](https://github.com/harvard-edge/cs249r_book/pull/892) | hzeljko | Other/root | Final test after syncing with upstream dev |
| 2025-07-15 | [#894](https://github.com/harvard-edge/cs249r_book/pull/894) | hzeljko | Other/root | Make new margin note box |
| 2025-07-18 | [#895](https://github.com/harvard-edge/cs249r_book/pull/895) | hzeljko | Book | Improve styling of reference and chapter connection blocks in html; Add new Tikz figures in ch1 and ch2 |
| 2025-07-19 | [#896](https://github.com/harvard-edge/cs249r_book/pull/896) | hzeljko | Other/root | Add new Tikz figures in ch3 to ch6 |
| 2025-07-20 | [#897](https://github.com/harvard-edge/cs249r_book/pull/897) | hzeljko | Book | New TikZ figures in Chapter 7 and Chapter 8 |
| 2025-07-24 | [#903](https://github.com/harvard-edge/cs249r_book/pull/903) | profvjreddi | Other/root | Renames figure identifier for consistency. |
| 2025-07-24 | [#904](https://github.com/harvard-edge/cs249r_book/pull/904) | hzeljko | Other/root | Fixing inject_crossrefs.lua |
| 2025-07-25 | [#906](https://github.com/harvard-edge/cs249r_book/pull/906) | hzeljko | Other/root | New Tikz figures in chapters 9 and 10 |
| 2025-07-29 | [#907](https://github.com/harvard-edge/cs249r_book/pull/907) | Mjrovai | Labs | Updating Labs with the XIAOML Kit (replacing XIAO ESP32S3 Sense)  |
| 2025-07-29 | [#908](https://github.com/harvard-edge/cs249r_book/pull/908) | profvjreddi | CI/Infra | Fix github build workflow after repo reorganization |
| 2025-07-31 | [#911](https://github.com/harvard-edge/cs249r_book/pull/911) | Mjrovai | Book | Update subtitles on vlm.qmd |
| 2025-08-03 | [#913](https://github.com/harvard-edge/cs249r_book/pull/913) | Mjrovai | Kits | Updating Image Classifiction - XIAOML kit (ESP32S3 Sense) |
| 2025-08-03 | [#914](https://github.com/harvard-edge/cs249r_book/pull/914) | hzeljko | Book | Add new TikZ figures in chapter 12 |
| 2025-08-07 | [#921](https://github.com/harvard-edge/cs249r_book/pull/921) | hzeljko | Book | Add new TikZ figures in chapter 4 and chapter 16 |
| 2025-08-07 | [#923](https://github.com/harvard-edge/cs249r_book/pull/923) | Mjrovai | Kits | Xiaoml kit |
| 2025-08-07 | [#924](https://github.com/harvard-edge/cs249r_book/pull/924) | hzeljko | Book | Fix page break in chapter 16 |
| 2025-08-08 | [#925](https://github.com/harvard-edge/cs249r_book/pull/925) | Mjrovai | Kits | Xiaoml kit - Img Class Lab - Deploy with Edge Impulse  |
| 2025-08-08 | [#926](https://github.com/harvard-edge/cs249r_book/pull/926) | hzeljko | Other/root | Fix visual break in margin tcolorbox |
| 2025-08-11 | [#927](https://github.com/harvard-edge/cs249r_book/pull/927) | hzeljko | Other/root | Fix colors in code listing |
| 2025-08-11 | [#928](https://github.com/harvard-edge/cs249r_book/pull/928) | hzeljko | Book | Add a new TikZ figure in chapter 12 |
| 2025-08-11 | [#929](https://github.com/harvard-edge/cs249r_book/pull/929) | hzeljko | Book | Add a new TikZ figure in chapter 2 |
| 2025-08-12 | [#930](https://github.com/harvard-edge/cs249r_book/pull/930) | hzeljko | Book | Add a new Tikz figure in chapter 9 |
| 2025-08-12 | [#933](https://github.com/harvard-edge/cs249r_book/pull/933) | profvjreddi | CI/Infra | feat(ci): add workflow to fix file casing |
| 2025-08-15 | [#935](https://github.com/harvard-edge/cs249r_book/pull/935) | profvjreddi | Other/root | 934 Figures in ML Systems |
| 2025-08-15 | [#936](https://github.com/harvard-edge/cs249r_book/pull/936) | profvjreddi | Other/root | 910 Mlsysbook_chapter18_4_dropout |
| 2025-08-15 | [#937](https://github.com/harvard-edge/cs249r_book/pull/937) | profvjreddi | Other/root | Clarifies dropout's role in uncertainty estimation. |
| 2025-08-19 | [#942](https://github.com/harvard-edge/cs249r_book/pull/942) | profvjreddi | CI/Infra | Workflow updates |
| 2025-08-23 | [#949](https://github.com/harvard-edge/cs249r_book/pull/949) | taunoe | Other/root | Added missing loop() |
| 2025-08-23 | [#950](https://github.com/harvard-edge/cs249r_book/pull/950) | profvjreddi | Other/root | fix: Correct biological neuron mapping and add optical interconnects context |
| 2025-08-25 | [#951](https://github.com/harvard-edge/cs249r_book/pull/951) | profvjreddi | Other/root | fix: merge overlapping memory allocation subsections and improve table readability |
| 2025-08-25 | [#952](https://github.com/harvard-edge/cs249r_book/pull/952) | profvjreddi | Other/root | Fix/issue 945 transient errors causes |
| 2025-08-29 | [#957](https://github.com/harvard-edge/cs249r_book/pull/957) | taunoe | Other/root | Boards package - not library |
| 2025-09-04 | [#958](https://github.com/harvard-edge/cs249r_book/pull/958) | hzeljko | Other/root | Add custom-code.theme and clean up code highlighting setup |
| 2025-09-23 | [#965](https://github.com/harvard-edge/cs249r_book/pull/965) | hzeljko | Book | Polish Chapter 1; align callout icon colors; add new TikZ figures |
| 2025-10-01 | [#966](https://github.com/harvard-edge/cs249r_book/pull/966) | hzeljko | Other/root | New Tikz figures in chapters 4 & 12 |
| 2025-10-06 | [#967](https://github.com/harvard-edge/cs249r_book/pull/967) | hzeljko | Book | Update Tikz figure in chapter-12 |
| 2025-10-06 | [#968](https://github.com/harvard-edge/cs249r_book/pull/968) | hzeljko | Book | Update Tikz figure in chapter-7 |
| 2025-10-07 | [#969](https://github.com/harvard-edge/cs249r_book/pull/969) | hzeljko | Book | Update Tikz figure in chapter 11 |
| 2025-10-07 | [#970](https://github.com/harvard-edge/cs249r_book/pull/970) | hzeljko | Book | Update two TikZ figures in chapter-13 |
| 2025-10-08 | [#971](https://github.com/harvard-edge/cs249r_book/pull/971) | hzeljko | Book | Update a Tikz figure in chapter-16 |
| 2025-10-08 | [#972](https://github.com/harvard-edge/cs249r_book/pull/972) | hzeljko | Book | Update a Tikz figure in chapter-14 |
| 2025-10-09 | [#973](https://github.com/harvard-edge/cs249r_book/pull/973) | profvjreddi | Other/root | Merge dev improvements into main |
| 2025-10-12 | [#978](https://github.com/harvard-edge/cs249r_book/pull/978) | hzeljko | Book | Update Tikz figures in chapter 20 |
| 2025-10-12 | [#979](https://github.com/harvard-edge/cs249r_book/pull/979) | hzeljko | Book | Update a Tikz figure in chapter 10 |
| 2025-10-13 | [#977](https://github.com/harvard-edge/cs249r_book/pull/977) | VThuong99 | Other/root | Fix: correct CNN architecture question option order (issue #976) |
| 2025-10-13 | [#981](https://github.com/harvard-edge/cs249r_book/pull/981) | VThuong99 | Other/root | Fix LaTeX typo and word break in Section 4.7.3 (issue #980) |
| 2025-10-17 | [#975](https://github.com/harvard-edge/cs249r_book/pull/975) | VThuong99 | Other/root | Fix example equation by adding transpose and clarify note on vector orientation (issue #974) |
| 2025-10-17 | [#985](https://github.com/harvard-edge/cs249r_book/pull/985) | profvjreddi | Other/root | 982 Incorrect self-reference to Section 7.3.1 within Section 7.3.1 |
| 2025-10-17 | [#986](https://github.com/harvard-edge/cs249r_book/pull/986) | profvjreddi | Book | 984 Inconsistent formatting of 'Purpose' section in Chapter 10 (Model Optimizations) |
| 2025-10-17 | [#987](https://github.com/harvard-edge/cs249r_book/pull/987) | hzeljko | Book | Update new Tikz figures in chapter 15 and chapter |
| 2025-10-19 | [#988](https://github.com/harvard-edge/cs249r_book/pull/988) | VThuong99 | Other/root | Fix small typo |
| 2025-10-24 | [#989](https://github.com/harvard-edge/cs249r_book/pull/989) | eimlav | Other/root | Fix dark mode stylings for callout examples |
| 2025-10-26 | [#992](https://github.com/harvard-edge/cs249r_book/pull/992) | swilcock0 | Other/root | Fix typo in IMU data description |
| 2025-10-28 | [#996](https://github.com/harvard-edge/cs249r_book/pull/996) | foundingnimo | SocratiQ | Improve clarity of SocratiQ functionality description |
| 2025-10-29 | [#999](https://github.com/harvard-edge/cs249r_book/pull/999) | eimlav | Kits | Fix link to ML kits |
| 2025-10-29 | [#1001](https://github.com/harvard-edge/cs249r_book/pull/1001) | didier-durand | Other/root | Fixing typos discovered while reading |
| 2025-10-29 | [#1002](https://github.com/harvard-edge/cs249r_book/pull/1002) | didier-durand | Labs | Fixing typos discovered while reading Labs section |
| 2025-10-30 | [#1003](https://github.com/harvard-edge/cs249r_book/pull/1003) | foundingnimo | SocratiQ | Refine image descriptions for SocratiQ documentation and fix grammar |
| 2025-10-30 | [#1004](https://github.com/harvard-edge/cs249r_book/pull/1004) | didier-durand | Other/root | Fixing dangling links in /docs/PUBLISH_LIVE_WORKFLOWS.md |
| 2025-10-30 | [#1005](https://github.com/harvard-edge/cs249r_book/pull/1005) | didier-durand | Other/root | Fixing dangling link in README.md |
| 2025-10-31 | [#1006](https://github.com/harvard-edge/cs249r_book/pull/1006) | didier-durand | Other/root | Fixing dangling link in README.md |
| 2025-10-31 | [#1007](https://github.com/harvard-edge/cs249r_book/pull/1007) | didier-durand | Other/root | Fixing dangling link in CUSTOM_EXTENSIONS.md |
| 2025-10-31 | [#1008](https://github.com/harvard-edge/cs249r_book/pull/1008) | didier-durand | Other/root | Fixing typos in contribute.md |
| 2025-10-31 | [#1010](https://github.com/harvard-edge/cs249r_book/pull/1010) | profvjreddi | Other/root | fix(introduction): correct Amdahl's Law speedup calculation from 5x to 1.25x |
| 2025-11-01 | [#1012](https://github.com/harvard-edge/cs249r_book/pull/1012) | foundingnimo | Other/root | Clarify distinctions between ML and traditional software |
| 2025-11-01 | [#1013](https://github.com/harvard-edge/cs249r_book/pull/1013) | didier-durand | Other/root | Fixing typos in 2 files |
| 2025-11-01 | [#1015](https://github.com/harvard-edge/cs249r_book/pull/1015) | profvjreddi | Other/root | Simplifies MYCIN example in introduction |
| 2025-11-01 | [#1017](https://github.com/harvard-edge/cs249r_book/pull/1017) | profvjreddi | Book | fix(epub): ensure XHTML compliance in index.qmd |
| 2025-11-02 | [#1019](https://github.com/harvard-edge/cs249r_book/pull/1019) | didier-durand | Other/root | Fixing typos in 2 files |
| 2025-11-02 | [#1021](https://github.com/harvard-edge/cs249r_book/pull/1021) | profvjreddi | Other/root | Expands introduction quiz and clarifies skew |
| 2025-11-02 | [#1022](https://github.com/harvard-edge/cs249r_book/pull/1022) | foundingnimo | Other/root | Fix typos in DARPA Grand Challenge distance |
| 2025-11-03 | [#1023](https://github.com/harvard-edge/cs249r_book/pull/1023) | didier-durand | Book | Fixing typos in ops.qmd |
| 2025-11-03 | [#1025](https://github.com/harvard-edge/cs249r_book/pull/1025) | profvjreddi | Other/root | Fixes a typo in Figure 2.1 node label |
| 2025-11-03 | [#1026](https://github.com/harvard-edge/cs249r_book/pull/1026) | profvjreddi | Other/root | fix(epub): ensure XHTML compliance in frontmatter files |
| 2025-11-04 | [#1027](https://github.com/harvard-edge/cs249r_book/pull/1027) | foundingnimo | Other/root | Refine grammar of ImageNet breakthrough explanation |
| 2025-11-04 | [#1028](https://github.com/harvard-edge/cs249r_book/pull/1028) | foundingnimo | Other/root | Tidy up sentence structure in ML code->data shift |
| 2025-11-04 | [#1029](https://github.com/harvard-edge/cs249r_book/pull/1029) | oamazonasgabriel | Other/root | Update DOI for article in introduction.bib |
| 2025-11-05 | [#1030](https://github.com/harvard-edge/cs249r_book/pull/1030) | oamazonasgabriel | CI/Infra | CI: Fix "Update contributors list" action failing on non-fast-forward |
| 2025-11-05 | [#1031](https://github.com/harvard-edge/cs249r_book/pull/1031) | foundingnimo | Other/root | Expand on data drift and model adaptation challenges |
| 2025-11-05 | [#1032](https://github.com/harvard-edge/cs249r_book/pull/1032) | foundingnimo | Other/root | Small changes to address formatting |
| 2025-11-05 | [#1035](https://github.com/harvard-edge/cs249r_book/pull/1035) | profvjreddi | Other/root | fix(quizzes): correct MCQ answer explanations and add validation |
| 2025-11-06 | [#1037](https://github.com/harvard-edge/cs249r_book/pull/1037) | profvjreddi | Other/root | Removes duplicate words in DL primer |
| 2025-11-06 | [#1038](https://github.com/harvard-edge/cs249r_book/pull/1038) | didier-durand | Labs | Fixing typos in labs section |
| 2025-11-06 | [#1039](https://github.com/harvard-edge/cs249r_book/pull/1039) | didier-durand | Other/root | Fixing typos re. spelling of Ultralytics library |
| 2025-11-07 | [#1040](https://github.com/harvard-edge/cs249r_book/pull/1040) | foundingnimo | Other/root | Suggest an alternate research article |
| 2025-11-12 | [#1044](https://github.com/harvard-edge/cs249r_book/pull/1044) | didier-durand | SocratiQ | Fixing link and typo in README for SocratiQ |
| 2025-11-12 | [#1045](https://github.com/harvard-edge/cs249r_book/pull/1045) | didier-durand | Other/root | Fixing typos in 3 files |
| 2025-11-13 | [#1046](https://github.com/harvard-edge/cs249r_book/pull/1046) | didier-durand | Other/root | Fixing typos in 3 files |
| 2025-11-16 | [#1047](https://github.com/harvard-edge/cs249r_book/pull/1047) | didier-durand | Other/root | Fixing typos in 3 files for Lbs section |
| 2025-11-19 | [#1048](https://github.com/harvard-edge/cs249r_book/pull/1048) | didier-durand | Other/root | Fixing typos in 2 files |
| 2025-11-20 | [#1049](https://github.com/harvard-edge/cs249r_book/pull/1049) | didier-durand | Other/root | Fixing typos |
| 2025-11-21 | [#1050](https://github.com/harvard-edge/cs249r_book/pull/1050) | didier-durand | Other/root | Fix typos in files |
| 2025-11-27 | [#1051](https://github.com/harvard-edge/cs249r_book/pull/1051) | didier-durand | Other/root | Fixing typos in 3 files |
| 2025-11-28 | [#1054](https://github.com/harvard-edge/cs249r_book/pull/1054) | hzeljko | Book | Update chapter-6 |
| 2025-11-28 | [#1055](https://github.com/harvard-edge/cs249r_book/pull/1055) | didier-durand | Other/root | Fixing typos in 3 files |
| 2025-11-29 | [#1056](https://github.com/harvard-edge/cs249r_book/pull/1056) | didier-durand | Other/root | Fix typos in 2 files |
| 2025-12-01 | [#1058](https://github.com/harvard-edge/cs249r_book/pull/1058) | didier-durand | Other/root | Fixing typos in 2 files |
| 2025-12-02 | [#1060](https://github.com/harvard-edge/cs249r_book/pull/1060) | didier-durand | Other/root | Fixing typos in 3 files |
| 2025-12-03 | [#1062](https://github.com/harvard-edge/cs249r_book/pull/1062) | didier-durand | Other/root | [Doc] removed all remaining 'teeths' |
| 2025-12-04 | [#1063](https://github.com/harvard-edge/cs249r_book/pull/1063) | didier-durand | Other/root | [Doc] fixing incorrect sample code |
| 2025-12-04 | [#1064](https://github.com/harvard-edge/cs249r_book/pull/1064) | didier-durand | Other/root | [Doc] removing duplicate on Moore's Law in glossary |
| 2025-12-04 | [#1065](https://github.com/harvard-edge/cs249r_book/pull/1065) | didier-durand | Other/root | [Doc] Last 2 spotted typos in content |
| 2025-12-04 | [#1066](https://github.com/harvard-edge/cs249r_book/pull/1066) | didier-durand | Other/root | [Doc] typos in .py and CHANGELOG.md |
| 2025-12-05 | [#1067](https://github.com/harvard-edge/cs249r_book/pull/1067) | didier-durand | Other/root | [Doc] Hyperlink to non-existing file removed |
| 2025-12-05 | [#1068](https://github.com/harvard-edge/cs249r_book/pull/1068) | profvjreddi | TinyTorch | Repository Restructuring: Prepare for TinyTorch Integration |
| 2025-12-08 | [#1069](https://github.com/harvard-edge/cs249r_book/pull/1069) | didier-durand | Other/root | [Workflows] Prevent job runs on forks |
| 2025-12-08 | [#1071](https://github.com/harvard-edge/cs249r_book/pull/1071) | didier-durand | Other/root | [Doc]: what is Azure Inferentia ?  |
| 2025-12-30 | [#1084](https://github.com/harvard-edge/cs249r_book/pull/1084) | didier-durand | TinyTorch | [Doc]: code error in Tinytorch ? |
| 2025-12-30 | [#1086](https://github.com/harvard-edge/cs249r_book/pull/1086) | KarthikDani | Other/root | Fix: Update installer to require Python 3.10+ and detect Homebrew version |

</details>

<details>
<summary><strong>2026</strong>, 668 merged PRs</summary>

| Date | PR | Author | Category | Title |
|---|---|---|---|---|
| 2026-01-02 | [#1092](https://github.com/harvard-edge/cs249r_book/pull/1092) | oamazonasgabriel | Other/root | docs(data-engineering): improve cost effectiveness integration in scalability pillar |
| 2026-01-02 | [#1094](https://github.com/harvard-edge/cs249r_book/pull/1094) | RinZ27 | Other/root | Security: mitigate shell injection in build-baremetal |
| 2026-01-02 | [#1097](https://github.com/harvard-edge/cs249r_book/pull/1097) | ParampreetSingh23 | Other/root | Fix navbar overflow by adjusting collapse breakpoint |
| 2026-01-03 | [#1099](https://github.com/harvard-edge/cs249r_book/pull/1099) | snuggs | Other/root | Update Broken PDF Link in README.md #1100 |
| 2026-01-07 | [#1102](https://github.com/harvard-edge/cs249r_book/pull/1102) | XaicuL | Other/root | Add README Korean, Chinese, and Japanese  |
| 2026-01-19 | [#1113](https://github.com/harvard-edge/cs249r_book/pull/1113) | minhdang26403 | Other/root | fix: correct RandomHorizontalFlip axis for HWC inputs |
| 2026-01-19 | [#1114](https://github.com/harvard-edge/cs249r_book/pull/1114) | minhdang26403 | Other/root | fix: initialize parameter's gradient after creating Optimizer object |
| 2026-01-19 | [#1115](https://github.com/harvard-edge/cs249r_book/pull/1115) | minhdang26403 | Other/root | fix: miscellaneous fix for Tokenizer |
| 2026-01-19 | [#1117](https://github.com/harvard-edge/cs249r_book/pull/1117) | minhdang26403 | Other/root | fix: fix module import in Transformers module test |
| 2026-01-20 | [#1118](https://github.com/harvard-edge/cs249r_book/pull/1118) | minhdang26403 | Other/root | fix: fix memory calculation result |
| 2026-01-20 | [#1120](https://github.com/harvard-edge/cs249r_book/pull/1120) | minhdang26403 | Other/root | fix: matmul should not allow 0D tensors |
| 2026-01-20 | [#1123](https://github.com/harvard-edge/cs249r_book/pull/1123) | profvjreddi | Other/root | Remove unused next_functions from Function class |
| 2026-01-22 | [#1125](https://github.com/harvard-edge/cs249r_book/pull/1125) | profvjreddi | Other/root | Add per-project All Contributors setup |
| 2026-01-23 | [#1132](https://github.com/harvard-edge/cs249r_book/pull/1132) | BunningsWarehouseOfficial | Book | fix: broken chapter links in README |
| 2026-01-23 | [#1133](https://github.com/harvard-edge/cs249r_book/pull/1133) | didier-durand | Other/root | [Doc]: fixing some typos |
| 2026-01-25 | [#1136](https://github.com/harvard-edge/cs249r_book/pull/1136) | profvjreddi | CI/Infra | Fix optimizer gradient bug and CI improvements |
| 2026-01-26 | [#1139](https://github.com/harvard-edge/cs249r_book/pull/1139) | minhdang26403 | Other/root | fix: fix typo and answer render error in Activations module |
| 2026-01-26 | [#1140](https://github.com/harvard-edge/cs249r_book/pull/1140) | minhdang26403 | Other/root | fix: no need to clip input value of Sigmoid |
| 2026-01-26 | [#1141](https://github.com/harvard-edge/cs249r_book/pull/1141) | minhdang26403 | Other/root | fix: fix Softmax's forward pass implementation |
| 2026-02-01 | [#1149](https://github.com/harvard-edge/cs249r_book/pull/1149) | AndreaMattiaGaravagno | Slides | docs(slides): specify genai usage |
| 2026-02-04 | [#1151](https://github.com/harvard-edge/cs249r_book/pull/1151) | AndreaMattiaGaravagno | Other/root | feat: add step-by-step visualization to milestones |
| 2026-02-04 | [#1152](https://github.com/harvard-edge/cs249r_book/pull/1152) | AndreaMattiaGaravagno | TinyTorch | fix(tito-milestone): align bold cyan frame |
| 2026-02-04 | [#1153](https://github.com/harvard-edge/cs249r_book/pull/1153) | profvjreddi | CI/Infra | fix(ci): auto-label permissions for fork PRs |
| 2026-02-04 | [#1155](https://github.com/harvard-edge/cs249r_book/pull/1155) | profvjreddi | Other/root | fix(attention): correct complexity explanation and memory table bug |
| 2026-02-04 | [#1156](https://github.com/harvard-edge/cs249r_book/pull/1156) | profvjreddi | Other/root | fix(activations): correct GELU hint about 1.702 constant |
| 2026-02-04 | [#1157](https://github.com/harvard-edge/cs249r_book/pull/1157) | profvjreddi | Other/root | docs(activations): expand GELU explanation with both approximation forms |
| 2026-02-04 | [#1158](https://github.com/harvard-edge/cs249r_book/pull/1158) | profvjreddi | CI/Infra | fix(ci): handle branch names with slashes in fresh install test |
| 2026-02-04 | [#1159](https://github.com/harvard-edge/cs249r_book/pull/1159) | profvjreddi | CI/Infra | Merge dev → main: CI fixes, attention/GELU corrections, contributor updates |
| 2026-02-06 | [#1163](https://github.com/harvard-edge/cs249r_book/pull/1163) | minhdang26403 | Other/root | fix small typo |
| 2026-02-13 | [#1169](https://github.com/harvard-edge/cs249r_book/pull/1169) | adil-mubashir-ch | Other/root | Fix Windows install issues |
| 2026-02-13 | [#1170](https://github.com/harvard-edge/cs249r_book/pull/1170) | BunningsWarehouseOfficial | SocratiQ | Fix typo in SocratiQ introduction |
| 2026-02-13 | [#1171](https://github.com/harvard-edge/cs249r_book/pull/1171) | profvjreddi | TinyTorch | TinyTorch: progressive disclosure + Windows install cleanup |
| 2026-02-14 | [#1172](https://github.com/harvard-edge/cs249r_book/pull/1172) | kai4avaya | Other/root | fixed google auth allow iframe, slow index.html |
| 2026-02-19 | [#1181](https://github.com/harvard-edge/cs249r_book/pull/1181) | pipme | Other/root | Fix PDF download link in README.md |
| 2026-02-19 | [#1183](https://github.com/harvard-edge/cs249r_book/pull/1183) | Pratham-ja | Other/root | Improve activation graph visualization in Module 02 |
| 2026-02-21 | [#1190](https://github.com/harvard-edge/cs249r_book/pull/1190) | hzeljko | Book | Update training chapter and add missing color definition |
| 2026-02-21 | [#1194](https://github.com/harvard-edge/cs249r_book/pull/1194) | hzeljko | Book | Update figure in chapter 5 |
| 2026-02-22 | [#1179](https://github.com/harvard-edge/cs249r_book/pull/1179) | salmanmkc | Other/root | Upgrade GitHub Actions to latest versions |
| 2026-02-22 | [#1182](https://github.com/harvard-edge/cs249r_book/pull/1182) | RinZ27 | Other/root | Improve dataset extraction robustness |
| 2026-02-22 | [#1197](https://github.com/harvard-edge/cs249r_book/pull/1197) | hzeljko | Book | Updated figures in chapter 10: model_compression |
| 2026-02-24 | [#1200](https://github.com/harvard-edge/cs249r_book/pull/1200) | hzeljko | Book | Updated figures in chapter 13 |
| 2026-02-25 | [#1202](https://github.com/harvard-edge/cs249r_book/pull/1202) | adityamulik | Other/root | Fix incorrect matrix multiplication computation in notebook example |
| 2026-02-27 | [#1206](https://github.com/harvard-edge/cs249r_book/pull/1206) | kai4avaya | Other/root | updated calendar to pull from real cal, account deletions enabled |
| 2026-02-27 | [#1207](https://github.com/harvard-edge/cs249r_book/pull/1207) | kai4avaya | Other/root | bug fix user manual account broken |
| 2026-03-01 | [#1178](https://github.com/harvard-edge/cs249r_book/pull/1178) | salmanmkc | Other/root | Upgrade GitHub Actions for Node 24 compatibility |
| 2026-03-01 | [#1208](https://github.com/harvard-edge/cs249r_book/pull/1208) | harishb00 | Other/root | Fixed typo in GitHub user links and avatars in README |
| 2026-03-04 | [#1210](https://github.com/harvard-edge/cs249r_book/pull/1210) | Roldao-Neto | Other/root | fix: update requires-python to >=3.9 to run with uv |
| 2026-03-04 | [#1211](https://github.com/harvard-edge/cs249r_book/pull/1211) | Roldao-Neto | TinyTorch | Moved pytest from optional to required dependencies in tinytorch/pyproject.toml |
| 2026-03-11 | [#1227](https://github.com/harvard-edge/cs249r_book/pull/1227) | hzeljko | Book | Updated TikZ figres in chapter 2 |
| 2026-03-15 | [#1216](https://github.com/harvard-edge/cs249r_book/pull/1216) | minhdang26403 | Other/root | fix(losses): clean up log_softmax implementation |
| 2026-03-15 | [#1218](https://github.com/harvard-edge/cs249r_book/pull/1218) | adityamulik | Other/root | fix: handle null synced_modules in sync progress response |
| 2026-03-15 | [#1237](https://github.com/harvard-edge/cs249r_book/pull/1237) | hzeljko | Book | Update Matplotlib in vol2/ch4 |
| 2026-03-15 | [#1238](https://github.com/harvard-edge/cs249r_book/pull/1238) | hzeljko | Book | Update Matplotlib in vol2/ch5 |
| 2026-03-16 | [#1243](https://github.com/harvard-edge/cs249r_book/pull/1243) | profvjreddi | Other/root | fix: deterministic file-path rules for PR area labels |
| 2026-03-17 | [#1195](https://github.com/harvard-edge/cs249r_book/pull/1195) | harishb00 | Book | feature/typehints - chapter 1 and 2 |
| 2026-03-17 | [#1217](https://github.com/harvard-edge/cs249r_book/pull/1217) | minhdang26403 | Other/root | Add the hyperlink to the blog post name |
| 2026-03-17 | [#1224](https://github.com/harvard-edge/cs249r_book/pull/1224) | Tess314 | Book | Update model_compression.qmd |
| 2026-03-18 | [#1215](https://github.com/harvard-edge/cs249r_book/pull/1215) | yarikoptic | Other/root | Centralize codespell config and add 1 more fix |
| 2026-03-18 | [#1246](https://github.com/harvard-edge/cs249r_book/pull/1246) | hzeljko | Book | Update Matplotlib graphs in vol2/ch9 |
| 2026-03-18 | [#1247](https://github.com/harvard-edge/cs249r_book/pull/1247) | hzeljko | Book | Update Matplotlib graphs in vol2/ch10 (inference.qmd) |
| 2026-03-18 | [#1248](https://github.com/harvard-edge/cs249r_book/pull/1248) | hzeljko | Book | Update Matplotlib graphs in vol2/ch11 (edge_intelligence.qmd) |
| 2026-03-18 | [#1249](https://github.com/harvard-edge/cs249r_book/pull/1249) | hzeljko | Book | Update Matplotlib graphs in vol2/ch12 (ops_scale.qmd) |
| 2026-03-20 | [#1192](https://github.com/harvard-edge/cs249r_book/pull/1192) | imuday984 | Other/root | feat: add type hints to modules 03, 04, and 05 (#1167) |
| 2026-03-20 | [#1254](https://github.com/harvard-edge/cs249r_book/pull/1254) | Tess314 | Other/root | Update .all-contributorsrc |
| 2026-03-20 | [#1262](https://github.com/harvard-edge/cs249r_book/pull/1262) | asgalon | Other/root | Fix #1221 suppress expected overflow warning in test |
| 2026-03-20 | [#1263](https://github.com/harvard-edge/cs249r_book/pull/1263) | profvjreddi | Other/root | Revert: add type hints to modules 03, 04, and 05 |
| 2026-03-21 | [#1239](https://github.com/harvard-edge/cs249r_book/pull/1239) | octo-patch | Other/root | feat: add cloud LLM provider support for caption generation |
| 2026-03-21 | [#1250](https://github.com/harvard-edge/cs249r_book/pull/1250) | hzeljko | Book | Update a Matplotlip graph in vol2/ch13 (security_privacy.qmd) |
| 2026-03-21 | [#1251](https://github.com/harvard-edge/cs249r_book/pull/1251) | hzeljko | Book | Update Matplotlib graphs in vol2/ch14 (robust_ai.qmd) |
| 2026-03-21 | [#1253](https://github.com/harvard-edge/cs249r_book/pull/1253) | hzeljko | Book | Update Matplotlib graphs in vol2/ch15 (sustainable_ai.qmd) |
| 2026-03-21 | [#1259](https://github.com/harvard-edge/cs249r_book/pull/1259) | hzeljko | Book | Update a Matplotlib graph in vol2/ch16 (responsible_ai.qmd) |
| 2026-03-21 | [#1260](https://github.com/harvard-edge/cs249r_book/pull/1260) | hzeljko | Book | Update Matplotlib graphs in vol1/ch1 (introduction.qmd) |
| 2026-03-21 | [#1261](https://github.com/harvard-edge/cs249r_book/pull/1261) | hzeljko | Book | Update Matplotlib in vol1/ch2 (ml_systems.qmd) |
| 2026-03-21 | [#1264](https://github.com/harvard-edge/cs249r_book/pull/1264) | hzeljko | Book | Update a Matplotlib graph in vol1/ch3 (ml_workflow.qmd) |
| 2026-03-21 | [#1265](https://github.com/harvard-edge/cs249r_book/pull/1265) | hzeljko | Book | Update a Matplotlib graph in vol1/ch4 (data_engineering.qmd) |
| 2026-03-21 | [#1266](https://github.com/harvard-edge/cs249r_book/pull/1266) | hzeljko | Book | Update Matplotlib graphs in vol1/ch6 (nn_architectures.qmd) |
| 2026-03-21 | [#1267](https://github.com/harvard-edge/cs249r_book/pull/1267) | oamazonasgabriel | Other/root | fix(contributors): update Gabriel Amazonas GitHub username references |
| 2026-03-22 | [#1269](https://github.com/harvard-edge/cs249r_book/pull/1269) | hzeljko | Book | Update Matplotlib graphs in vol1/ch7 (frameworks.qmd) |
| 2026-03-22 | [#1270](https://github.com/harvard-edge/cs249r_book/pull/1270) | hzeljko | Book | Update Matplotlib graphs and Tikz figures in vol1/ch8 (training.qmd) |
| 2026-03-22 | [#1271](https://github.com/harvard-edge/cs249r_book/pull/1271) | asgalon | Other/root | Fix #1268 installscript fails on ubuntu LTS 20.04 |
| 2026-03-23 | [#1273](https://github.com/harvard-edge/cs249r_book/pull/1273) | hzeljko | Book | Update Matplotlib graphs in vol1/ch9 (data_selection.qmd) |
| 2026-03-23 | [#1274](https://github.com/harvard-edge/cs249r_book/pull/1274) | hzeljko | Book | Update a Matplotlib graph in vol1/ch10 (model_compression.qmd) |
| 2026-03-23 | [#1277](https://github.com/harvard-edge/cs249r_book/pull/1277) | hzeljko | Book | Update Matplotlib graphs in vol1/ch11 (hw_acceleration.qmd) |
| 2026-03-24 | [#1279](https://github.com/harvard-edge/cs249r_book/pull/1279) | asgalon | Other/root | Fix #1256 refactor Token Constants, test vocab init/build behaviour |
| 2026-03-24 | [#1281](https://github.com/harvard-edge/cs249r_book/pull/1281) | hzeljko | Book | Update Matplotlib graphs in vol1/ch3 (model_serving.qmd) |
| 2026-03-25 | [#1282](https://github.com/harvard-edge/cs249r_book/pull/1282) | hzeljko | Book | Update Matplotlib graphs in vol1/ch14 (ml_ops.qmd) |
| 2026-03-25 | [#1283](https://github.com/harvard-edge/cs249r_book/pull/1283) | hzeljko | Book | Updated a Matplotlib graph in vol1/ch15 (responsible_engr.qmd) |
| 2026-04-02 | [#1286](https://github.com/harvard-edge/cs249r_book/pull/1286) | hzeljko | Book | Update a Tikz figure in vol1/ch1 (introduction.qmd) |
| 2026-04-02 | [#1287](https://github.com/harvard-edge/cs249r_book/pull/1287) | hzeljko | Book | Update TikZ figures in vol1/ch3 (ml_workflow.qmd) |
| 2026-04-02 | [#1288](https://github.com/harvard-edge/cs249r_book/pull/1288) | hzeljko | Book | Update TikZ figures in vol1/ch4 (data_engineering.qmd) |
| 2026-04-02 | [#1289](https://github.com/harvard-edge/cs249r_book/pull/1289) | hzeljko | Book | Update TikZ figures in vol1/ch5 (nn_computation.qmd) |
| 2026-04-02 | [#1290](https://github.com/harvard-edge/cs249r_book/pull/1290) | hzeljko | Book | Update TikZ figure in vol1/ch6 (nn_architectures.qmd) |
| 2026-04-07 | [#1293](https://github.com/harvard-edge/cs249r_book/pull/1293) | dependabot | StaffML | chore(deps): bump vite from 8.0.3 to 8.0.5 in /interviews/staffml |
| 2026-04-09 | [#1294](https://github.com/harvard-edge/cs249r_book/pull/1294) | farhan523 | TinyTorch | fix(tinytorch): mobile responsiveness for intro page grids |
| 2026-04-09 | [#1295](https://github.com/harvard-edge/cs249r_book/pull/1295) | dependabot | Dependencies | chore(deps): bump basic-ftp from 5.2.0 to 5.2.1 |
| 2026-04-09 | [#1296](https://github.com/harvard-edge/cs249r_book/pull/1296) | dependabot | StaffML | chore(deps): bump undici and wrangler in /interviews/staffml/worker |
| 2026-04-09 | [#1297](https://github.com/harvard-edge/cs249r_book/pull/1297) | dependabot | StaffML | chore(deps): bump esbuild and wrangler in /interviews/staffml/worker |
| 2026-04-09 | [#1301](https://github.com/harvard-edge/cs249r_book/pull/1301) | profvjreddi | MLSys·im | fix(mlsysim): restore solver.py and formulas.py lost in merge regression |
| 2026-04-09 | [#1302](https://github.com/harvard-edge/cs249r_book/pull/1302) | profvjreddi | MLSys·im | fix(mlsysim): align docs with *Model naming convention |
| 2026-04-09 | [#1303](https://github.com/harvard-edge/cs249r_book/pull/1303) | profvjreddi | MLSys·im | fix(mlsysim): update distributed tutorial to attribute access API |
| 2026-04-09 | [#1304](https://github.com/harvard-edge/cs249r_book/pull/1304) | profvjreddi | MLSys·im | fix(mlsysim): convert all dict accesses in distributed tutorial |
| 2026-04-09 | [#1307](https://github.com/harvard-edge/cs249r_book/pull/1307) | profvjreddi | StaffML | feat(staffml): group sidebar questions by track + floating card |
| 2026-04-09 | [#1308](https://github.com/harvard-edge/cs249r_book/pull/1308) | profvjreddi | Other/root | Merge periodic-table paper polish (passes 2-16) into dev |
| 2026-04-09 | [#1309](https://github.com/harvard-edge/cs249r_book/pull/1309) | profvjreddi | StaffML | fix(staffml): position sidebar below header |
| 2026-04-09 | [#1310](https://github.com/harvard-edge/cs249r_book/pull/1310) | profvjreddi | Other/root | fix: restore periodic-table paper polish lost in merge |
| 2026-04-10 | [#1312](https://github.com/harvard-edge/cs249r_book/pull/1312) | profvjreddi | MLSys·im | fix(mlsysim): address reviewer feedback + improve landing page |
| 2026-04-12 | [#1313](https://github.com/harvard-edge/cs249r_book/pull/1313) | farhan523 | TinyTorch | fix(tinytorch): typo in big-picture diagram + preface mobile responsiveness |
| 2026-04-12 | [#1314](https://github.com/harvard-edge/cs249r_book/pull/1314) | farhan523 | TinyTorch | fix(tinytorch): typo "Tokentic" → "Tokenization" in site-quarto diagram |
| 2026-04-12 | [#1315](https://github.com/harvard-edge/cs249r_book/pull/1315) | farhan523 | TinyTorch | fix(tinytorch): rename misspelled "commununity" image file |
| 2026-04-12 | [#1316](https://github.com/harvard-edge/cs249r_book/pull/1316) | farhan523 | TinyTorch | fix(tinytorch): remove extra space in "Available Now" heading |
| 2026-04-12 | [#1319](https://github.com/harvard-edge/cs249r_book/pull/1319) | dependabot | Dependencies | chore(deps): bump basic-ftp from 5.2.1 to 5.2.2 |
| 2026-04-12 | [#1321](https://github.com/harvard-edge/cs249r_book/pull/1321) | Shashank-Tripathi-07 | MLSys·im | fix: export DecisionLog and FailureBanner from mlsysim.labs |
| 2026-04-12 | [#1323](https://github.com/harvard-edge/cs249r_book/pull/1323) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): correct export path mismatch for modules 09 and 13 |
| 2026-04-12 | [#1324](https://github.com/harvard-edge/cs249r_book/pull/1324) | dependabot | StaffML | chore(deps): bump next from 15.5.14 to 15.5.15 in /interviews/staffml |
| 2026-04-13 | [#1325](https://github.com/harvard-edge/cs249r_book/pull/1325) | farhan523 | TinyTorch | fix(tinytorch): fix dark mode visibility on preface page |
| 2026-04-13 | [#1326](https://github.com/harvard-edge/cs249r_book/pull/1326) | farhan523 | TinyTorch | fix(tinytorch): fix dark mode visibility on big-picture page |
| 2026-04-16 | [#1306](https://github.com/harvard-edge/cs249r_book/pull/1306) | asgalon | Labs | #1305 [Bug] Labs lab00 cleanup, run 1 |
| 2026-04-16 | [#1329](https://github.com/harvard-edge/cs249r_book/pull/1329) | farhan523 | TinyTorch | fix(tinytorch): fix role-cards layout overflow and mobile collapse on overview page |
| 2026-04-16 | [#1330](https://github.com/harvard-edge/cs249r_book/pull/1330) | farhan523 | TinyTorch | fix(tinytorch): fix dark mode visibility on overview page role cards and session flow |
| 2026-04-16 | [#1335](https://github.com/harvard-edge/cs249r_book/pull/1335) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): ensure model params have requires_grad=True in trainer_init |
| 2026-04-16 | [#1336](https://github.com/harvard-edge/cs249r_book/pull/1336) | Shashank-Tripathi-07 | TinyTorch | test(tinytorch): add finite-difference gradient correctness tests for Module 06 |
| 2026-04-16 | [#1337](https://github.com/harvard-edge/cs249r_book/pull/1337) | Shashank-Tripathi-07 | TinyTorch | test(tinytorch): add coverage tests for Module 08 training infrastructure |
| 2026-04-16 | [#1338](https://github.com/harvard-edge/cs249r_book/pull/1338) | Shashank-Tripathi-07 | Other/root | fix(tests/10_tokenization): replace raw numpy array params with Tensor in DummyModel |
| 2026-04-16 | [#1339](https://github.com/harvard-edge/cs249r_book/pull/1339) | Shashank-Tripathi-07 | Labs | fix(labs/lab01): unblock prediction widget chain |
| 2026-04-16 | [#1340](https://github.com/harvard-edge/cs249r_book/pull/1340) | profvjreddi | Other/root | fix(tests): drop stray gradient test file from #1338 |
| 2026-04-16 | [#1343](https://github.com/harvard-edge/cs249r_book/pull/1343) | profvjreddi | TinyTorch | fix(tinytorch): wire Tanh into enable_autograd() |
| 2026-04-16 | [#1344](https://github.com/harvard-edge/cs249r_book/pull/1344) | profvjreddi | CI/Infra | fix(ci): move path vars to workflow-level env so fork PRs can build |
| 2026-04-16 | [#1345](https://github.com/harvard-edge/cs249r_book/pull/1345) | profvjreddi | Labs | chore(labs): upgrade marimo pin to 0.23.1 and fix resulting lint issues |
| 2026-04-16 | [#1346](https://github.com/harvard-edge/cs249r_book/pull/1346) | profvjreddi | CI/Infra | feat(ci): tooling to catch fork-PR variable leaks and marimo dataflow bugs |
| 2026-04-16 | [#1348](https://github.com/harvard-edge/cs249r_book/pull/1348) | profvjreddi | StaffML | feat(vault): v0.9.0 — YAML source of truth + release pipeline + CC-BY-NC-4.0 corpus |
| 2026-04-16 | [#1350](https://github.com/harvard-edge/cs249r_book/pull/1350) | profvjreddi | Labs | fix(labs): relax widget-gated-cell check to catch only multi-widget leaks |
| 2026-04-16 | [#1351](https://github.com/harvard-edge/cs249r_book/pull/1351) | profvjreddi | Labs | refactor(labs/lab_06): migrate fault-tolerance lab to Pattern C (proof lab) |
| 2026-04-16 | [#1352](https://github.com/harvard-edge/cs249r_book/pull/1352) | profvjreddi | Labs | refactor(labs): migrate 30 labs to Pattern C (closes out #1347) |
| 2026-04-16 | [#1353](https://github.com/harvard-edge/cs249r_book/pull/1353) | profvjreddi | Labs | fix(labs/lab_05): import plotly.subplots AFTER micropip install |
| 2026-04-17 | [#1354](https://github.com/harvard-edge/cs249r_book/pull/1354) | dependabot | StaffML | chore(deps): bump vite and vitest in /interviews/staffml-vault-worker |
| 2026-04-17 | [#1356](https://github.com/harvard-edge/cs249r_book/pull/1356) | dependabot | StaffML | chore(deps): bump undici and wrangler in /interviews/staffml-vault-worker |
| 2026-04-17 | [#1357](https://github.com/harvard-edge/cs249r_book/pull/1357) | dependabot | Dependencies | chore(deps): bump basic-ftp from 5.2.2 to 5.3.0 |
| 2026-04-17 | [#1371](https://github.com/harvard-edge/cs249r_book/pull/1371) | profvjreddi | Labs | refactor(labs/lab_00): manual Pattern C migration (closes #1347) |
| 2026-04-17 | [#1373](https://github.com/harvard-edge/cs249r_book/pull/1373) | profvjreddi | CI/Infra | fix(ci): unblock book-validate by grandfathering 29 bib violations |
| 2026-04-17 | [#1374](https://github.com/harvard-edge/cs249r_book/pull/1374) | profvjreddi | Labs | feat(labs/ci): browser-level wasm smoke via playwright + coep/coop |
| 2026-04-17 | [#1375](https://github.com/harvard-edge/cs249r_book/pull/1375) | profvjreddi | CI/Infra | fix(ci): unblock book-validate pre-commit (YAML + codespell + manifest) |
| 2026-04-17 | [#1376](https://github.com/harvard-edge/cs249r_book/pull/1376) | profvjreddi | StaffML | fix(staffml): unblock staffml-preview-dev type check + build |
| 2026-04-17 | [#1379](https://github.com/harvard-edge/cs249r_book/pull/1379) | profvjreddi | Book | chore(format): apply bibtex-tidy + EOF-fixer residuals (unblock Book badge) |
| 2026-04-17 | [#1381](https://github.com/harvard-edge/cs249r_book/pull/1381) | profvjreddi | Labs | fix(labs/lab_00): suppress infeasible callout on exactly-right answer (#1305) |
| 2026-04-17 | [#1382](https://github.com/harvard-edge/cs249r_book/pull/1382) | profvjreddi | Other/root | chore(bib): verify 30 grandfathered paper-bib entries |
| 2026-04-17 | [#1384](https://github.com/harvard-edge/cs249r_book/pull/1384) | profvjreddi | StaffML | chore(ci): delete vault-content-hash-sli workflow (non-solution) |
| 2026-04-17 | [#1385](https://github.com/harvard-edge/cs249r_book/pull/1385) | profvjreddi | Labs | fix(labs/vol1): restore slider/dropdown dataflow in lab_02 + lab_03 (#1332) |
| 2026-04-17 | [#1386](https://github.com/harvard-edge/cs249r_book/pull/1386) | profvjreddi | Labs | fix(labs): widget return-tuple sweep across 33 labs + static regression guard (#1332) |
| 2026-04-17 | [#1387](https://github.com/harvard-edge/cs249r_book/pull/1387) | profvjreddi | Labs | fix(labs): lab_02 Part A scenario alignment + extract tabs-cell widgets (#1332) |
| 2026-04-17 | [#1391](https://github.com/harvard-edge/cs249r_book/pull/1391) | profvjreddi | TinyTorch | feat(tinytorch): add ndim, numel(), contiguous() to Tensor |
| 2026-04-18 | [#1397](https://github.com/harvard-edge/cs249r_book/pull/1397) | profvjreddi | MLSys·im | MLSys·im 0.1.0 release-prep audit |
| 2026-04-18 | [#1398](https://github.com/harvard-edge/cs249r_book/pull/1398) | profvjreddi | TinyTorch | Switch TinyTorch site to Quarto + fix docs |
| 2026-04-18 | [#1399](https://github.com/harvard-edge/cs249r_book/pull/1399) | profvjreddi | Other/root | fix: GELU gradient mismatch + float32 test precision |
| 2026-04-18 | [#1400](https://github.com/harvard-edge/cs249r_book/pull/1400) | profvjreddi | TinyTorch | fix: enable sidebar + fix raw markdown on TinyTorch landing page |
| 2026-04-18 | [#1401](https://github.com/harvard-edge/cs249r_book/pull/1401) | profvjreddi | Other/root | fix: announcement bar + sidebar consistency |
| 2026-04-18 | [#1402](https://github.com/harvard-edge/cs249r_book/pull/1402) | profvjreddi | Other/root | feat: add announcement bars to all remaining Quarto sites |
| 2026-04-18 | [#1403](https://github.com/harvard-edge/cs249r_book/pull/1403) | profvjreddi | MLSys·im | fix(shared): centralize font loading + fix mlsysim carousel |
| 2026-04-19 | [#1389](https://github.com/harvard-edge/cs249r_book/pull/1389) | Shashank-Tripathi-07 | Labs | fix(lab05): resolve silent WASM hang on Pyodide boot (#1388) |
| 2026-04-19 | [#1395](https://github.com/harvard-edge/cs249r_book/pull/1395) | octo-patch | Other/root | fix(epub): stop sidenote filter from injecting LaTeX into EPUB output |
| 2026-04-19 | [#1396](https://github.com/harvard-edge/cs249r_book/pull/1396) | farhan523 | TinyTorch | fix(tinytorch): fix dark mode visibility across all cards on modules page |
| 2026-04-19 | [#1406](https://github.com/harvard-edge/cs249r_book/pull/1406) | profvjreddi | Other/root | PR-3: Scripts, audits, cleanup (build stamp, PDF dropdown, 404s, mirror guard, dedup, RELEASE-PREP) |
| 2026-04-19 | [#1407](https://github.com/harvard-edge/cs249r_book/pull/1407) | profvjreddi | TinyTorch | PR-4: TinyTorch release prep — MIT/CC-BY-NC-SA dual licensing + v0.10.0 + workflow explicit-version override |
| 2026-04-19 | [#1408](https://github.com/harvard-edge/cs249r_book/pull/1408) | profvjreddi | Book | PR-4b: Per-volume book versioning (vol1-v* / vol2-v* independent tag spaces) |
| 2026-04-19 | [#1409](https://github.com/harvard-edge/cs249r_book/pull/1409) | profvjreddi | Other/root | PR-5: Cutover skeletons (rollback-legacy + redirect map + sitemap aggregator) |
| 2026-04-19 | [#1410](https://github.com/harvard-edge/cs249r_book/pull/1410) | profvjreddi | Book | fix(book): route per-volume builds to per-volume index pages |
| 2026-04-20 | [#1392](https://github.com/harvard-edge/cs249r_book/pull/1392) | Shashank-Tripathi-07 | TinyTorch | test(tinytorch): PyTorch-compat test coverage for Tensor API additions |
| 2026-04-20 | [#1404](https://github.com/harvard-edge/cs249r_book/pull/1404) | profvjreddi | Other/root | PR-1: Release-prep safety net (link checking + publish guards + nightly link-rot) |
| 2026-04-20 | [#1405](https://github.com/harvard-edge/cs249r_book/pull/1405) | profvjreddi | Other/root | PR-2: Visual polish (announcement bars, theme persistence, dev-mirror fix, audit script) |
| 2026-04-20 | [#1411](https://github.com/harvard-edge/cs249r_book/pull/1411) | profvjreddi | Dependencies | fix(book-publish): bump version in BOTH per-volume index files |
| 2026-04-20 | [#1412](https://github.com/harvard-edge/cs249r_book/pull/1412) | profvjreddi | MLSys·im | fix(mlsysim): add missing contributors (VJ as #1, Zeljko Hrcek) |
| 2026-04-20 | [#1413](https://github.com/harvard-edge/cs249r_book/pull/1413) | profvjreddi | CI/Infra | fix(ci): clear all 8 failing pre-commit hooks on dev |
| 2026-04-20 | [#1414](https://github.com/harvard-edge/cs249r_book/pull/1414) | Shashank-Tripathi-07 | StaffML | fix(staffml/practice): apply effectiveMaxScore to score buttons and save handler |
| 2026-04-20 | [#1415](https://github.com/harvard-edge/cs249r_book/pull/1415) | Shashank-Tripathi-07 | StaffML | fix(staffml/gauntlet): add rubric checkboxes and fix silent fail on empty question pool |
| 2026-04-20 | [#1416](https://github.com/harvard-edge/cs249r_book/pull/1416) | farhan523 | TinyTorch | fix(tinytorch): fix dark mode visibility across cards on milestones page |
| 2026-04-20 | [#1418](https://github.com/harvard-edge/cs249r_book/pull/1418) | profvjreddi | TinyTorch | fix(publish): block silent version downgrades in tinytorch + book publish-live |
| 2026-04-20 | [#1419](https://github.com/harvard-edge/cs249r_book/pull/1419) | profvjreddi | MLSys·im | fix(tests): make mlsysim.core importable from CI without pip install |
| 2026-04-20 | [#1420](https://github.com/harvard-edge/cs249r_book/pull/1420) | profvjreddi | MLSys·im | fix(quarto): restore mlsysim importability after PR #1397 outer-init removal |
| 2026-04-20 | [#1422](https://github.com/harvard-edge/cs249r_book/pull/1422) | profvjreddi | Other/root | fix(styles): sync harvard theme mirror after $primary/$accent change |
| 2026-04-21 | [#1425](https://github.com/harvard-edge/cs249r_book/pull/1425) | hzeljko | Other/root | Improve sidenote offset handling and intro layout fixes |
| 2026-04-21 | [#1426](https://github.com/harvard-edge/cs249r_book/pull/1426) | profvjreddi | StaffML | refactor(vault): schema v1.0 — self-contained YAML, flat-by-track, single source of truth |
| 2026-04-21 | [#1427](https://github.com/harvard-edge/cs249r_book/pull/1427) | profvjreddi | StaffML | feat(vault): phase 2 — apply 813 LLM-reviewed zone/level reclassifications |
| 2026-04-21 | [#1428](https://github.com/harvard-edge/cs249r_book/pull/1428) | profvjreddi | StaffML | feat(vault): ID scheme v2 + vault ls/show/chain browse commands |
| 2026-04-22 | [#1429](https://github.com/harvard-edge/cs249r_book/pull/1429) | Shashank-Tripathi-07 | Other/root | fix(worker): move Gemini API key from URL query param to request header |
| 2026-04-22 | [#1430](https://github.com/harvard-edge/cs249r_book/pull/1430) | Shashank-Tripathi-07 | StaffML | fix(staffml): stale closure in gauntlet keyboard handler and importProgress partial-write |
| 2026-04-22 | [#1431](https://github.com/harvard-edge/cs249r_book/pull/1431) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): regression models silently report wrong accuracy in Trainer.evaluate |
| 2026-04-22 | [#1432](https://github.com/harvard-edge/cs249r_book/pull/1432) | hzeljko | Other/root | Update introduction and halftitle page |
| 2026-04-22 | [#1433](https://github.com/harvard-edge/cs249r_book/pull/1433) | profvjreddi | Other/root | feat(d1): cutover production D1 + live worker serving v1.0 schema |
| 2026-04-22 | [#1434](https://github.com/harvard-edge/cs249r_book/pull/1434) | profvjreddi | StaffML | fix(vault): make chains robust for public release |
| 2026-04-22 | [#1435](https://github.com/harvard-edge/cs249r_book/pull/1435) | profvjreddi | StaffML | feat(staffml): flip default data source to vault worker |
| 2026-04-22 | [#1436](https://github.com/harvard-edge/cs249r_book/pull/1436) | profvjreddi | Other/root | chore(paper): regenerate figures + stats + macros from v1.0 corpus |
| 2026-04-22 | [#1437](https://github.com/harvard-edge/cs249r_book/pull/1437) | profvjreddi | StaffML | feat(staffml): shrink bundle 79% via summary + worker hydration |
| 2026-04-22 | [#1438](https://github.com/harvard-edge/cs249r_book/pull/1438) | profvjreddi | CI/Infra | feat(ci): close the deploy loop — YAML change triggers full pipeline |
| 2026-04-22 | [#1439](https://github.com/harvard-edge/cs249r_book/pull/1439) | profvjreddi | StaffML | chore(ci): rename vault-ci.yml → staffml-validate-vault.yml |
| 2026-04-22 | [#1440](https://github.com/harvard-edge/cs249r_book/pull/1440) | profvjreddi | StaffML | fix(staffml): re-nest worker response — practice page white-screen |
| 2026-04-22 | [#1441](https://github.com/harvard-edge/cs249r_book/pull/1441) | profvjreddi | StaffML | fix(staffml): allow Cloudflare Web Analytics beacon in CSP |
| 2026-04-22 | [#1442](https://github.com/harvard-edge/cs249r_book/pull/1442) | profvjreddi | StaffML | feat(staffml): flip default theme to light for ecosystem consistency |
| 2026-04-22 | [#1443](https://github.com/harvard-edge/cs249r_book/pull/1443) | profvjreddi | StaffML | chore(staffml): release polish — drop hash pin, skeletons, error reporting |
| 2026-04-22 | [#1444](https://github.com/harvard-edge/cs249r_book/pull/1444) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): constant tensor silently zeroed after quantize/dequantize roundtrip |
| 2026-04-22 | [#1445](https://github.com/harvard-edge/cs249r_book/pull/1445) | profvjreddi | StaffML | fix(staffml): mobile tap targets on practice-page icon buttons |
| 2026-04-22 | [#1446](https://github.com/harvard-edge/cs249r_book/pull/1446) | profvjreddi | TinyTorch | fix(tinytorch/pdf): port to Quarto + fix mermaid rendering |
| 2026-04-22 | [#1447](https://github.com/harvard-edge/cs249r_book/pull/1447) | profvjreddi | StaffML | feat(ci): E2E smoke + gate publish on validate-vault |
| 2026-04-22 | [#1448](https://github.com/harvard-edge/cs249r_book/pull/1448) | profvjreddi | StaffML | fix(vault-cli): resolve ruff errors blocking validate-vault |
| 2026-04-22 | [#1449](https://github.com/harvard-edge/cs249r_book/pull/1449) | profvjreddi | StaffML | chore(vault): refresh exemplar-gaps.yaml after today's corpus shifts |
| 2026-04-22 | [#1450](https://github.com/harvard-edge/cs249r_book/pull/1450) | profvjreddi | StaffML | fix(staffml): wire ChainBadge click to a pre-reveal sibling strip |
| 2026-04-22 | [#1451](https://github.com/harvard-edge/cs249r_book/pull/1451) | profvjreddi | StaffML | staffml: chain CTR analytics + footer cleanup + ChainBadge polish |
| 2026-04-22 | [#1452](https://github.com/harvard-edge/cs249r_book/pull/1452) | profvjreddi | Other/root | Fix dark-mode shield + dedup About paper CTA |
| 2026-04-22 | [#1453](https://github.com/harvard-edge/cs249r_book/pull/1453) | dependabot | MLSys·im | deps(mlsysim): update pytest-cov requirement from >=4.0 to >=7.1.0 in /mlsysim |
| 2026-04-22 | [#1455](https://github.com/harvard-edge/cs249r_book/pull/1455) | dependabot | MLSys·im | deps(mlsysim): update ortools requirement from >=9.6 to >=9.15.6755 in /mlsysim |
| 2026-04-22 | [#1456](https://github.com/harvard-edge/cs249r_book/pull/1456) | dependabot | TinyTorch | deps(tinytorch): update jupyter requirement from >=1.1.0 to >=1.1.1 in /tinytorch |
| 2026-04-22 | [#1457](https://github.com/harvard-edge/cs249r_book/pull/1457) | dependabot | MLSys·im | deps(mlsysim): update jupyter requirement from >=1.0 to >=1.1.1 in /mlsysim |
| 2026-04-22 | [#1458](https://github.com/harvard-edge/cs249r_book/pull/1458) | dependabot | Dependencies | deps(book): update lxml requirement from >=5.0.0 to >=6.1.0 |
| 2026-04-22 | [#1459](https://github.com/harvard-edge/cs249r_book/pull/1459) | dependabot | StaffML | deps(vault-worker): bump wrangler from 4.83.0 to 4.84.1 in /interviews/staffml-vault-worker |
| 2026-04-22 | [#1461](https://github.com/harvard-edge/cs249r_book/pull/1461) | dependabot | StaffML | deps(staffml-worker): bump typescript from 5.9.3 to 6.0.3 in /interviews/staffml/worker |
| 2026-04-22 | [#1462](https://github.com/harvard-edge/cs249r_book/pull/1462) | dependabot | TinyTorch | deps(tinytorch): update numpy requirement from <3.0.0,>=1.24.0 to >=2.2.6,<3.0.0 in /tinytorch |
| 2026-04-22 | [#1463](https://github.com/harvard-edge/cs249r_book/pull/1463) | dependabot | MLSys·im | deps(mlsysim): update matplotlib requirement from >=3.5.0 to >=3.10.8 in /mlsysim |
| 2026-04-22 | [#1464](https://github.com/harvard-edge/cs249r_book/pull/1464) | dependabot | Labs | deps(labs-ext): bump @types/node from 20.19.37 to 25.6.0 in /labs/vscode-ext |
| 2026-04-22 | [#1465](https://github.com/harvard-edge/cs249r_book/pull/1465) | dependabot | TinyTorch | deps(tinytorch): update jupytext requirement from >=1.16.0 to >=1.19.1 in /tinytorch |
| 2026-04-22 | [#1466](https://github.com/harvard-edge/cs249r_book/pull/1466) | dependabot | CI/Infra | ci(deps): bump actions/setup-python from 5 to 6 |
| 2026-04-22 | [#1468](https://github.com/harvard-edge/cs249r_book/pull/1468) | dependabot | Labs | deps(labs-ext): bump @types/vscode from 1.110.0 to 1.116.0 in /labs/vscode-ext |
| 2026-04-22 | [#1469](https://github.com/harvard-edge/cs249r_book/pull/1469) | dependabot | Dependencies | deps(book): update scipy requirement from >=1.11.0 to >=1.13.1 |
| 2026-04-22 | [#1470](https://github.com/harvard-edge/cs249r_book/pull/1470) | dependabot | CI/Infra | ci(deps): bump actions/setup-java from 4 to 5 |
| 2026-04-22 | [#1471](https://github.com/harvard-edge/cs249r_book/pull/1471) | dependabot | TinyTorch | deps(tinytorch): update pytest requirement from >=8.0.0 to >=9.0.3 in /tinytorch |
| 2026-04-22 | [#1472](https://github.com/harvard-edge/cs249r_book/pull/1472) | dependabot | StaffML | deps(vault-worker): bump vitest from 4.1.4 to 4.1.5 in /interviews/staffml-vault-worker |
| 2026-04-22 | [#1473](https://github.com/harvard-edge/cs249r_book/pull/1473) | dependabot | CI/Infra | ci(deps): bump lycheeverse/lychee-action from 2.7.0 to 2.8.0 |
| 2026-04-22 | [#1474](https://github.com/harvard-edge/cs249r_book/pull/1474) | dependabot | StaffML | deps(staffml-worker): bump wrangler from 4.81.0 to 4.84.1 in /interviews/staffml/worker |
| 2026-04-22 | [#1476](https://github.com/harvard-edge/cs249r_book/pull/1476) | dependabot | Dependencies | deps(book): update jupytext requirement from >=1.16.0 to >=1.19.1 |
| 2026-04-22 | [#1478](https://github.com/harvard-edge/cs249r_book/pull/1478) | dependabot | CI/Infra | ci(deps): bump softprops/action-gh-release from 2 to 3 |
| 2026-04-22 | [#1480](https://github.com/harvard-edge/cs249r_book/pull/1480) | dependabot | TinyTorch | deps(tinytorch-ext): bump @types/vscode from 1.109.0 to 1.116.0 in /tinytorch/vscode-ext |
| 2026-04-22 | [#1481](https://github.com/harvard-edge/cs249r_book/pull/1481) | dependabot | StaffML | deps(vault-worker): bump typescript from 5.9.3 to 6.0.3 in /interviews/staffml-vault-worker |
| 2026-04-22 | [#1482](https://github.com/harvard-edge/cs249r_book/pull/1482) | dependabot | StaffML | deps(staffml-worker): bump @cloudflare/workers-types from 4.20260405.1 to 4.20260422.1 in /interviews/staffml/worker |
| 2026-04-22 | [#1483](https://github.com/harvard-edge/cs249r_book/pull/1483) | dependabot | Kits | deps(kits-ext): bump @types/vscode from 1.110.0 to 1.116.0 in /kits/vscode-ext |
| 2026-04-22 | [#1484](https://github.com/harvard-edge/cs249r_book/pull/1484) | dependabot | Kits | deps(kits-ext): bump @types/node from 20.19.37 to 25.6.0 in /kits/vscode-ext |
| 2026-04-22 | [#1485](https://github.com/harvard-edge/cs249r_book/pull/1485) | dependabot | Dependencies | deps(book): update ipykernel requirement from >=6.29.0 to >=6.31.0 |
| 2026-04-22 | [#1486](https://github.com/harvard-edge/cs249r_book/pull/1486) | dependabot | Dependencies | deps(book): update plotly requirement from >=5.0.0 to >=6.7.0 |
| 2026-04-22 | [#1487](https://github.com/harvard-edge/cs249r_book/pull/1487) | dependabot | StaffML | deps(staffml): bump the next-react group across 1 directory with 3 updates |
| 2026-04-22 | [#1488](https://github.com/harvard-edge/cs249r_book/pull/1488) | dependabot | CI/Infra | ci(deps): bump actions/checkout from 4 to 6 |
| 2026-04-22 | [#1489](https://github.com/harvard-edge/cs249r_book/pull/1489) | dependabot | StaffML | deps(staffml): bump @types/node from 20.19.37 to 25.6.0 in /interviews/staffml |
| 2026-04-22 | [#1492](https://github.com/harvard-edge/cs249r_book/pull/1492) | dependabot | StaffML | deps(staffml): bump framer-motion from 11.18.2 to 12.38.0 in /interviews/staffml |
| 2026-04-22 | [#1493](https://github.com/harvard-edge/cs249r_book/pull/1493) | dependabot | Dependencies | deps(book-ext): bump @types/vscode from 1.108.1 to 1.116.0 in /book/vscode-ext |
| 2026-04-22 | [#1495](https://github.com/harvard-edge/cs249r_book/pull/1495) | dependabot | MLSys·im | deps(mlsysim-ext): bump @types/vscode from 1.110.0 to 1.116.0 in /mlsysim/vscode-ext |
| 2026-04-22 | [#1496](https://github.com/harvard-edge/cs249r_book/pull/1496) | dependabot | StaffML | deps(staffml): bump vitest from 4.1.2 to 4.1.5 in /interviews/staffml |
| 2026-04-22 | [#1498](https://github.com/harvard-edge/cs249r_book/pull/1498) | dependabot | StaffML | deps(staffml): bump jsdom from 29.0.1 to 29.0.2 in /interviews/staffml |
| 2026-04-22 | [#1499](https://github.com/harvard-edge/cs249r_book/pull/1499) | profvjreddi | Dependencies | deps: bump TypeScript 5→6, @types/node 20→25, ipykernel 6→7 |
| 2026-04-22 | [#1500](https://github.com/harvard-edge/cs249r_book/pull/1500) | profvjreddi | Book | fix(book/vol2): escape LaTeX underscore bug in inference index entries |
| 2026-04-23 | [#1501](https://github.com/harvard-edge/cs249r_book/pull/1501) | profvjreddi | Book | fix(book/vol2): escape LaTeX ampersand bug in scaling laws index entry |
| 2026-04-23 | [#1505](https://github.com/harvard-edge/cs249r_book/pull/1505) | profvjreddi | Other/root | docs(announcements): unify announcement bars across nine Quarto sites |
| 2026-04-24 | [#1394](https://github.com/harvard-edge/cs249r_book/pull/1394) | kai4avaya | SocratiQ | feat: add socratiq directory (excluding node_modules and dist) |
| 2026-04-24 | [#1504](https://github.com/harvard-edge/cs249r_book/pull/1504) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): LayerNorm gamma/beta missing requires_grad=True |
| 2026-04-24 | [#1506](https://github.com/harvard-edge/cs249r_book/pull/1506) | profvjreddi | StaffML | feat(staffml): announcement bar matching Quarto's identical structure + behavior |
| 2026-04-24 | [#1507](https://github.com/harvard-edge/cs249r_book/pull/1507) | octo-patch | Labs | fix(lab04): remove answer spoilers from prediction prompts in lab_04_data_engr |
| 2026-04-24 | [#1509](https://github.com/harvard-edge/cs249r_book/pull/1509) | profvjreddi | StaffML | fix(staffml/announcement): top-align megaphone icon + gate dismiss on live |
| 2026-04-24 | [#1510](https://github.com/harvard-edge/cs249r_book/pull/1510) | hzeljko | Book | Refine Chapter 2 PDF layout |
| 2026-04-24 | [#1511](https://github.com/harvard-edge/cs249r_book/pull/1511) | hzeljko | Book | Refine Chapter 3 PDF layout |
| 2026-04-24 | [#1512](https://github.com/harvard-edge/cs249r_book/pull/1512) | profvjreddi | TinyTorch | fix(tinytorch/announcement): restore unified ecosystem template |
| 2026-04-24 | [#1513](https://github.com/harvard-edge/cs249r_book/pull/1513) | profvjreddi | Book | Batch merge: PRs #1504, #1507, #1510, #1511 + chapter-opener font |
| 2026-04-24 | [#1514](https://github.com/harvard-edge/cs249r_book/pull/1514) | profvjreddi | Other/root | fix(shared-sidebar): tighten vertical spacing for uniform Quarto look |
| 2026-04-24 | [#1515](https://github.com/harvard-edge/cs249r_book/pull/1515) | profvjreddi | Other/root | refactor(sidebar): consolidate to single source of truth (-1508 lines) |
| 2026-04-24 | [#1520](https://github.com/harvard-edge/cs249r_book/pull/1520) | profvjreddi | SocratiQ | chore(socratiq): post-merge hygiene and bundle-drift CI guard |
| 2026-04-24 | [#1521](https://github.com/harvard-edge/cs249r_book/pull/1521) | profvjreddi | MLSys·im | mlsysim: v0.1.0 release — Apache-2.0 relicense |
| 2026-04-24 | [#1522](https://github.com/harvard-edge/cs249r_book/pull/1522) | profvjreddi | MLSys·im | mlsysim: v0.1.1 + automated PyPI publish workflow |
| 2026-04-24 | [#1523](https://github.com/harvard-edge/cs249r_book/pull/1523) | profvjreddi | MLSys·im | ci(mlsysim): harden PyPI publish — matrix tests, post-publish verify, attestations |
| 2026-04-24 | [#1524](https://github.com/harvard-edge/cs249r_book/pull/1524) | profvjreddi | TinyTorch | polish(tinytorch): sidebar + headers + flat tier cards + PDF milestone transitions |
| 2026-04-24 | [#1525](https://github.com/harvard-edge/cs249r_book/pull/1525) | profvjreddi | MLSys·im | mlsysim: align identity copy with paper title + CHANGELOG-driven release notes |
| 2026-04-24 | [#1526](https://github.com/harvard-edge/cs249r_book/pull/1526) | profvjreddi | TinyTorch | polish(tinytorch/ux): hero hierarchy + milestone roster + honest star CTA |
| 2026-04-24 | [#1527](https://github.com/harvard-edge/cs249r_book/pull/1527) | profvjreddi | TinyTorch | fix(tinytorch/ux): restore H2-H6 decoration override + de-header comparison cards |
| 2026-04-25 | [#1516](https://github.com/harvard-edge/cs249r_book/pull/1516) | dependabot | SocratiQ | build(deps): bump postcss from 8.4.49 to 8.5.10 in /socratiq |
| 2026-04-25 | [#1517](https://github.com/harvard-edge/cs249r_book/pull/1517) | dependabot | SocratiQ | build(deps-dev): bump vite from 5.4.20 to 8.0.10 in /socratiq |
| 2026-04-25 | [#1519](https://github.com/harvard-edge/cs249r_book/pull/1519) | dependabot | SocratiQ | build(deps): bump lodash-es and mermaid in /socratiq |
| 2026-04-25 | [#1528](https://github.com/harvard-edge/cs249r_book/pull/1528) | dependabot | SocratiQ | build(deps): bump seroval from 1.1.1 to 1.5.2 in /socratiq |
| 2026-04-25 | [#1529](https://github.com/harvard-edge/cs249r_book/pull/1529) | farhan523 | TinyTorch | fix(tinytorch/ux): restore dark-mode contrast on preface + missing overrides |
| 2026-04-25 | [#1530](https://github.com/harvard-edge/cs249r_book/pull/1530) | Shashank-Tripathi-07 | MLSys·im | fix(mlsysim): hierarchical AllReduce broadcast 8x overestimate + H200 capacity unit mismatch |
| 2026-04-25 | [#1533](https://github.com/harvard-edge/cs249r_book/pull/1533) | hzeljko | Book | Refined the PDF layout for Chapter 4 (data_engineering.qmd). |
| 2026-04-25 | [#1534](https://github.com/harvard-edge/cs249r_book/pull/1534) | dependabot | SocratiQ | build(deps-dev): bump picomatch from 2.3.1 to 2.3.2 in /socratiq |
| 2026-04-25 | [#1535](https://github.com/harvard-edge/cs249r_book/pull/1535) | dependabot | SocratiQ | build(deps): bump jspdf and jspdf-autotable in /socratiq |
| 2026-04-25 | [#1536](https://github.com/harvard-edge/cs249r_book/pull/1536) | dependabot | SocratiQ | build(deps-dev): bump rollup from 4.29.1 to 4.60.2 in /socratiq |
| 2026-04-25 | [#1537](https://github.com/harvard-edge/cs249r_book/pull/1537) | profvjreddi | TinyTorch | fix(tinytorch/site): site-wide dark-mode coverage + framework version chip |
| 2026-04-25 | [#1541](https://github.com/harvard-edge/cs249r_book/pull/1541) | profvjreddi | TinyTorch | fix(tinytorch/ux): readable code-block surface in dark mode |
| 2026-04-26 | [#1538](https://github.com/harvard-edge/cs249r_book/pull/1538) | dependabot | SocratiQ | build(deps): bump markdown-it from 14.1.0 to 14.1.1 in /socratiq |
| 2026-04-26 | [#1539](https://github.com/harvard-edge/cs249r_book/pull/1539) | dependabot | SocratiQ | build(deps): bump solid-js from 1.9.3 to 1.9.12 in /socratiq |
| 2026-04-26 | [#1540](https://github.com/harvard-edge/cs249r_book/pull/1540) | dependabot | StaffML | build(deps-dev): bump postcss from 8.5.8 to 8.5.10 in /interviews/staffml |
| 2026-04-26 | [#1543](https://github.com/harvard-edge/cs249r_book/pull/1543) | farhan523 | TinyTorch | fix(tinytorch/ux): h3, pre code, and who-card paragraph text visibility |
| 2026-04-26 | [#1544](https://github.com/harvard-edge/cs249r_book/pull/1544) | hzeljko | Book | Refined the PDF layout for Chapter 5 (nn_computation.qmd). |
| 2026-04-26 | [#1545](https://github.com/harvard-edge/cs249r_book/pull/1545) | profvjreddi | Other/root | fix(games): use 'all-time' in display strings to satisfy codespell |
| 2026-04-26 | [#1546](https://github.com/harvard-edge/cs249r_book/pull/1546) | profvjreddi | SocratiQ | fix(socratiq): bump vite-plugin-singlefile to ^2.3.0 (unblocks Bundle Drift) |
| 2026-04-26 | [#1548](https://github.com/harvard-edge/cs249r_book/pull/1548) | profvjreddi | Book | fix(book): break entirely-bold paragraphs that Pandoc promotes to <h4> |
| 2026-04-26 | [#1549](https://github.com/harvard-edge/cs249r_book/pull/1549) | profvjreddi | Book | fix(book): swap inverted MobileNet quantization values + align caption with figure |
| 2026-04-26 | [#1551](https://github.com/harvard-edge/cs249r_book/pull/1551) | profvjreddi | Book | fix(book): widen quantization-impact prose range to cover ResNet's 4.3× |
| 2026-04-26 | [#1552](https://github.com/harvard-edge/cs249r_book/pull/1552) | profvjreddi | Other/root | fix(links): suppress link-rot tracker noise + remove gitignored .claude link |
| 2026-04-26 | [#1553](https://github.com/harvard-edge/cs249r_book/pull/1553) | profvjreddi | Slides | fix(slides): repair the 6 unique broken tinyMLx/courseware URLs |
| 2026-04-26 | [#1554](https://github.com/harvard-edge/cs249r_book/pull/1554) | profvjreddi | Other/root | fix(links): aggressive lycheeignore to drive tracker to zero |
| 2026-04-26 | [#1555](https://github.com/harvard-edge/cs249r_book/pull/1555) | profvjreddi | Other/root | fix(links): round-2 lycheeignore + note on missing 00_course_overview.pdf |
| 2026-04-26 | [#1556](https://github.com/harvard-edge/cs249r_book/pull/1556) | profvjreddi | Other/root | feat(footnotes): citation-adjacency placement audit + binder rule |
| 2026-04-26 | [#1557](https://github.com/harvard-edge/cs249r_book/pull/1557) | farhan523 | TinyTorch | fix(tinytorch/ux): coverage for accent text + light-gradient panels |
| 2026-04-27 | [#1559](https://github.com/harvard-edge/cs249r_book/pull/1559) | dependabot | TinyTorch | deps(tinytorch): update sphinxcontrib-mermaid requirement from >=0.9.2 to >=2.0.1 in /tinytorch |
| 2026-04-27 | [#1560](https://github.com/harvard-edge/cs249r_book/pull/1560) | dependabot | MLSys·im | deps(mlsysim): update plotly requirement from >=5.0.0 to >=6.7.0 in /mlsysim |
| 2026-04-27 | [#1561](https://github.com/harvard-edge/cs249r_book/pull/1561) | dependabot | Dependencies | deps(book): update matplotlib requirement from >=3.7.0 to >=3.9.4 |
| 2026-04-27 | [#1562](https://github.com/harvard-edge/cs249r_book/pull/1562) | dependabot | Dependencies | deps(book): update nltk requirement from >=3.8 to >=3.9.2 |
| 2026-04-27 | [#1563](https://github.com/harvard-edge/cs249r_book/pull/1563) | dependabot | CI/Infra | ci(deps): bump actions/setup-python from 5 to 6 |
| 2026-04-27 | [#1564](https://github.com/harvard-edge/cs249r_book/pull/1564) | dependabot | MLSys·im | deps(mlsysim): update numpy requirement from >=1.24.0 to >=2.2.6 in /mlsysim |
| 2026-04-27 | [#1565](https://github.com/harvard-edge/cs249r_book/pull/1565) | dependabot | StaffML | deps(vault-worker): bump wrangler from 4.84.1 to 4.85.0 in /interviews/staffml-vault-worker |
| 2026-04-27 | [#1566](https://github.com/harvard-edge/cs249r_book/pull/1566) | dependabot | StaffML | deps(staffml-worker): bump wrangler from 4.84.1 to 4.85.0 in /interviews/staffml/worker |
| 2026-04-27 | [#1567](https://github.com/harvard-edge/cs249r_book/pull/1567) | dependabot | CI/Infra | ci(deps): bump actions/github-script from 7 to 9 |
| 2026-04-27 | [#1568](https://github.com/harvard-edge/cs249r_book/pull/1568) | dependabot | MLSys·im | deps(mlsysim): update matplotlib requirement from >=3.10.8 to >=3.10.9 in /mlsysim |
| 2026-04-27 | [#1569](https://github.com/harvard-edge/cs249r_book/pull/1569) | dependabot | StaffML | deps(vault-worker): bump @cloudflare/workers-types from 4.20260422.1 to 4.20260426.1 in /interviews/staffml-vault-worker |
| 2026-04-27 | [#1570](https://github.com/harvard-edge/cs249r_book/pull/1570) | dependabot | StaffML | deps(staffml): bump tailwindcss from 3.4.19 to 4.2.4 in /interviews/staffml |
| 2026-04-27 | [#1571](https://github.com/harvard-edge/cs249r_book/pull/1571) | dependabot | Dependencies | deps(book): update absl-py requirement from >=1.0.0 to >=2.3.1 |
| 2026-04-27 | [#1572](https://github.com/harvard-edge/cs249r_book/pull/1572) | dependabot | StaffML | deps(staffml-worker): bump @cloudflare/workers-types from 4.20260422.1 to 4.20260426.1 in /interviews/staffml/worker |
| 2026-04-27 | [#1573](https://github.com/harvard-edge/cs249r_book/pull/1573) | dependabot | CI/Infra | ci(deps): bump actions/download-artifact from 4 to 8 |
| 2026-04-27 | [#1574](https://github.com/harvard-edge/cs249r_book/pull/1574) | dependabot | MLSys·im | deps(mlsysim): update marimo requirement from >=0.19.0 to >=0.23.3 in /mlsysim |
| 2026-04-27 | [#1575](https://github.com/harvard-edge/cs249r_book/pull/1575) | dependabot | Dependencies | deps(book): update groq requirement from >=0.4.0 to >=1.0.0 |
| 2026-04-27 | [#1576](https://github.com/harvard-edge/cs249r_book/pull/1576) | dependabot | StaffML | deps(staffml): bump postcss from 8.5.10 to 8.5.12 in /interviews/staffml |
| 2026-04-27 | [#1577](https://github.com/harvard-edge/cs249r_book/pull/1577) | dependabot | CI/Infra | ci(deps): bump actions/upload-artifact from 4 to 7 |
| 2026-04-27 | [#1578](https://github.com/harvard-edge/cs249r_book/pull/1578) | dependabot | MLSys·im | deps(mlsysim): update pytest requirement from >=7.0 to >=9.0.3 in /mlsysim |
| 2026-04-27 | [#1579](https://github.com/harvard-edge/cs249r_book/pull/1579) | dependabot | CI/Infra | ci(deps): bump actions/setup-node from 4 to 6 |
| 2026-04-27 | [#1580](https://github.com/harvard-edge/cs249r_book/pull/1580) | dependabot | Dependencies | deps(book): update pydantic requirement from >=2.0.0 to >=2.13.3 |
| 2026-04-27 | [#1581](https://github.com/harvard-edge/cs249r_book/pull/1581) | dependabot | StaffML | deps(staffml): bump jsdom from 29.0.2 to 29.1.0 in /interviews/staffml |
| 2026-04-27 | [#1582](https://github.com/harvard-edge/cs249r_book/pull/1582) | dependabot | StaffML | deps(staffml): bump eslint from 9.39.4 to 10.2.1 in /interviews/staffml |
| 2026-04-27 | [#1583](https://github.com/harvard-edge/cs249r_book/pull/1583) | dependabot | StaffML | deps(staffml): bump typescript from 5.9.3 to 6.0.3 in /interviews/staffml |
| 2026-04-27 | [#1584](https://github.com/harvard-edge/cs249r_book/pull/1584) | dependabot | TinyTorch | deps(tinytorch): update jupyterlab requirement from >=4.2.0 to >=4.5.6 in /tinytorch |
| 2026-04-27 | [#1585](https://github.com/harvard-edge/cs249r_book/pull/1585) | dependabot | TinyTorch | deps(tinytorch): update pyyaml requirement from >=6.0 to >=6.0.3 in /tinytorch |
| 2026-04-27 | [#1586](https://github.com/harvard-edge/cs249r_book/pull/1586) | dependabot | TinyTorch | deps(tinytorch): update nbformat requirement from >=5.10.0 to >=5.10.4 in /tinytorch |
| 2026-04-27 | [#1587](https://github.com/harvard-edge/cs249r_book/pull/1587) | dependabot | TinyTorch | deps(tinytorch): update typing-extensions requirement from >=4.12.0 to >=4.15.0 in /tinytorch |
| 2026-04-27 | [#1588](https://github.com/harvard-edge/cs249r_book/pull/1588) | farhan523 | TinyTorch | fix(tinytorch/ux): light/dark/hybrid contrast for tito CLI cards + tables + code blocks |
| 2026-04-27 | [#1589](https://github.com/harvard-edge/cs249r_book/pull/1589) | hzeljko | Book | Refined the PDF layout for Chapter 6 (nn_architectures.qmd). |
| 2026-04-27 | [#1590](https://github.com/harvard-edge/cs249r_book/pull/1590) | Shashank-Tripathi-07 | StaffML | fix(staffml): correct wrong answer index in cloud-0024 |
| 2026-04-27 | [#1591](https://github.com/harvard-edge/cs249r_book/pull/1591) | Shashank-Tripathi-07 | StaffML | fix(staffml): correct cloud-0013 INT4 throughput math and unit error in distractor |
| 2026-04-27 | [#1592](https://github.com/harvard-edge/cs249r_book/pull/1592) | Shashank-Tripathi-07 | StaffML | fix(staffml): correct mobile-0534 napkin_math numbers to match scenario |
| 2026-04-28 | [#1594](https://github.com/harvard-edge/cs249r_book/pull/1594) | farhan523 | StaffML | Clean up StaffML analytics visibility listener |
| 2026-04-28 | [#1595](https://github.com/harvard-edge/cs249r_book/pull/1595) | hzeljko | Book | Refined the PDF layout for Chapter 7 (frameworks.qmd). |
| 2026-04-28 | [#1596](https://github.com/harvard-edge/cs249r_book/pull/1596) | Shashank-Tripathi-07 | Labs | fix(labs/tests): open lab files with explicit UTF-8 encoding |
| 2026-04-28 | [#1598](https://github.com/harvard-edge/cs249r_book/pull/1598) | profvjreddi | StaffML | refactor(staffml): retire prod static-fallback; opt-in dev-only |
| 2026-04-29 | [#1597](https://github.com/harvard-edge/cs249r_book/pull/1597) | Shashank-Tripathi-07 | MLSys·im | fix(mlsysim): correct unit conversion in calc_monthly_egress_cost |
| 2026-04-29 | [#1599](https://github.com/harvard-edge/cs249r_book/pull/1599) | farhan523 | Instructors | fix(instructors): keep theme toggle visible across breakpoints |
| 2026-04-29 | [#1600](https://github.com/harvard-edge/cs249r_book/pull/1600) | profvjreddi | StaffML | fix(ci/staffml): validate trailing-slash page paths in dev preview |
| 2026-04-30 | [#1601](https://github.com/harvard-edge/cs249r_book/pull/1601) | Shashank-Tripathi-07 | StaffML | fix(staffml): use summary fallback when Worker fetch fails in gauntlet |
| 2026-04-30 | [#1602](https://github.com/harvard-edge/cs249r_book/pull/1602) | Shashank-Tripathi-07 | StaffML | fix(staffml): add pickRandom to keyboard effect and pickNext dep arrays |
| 2026-04-30 | [#1604](https://github.com/harvard-edge/cs249r_book/pull/1604) | Shashank-Tripathi-07 | Labs | fix(instructors): correct lab subtitles in foundations syllabus (labs 09-15) |
| 2026-04-30 | [#1605](https://github.com/harvard-edge/cs249r_book/pull/1605) | farhan523 | Site | fix(site/community): restore spotlight quote visibility in dark mode |
| 2026-04-30 | [#1606](https://github.com/harvard-edge/cs249r_book/pull/1606) | farhan523 | Kits | fix(kits/raspi/llm): wrap context-token array to prevent page overflow |
| 2026-04-30 | [#1607](https://github.com/harvard-edge/cs249r_book/pull/1607) | farhan523 | Other/root | fix(quarto/scss): scope light-text overrides to dark theme |
| 2026-04-30 | [#1608](https://github.com/harvard-edge/cs249r_book/pull/1608) | Shashank-Tripathi-07 | MLSys·im | fix(mlsysim): skip viz test when matplotlib is not installed |
| 2026-04-30 | [#1609](https://github.com/harvard-edge/cs249r_book/pull/1609) | Shashank-Tripathi-07 | Kits | fix(kits): fix TFLite size typo and broken pth command in raspi image-classification lab |
| 2026-04-30 | [#1610](https://github.com/harvard-edge/cs249r_book/pull/1610) | hzeljko | Book | Refined the PDF layout for Chapter 8 (training.qmd). |
| 2026-04-30 | [#1616](https://github.com/harvard-edge/cs249r_book/pull/1616) | profvjreddi | TinyTorch | fix(tinytorch): tito module status milestone tracking (#1612, #1615) |
| 2026-04-30 | [#1617](https://github.com/harvard-edge/cs249r_book/pull/1617) | profvjreddi | TinyTorch | fix(tinytorch): perceptron weights deterministic across runs (#1611) |
| 2026-04-30 | [#1618](https://github.com/harvard-edge/cs249r_book/pull/1618) | profvjreddi | TinyTorch | fix(tinytorch): milestone 3 xor convergence and reporting (#1613, #1614) |
| 2026-04-30 | [#1619](https://github.com/harvard-edge/cs249r_book/pull/1619) | profvjreddi | Other/root | refactor: prevent further clone-size bloat (Phase 1, #1393, #1175) |
| 2026-04-30 | [#1620](https://github.com/harvard-edge/cs249r_book/pull/1620) | profvjreddi | CI/Infra | chore(codespell): permanently clear false positives blocking PR CI |
| 2026-05-01 | [#1621](https://github.com/harvard-edge/cs249r_book/pull/1621) | hzeljko | Book | Refined the PDF layout for Chapter 9 (data_selection.qmd). |
| 2026-05-01 | [#1622](https://github.com/harvard-edge/cs249r_book/pull/1622) | farhan523 | Other/root | fix(dev): rewrite shared navbar URLs by page depth in dev preview |
| 2026-05-02 | [#1623](https://github.com/harvard-edge/cs249r_book/pull/1623) | farhan523 | Kits | fix(kits): improve dark mode dropdown menu contrast |
| 2026-05-02 | [#1624](https://github.com/harvard-edge/cs249r_book/pull/1624) | farhan523 | Kits | fix(kits): make code blocks readable in dark mode |
| 2026-05-03 | [#1625](https://github.com/harvard-edge/cs249r_book/pull/1625) | Shashank-Tripathi-07 | Labs | fix(labs): correct ESP32 SRAM field and OOM ratio in Lab 01 |
| 2026-05-03 | [#1626](https://github.com/harvard-edge/cs249r_book/pull/1626) | Shashank-Tripathi-07 | Labs | fix(labs): use ESP32 SRAM capacity field in lab 02 |
| 2026-05-03 | [#1627](https://github.com/harvard-edge/cs249r_book/pull/1627) | Shashank-Tripathi-07 | Labs | fix(labs): use ESP32 SRAM capacity and correct OOM badge in lab 03 |
| 2026-05-03 | [#1628](https://github.com/harvard-edge/cs249r_book/pull/1628) | Shashank-Tripathi-07 | Labs | fix(labs): use ESP32 SRAM capacity field in lab 07 |
| 2026-05-03 | [#1629](https://github.com/harvard-edge/cs249r_book/pull/1629) | Shashank-Tripathi-07 | Labs | fix(labs): correct error cascade amplification factor in lab 04 |
| 2026-05-03 | [#1630](https://github.com/harvard-edge/cs249r_book/pull/1630) | Shashank-Tripathi-07 | Labs | fix(labs): correct FLOP scaling answer key in lab 05 Part C |
| 2026-05-03 | [#1631](https://github.com/harvard-edge/cs249r_book/pull/1631) | Shashank-Tripathi-07 | Labs | fix(labs): correct cold start time in lab 13 Part D |
| 2026-05-03 | [#1632](https://github.com/harvard-edge/cs249r_book/pull/1632) | Shashank-Tripathi-07 | Labs | fix(labs): correct debt multiplier answer in lab 14 Part D |
| 2026-05-03 | [#1633](https://github.com/harvard-edge/cs249r_book/pull/1633) | farhan523 | Instructors | fix(instructors): make left sidebar readable in dark mode |
| 2026-05-03 | [#1634](https://github.com/harvard-edge/cs249r_book/pull/1634) | farhan523 | TinyTorch | fix(instructors): repair broken TinyTorch Instructor Guide link |
| 2026-05-03 | [#1636](https://github.com/harvard-edge/cs249r_book/pull/1636) | hzeljko | Book | Refined the PDF layout for Chapter 10 (model_compression.qmd). |
| 2026-05-03 | [#1637](https://github.com/harvard-edge/cs249r_book/pull/1637) | profvjreddi | Book | fix(book): drop non-English TikZ comment that fails codespell |
| 2026-05-03 | [#1638](https://github.com/harvard-edge/cs249r_book/pull/1638) | farhan523 | Instructors | fix(instructors): improve dark mode dropdown menu contrast |
| 2026-05-03 | [#1639](https://github.com/harvard-edge/cs249r_book/pull/1639) | farhan523 | Instructors | fix(instructors): readable blockquote text in dark mode |
| 2026-05-04 | [#1640](https://github.com/harvard-edge/cs249r_book/pull/1640) | farhan523 | Instructors | fix(instructors): readable landing tables and footer CTA |
| 2026-05-04 | [#1641](https://github.com/harvard-edge/cs249r_book/pull/1641) | farhan523 | Slides | fix(slides): readable inventory tables on landing page |
| 2026-05-04 | [#1642](https://github.com/harvard-edge/cs249r_book/pull/1642) | dependabot | MLSys·im | deps(mlsysim): update rich requirement from >=13.0.0 to >=15.0.0 in /mlsysim |
| 2026-05-04 | [#1643](https://github.com/harvard-edge/cs249r_book/pull/1643) | dependabot | Dependencies | deps(book-ext): bump @types/vscode from 1.116.0 to 1.118.0 in /book/vscode-ext |
| 2026-05-04 | [#1644](https://github.com/harvard-edge/cs249r_book/pull/1644) | dependabot | StaffML | deps(staffml-worker): bump wrangler from 4.85.0 to 4.87.0 in /interviews/staffml/worker |
| 2026-05-04 | [#1645](https://github.com/harvard-edge/cs249r_book/pull/1645) | dependabot | MLSys·im | deps(mlsysim): update nbformat requirement from >=5.7 to >=5.10.4 in /mlsysim |
| 2026-05-04 | [#1646](https://github.com/harvard-edge/cs249r_book/pull/1646) | dependabot | Kits | deps(kits-ext): bump @types/vscode from 1.116.0 to 1.118.0 in /kits/vscode-ext |
| 2026-05-04 | [#1647](https://github.com/harvard-edge/cs249r_book/pull/1647) | dependabot | Labs | deps(labs-ext): bump @types/vscode from 1.116.0 to 1.118.0 in /labs/vscode-ext |
| 2026-05-04 | [#1648](https://github.com/harvard-edge/cs249r_book/pull/1648) | dependabot | MLSys·im | deps(mlsysim-ext): bump @types/vscode from 1.116.0 to 1.118.0 in /mlsysim/vscode-ext |
| 2026-05-04 | [#1649](https://github.com/harvard-edge/cs249r_book/pull/1649) | dependabot | StaffML | deps(vault-worker): bump @cloudflare/workers-types from 4.20260426.1 to 4.20260504.1 in /interviews/staffml-vault-worker |
| 2026-05-04 | [#1650](https://github.com/harvard-edge/cs249r_book/pull/1650) | dependabot | StaffML | deps(staffml): bump react-medium-image-zoom from 5.4.3 to 5.4.5 in /interviews/staffml in the next-react group across 1 directory |
| 2026-05-04 | [#1651](https://github.com/harvard-edge/cs249r_book/pull/1651) | dependabot | TinyTorch | deps(tinytorch-ext): bump @types/vscode from 1.116.0 to 1.118.0 in /tinytorch/vscode-ext |
| 2026-05-04 | [#1652](https://github.com/harvard-edge/cs249r_book/pull/1652) | dependabot | TinyTorch | deps(tinytorch): update ipywidgets requirement from >=8.0.0 to >=8.1.8 in /tinytorch |
| 2026-05-04 | [#1653](https://github.com/harvard-edge/cs249r_book/pull/1653) | dependabot | Dependencies | deps(book): update ghostscript requirement from >=0.7 to >=0.8.1 |
| 2026-05-04 | [#1654](https://github.com/harvard-edge/cs249r_book/pull/1654) | dependabot | MLSys·im | deps(mlsysim): update scipy requirement from >=1.10.0 to >=1.15.3 in /mlsysim |
| 2026-05-04 | [#1655](https://github.com/harvard-edge/cs249r_book/pull/1655) | dependabot | StaffML | deps(staffml-worker): bump @cloudflare/workers-types from 4.20260426.1 to 4.20260504.1 in /interviews/staffml/worker |
| 2026-05-04 | [#1656](https://github.com/harvard-edge/cs249r_book/pull/1656) | dependabot | TinyTorch | deps(tinytorch): update pytest-cov requirement from >=4.0.0 to >=7.1.0 in /tinytorch |
| 2026-05-04 | [#1657](https://github.com/harvard-edge/cs249r_book/pull/1657) | dependabot | StaffML | deps(staffml): bump eslint from 10.2.1 to 10.3.0 in /interviews/staffml |
| 2026-05-04 | [#1658](https://github.com/harvard-edge/cs249r_book/pull/1658) | dependabot | TinyTorch | deps(tinytorch): update certifi requirement from >=2023.0.0 to >=2026.4.22 in /tinytorch |
| 2026-05-04 | [#1660](https://github.com/harvard-edge/cs249r_book/pull/1660) | dependabot | Dependencies | deps(book): update nbdev requirement from >=2.3.0 to >=3.0.15 |
| 2026-05-04 | [#1661](https://github.com/harvard-edge/cs249r_book/pull/1661) | dependabot | TinyTorch | deps(tinytorch): update matplotlib requirement from >=3.9.0 to >=3.10.9 in /tinytorch |
| 2026-05-04 | [#1662](https://github.com/harvard-edge/cs249r_book/pull/1662) | dependabot | MLSys·im | deps(mlsysim): update pint requirement from >=0.23 to >=0.24.4 in /mlsysim |
| 2026-05-04 | [#1663](https://github.com/harvard-edge/cs249r_book/pull/1663) | dependabot | StaffML | deps(vault-worker): bump wrangler from 4.85.0 to 4.87.0 in /interviews/staffml-vault-worker |
| 2026-05-04 | [#1664](https://github.com/harvard-edge/cs249r_book/pull/1664) | dependabot | CI/Infra | ci(deps): bump actions/checkout from 4 to 6 |
| 2026-05-04 | [#1665](https://github.com/harvard-edge/cs249r_book/pull/1665) | dependabot | Dependencies | deps(book): update seaborn requirement from >=0.13.0 to >=0.13.2 |
| 2026-05-04 | [#1666](https://github.com/harvard-edge/cs249r_book/pull/1666) | dependabot | TinyTorch | deps(tinytorch): update nbdev requirement from >=2.3.0 to >=3.0.15 in /tinytorch |
| 2026-05-04 | [#1668](https://github.com/harvard-edge/cs249r_book/pull/1668) | dependabot | Dependencies | deps(book): update pint requirement from >=0.23 to >=0.24.4 |
| 2026-05-04 | [#1669](https://github.com/harvard-edge/cs249r_book/pull/1669) | dependabot | CI/Infra | ci(deps): bump actions/download-artifact from 4 to 8 |
| 2026-05-04 | [#1670](https://github.com/harvard-edge/cs249r_book/pull/1670) | dependabot | StaffML | deps(staffml): bump jsdom from 29.1.0 to 29.1.1 in /interviews/staffml |
| 2026-05-04 | [#1671](https://github.com/harvard-edge/cs249r_book/pull/1671) | dependabot | StaffML | deps(staffml): bump sigma from 3.0.2 to 3.0.3 in /interviews/staffml |
| 2026-05-04 | [#1672](https://github.com/harvard-edge/cs249r_book/pull/1672) | dependabot | CI/Infra | ci(deps): bump actions/setup-python from 5 to 6 |
| 2026-05-04 | [#1673](https://github.com/harvard-edge/cs249r_book/pull/1673) | dependabot | CI/Infra | ci(deps): bump softprops/action-gh-release from 2 to 3 |
| 2026-05-04 | [#1674](https://github.com/harvard-edge/cs249r_book/pull/1674) | dependabot | CI/Infra | ci(deps): bump actions/upload-artifact from 4 to 7 |
| 2026-05-04 | [#1675](https://github.com/harvard-edge/cs249r_book/pull/1675) | dependabot | Dependencies | deps(book): update jupyterlab requirement from >=4.2.0 to >=4.5.7 |
| 2026-05-05 | [#1677](https://github.com/harvard-edge/cs249r_book/pull/1677) | hzeljko | Book | Refined the PDF layout for Chapter 11 (hw_acceleration.qmd). |
| 2026-05-05 | [#1679](https://github.com/harvard-edge/cs249r_book/pull/1679) | profvjreddi | Other/root | fix: math notation and prose drift cleanup (31 sites, 11 files) |
| 2026-05-05 | [#1680](https://github.com/harvard-edge/cs249r_book/pull/1680) | profvjreddi | Book | cleanup(vol2): lowercase 'fleet stack' / 'energy wall' in body prose |
| 2026-05-05 | [#1681](https://github.com/harvard-edge/cs249r_book/pull/1681) | profvjreddi | Book | fix(book): numerical and clarity corrections across 11 chapter files |
| 2026-05-07 | [#1676](https://github.com/harvard-edge/cs249r_book/pull/1676) | Shashank-Tripathi-07 | Labs | fix(lab11): correct Part E Tiling Dividend answer key from option C to option D |
| 2026-05-07 | [#1678](https://github.com/harvard-edge/cs249r_book/pull/1678) | Shashank-Tripathi-07 | StaffML | fix(mlsysim): replace hardcoded Mac path in interviewer.py with relative Path |
| 2026-05-07 | [#1687](https://github.com/harvard-edge/cs249r_book/pull/1687) | Shashank-Tripathi-07 | TinyTorch | docs(tinytorch): expand OOM acronym in Module 01 checklist item |
| 2026-05-07 | [#1688](https://github.com/harvard-edge/cs249r_book/pull/1688) | hzeljko | Book | Refined the PDF layout for Chapter 12 (benchmarking.qmd). |
| 2026-05-07 | [#1693](https://github.com/harvard-edge/cs249r_book/pull/1693) | dependabot | Dependencies | chore(deps): bump ip-address from 10.1.0 to 10.2.0 |
| 2026-05-07 | [#1694](https://github.com/harvard-edge/cs249r_book/pull/1694) | dependabot | Dependencies | chore(deps): bump basic-ftp from 5.3.0 to 5.3.1 |
| 2026-05-08 | [#1697](https://github.com/harvard-edge/cs249r_book/pull/1697) | hzeljko | Book | Refined the PDF layout for Chapter 13 (model_serving.qmd). |
| 2026-05-10 | [#1682](https://github.com/harvard-edge/cs249r_book/pull/1682) | farhan523 | Slides | fix(slides): readable section-list tables on tinyml pages  |
| 2026-05-10 | [#1683](https://github.com/harvard-edge/cs249r_book/pull/1683) | farhan523 | Slides | fix(slides): readable slide-table headers on teaching/vol1/vol2 in dark mode |
| 2026-05-10 | [#1690](https://github.com/harvard-edge/cs249r_book/pull/1690) | bdub-1 | TinyTorch | docs(tinytorch): add GELU implementation and explanation |
| 2026-05-10 | [#1695](https://github.com/harvard-edge/cs249r_book/pull/1695) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): reuse Sigmoid class in GELU solution |
| 2026-05-10 | [#1696](https://github.com/harvard-edge/cs249r_book/pull/1696) | Shashank-Tripathi-07 | Labs | fix(labs): catch missing wheel before deploy to prevent BadZipFile in browser |
| 2026-05-10 | [#1698](https://github.com/harvard-edge/cs249r_book/pull/1698) | hzeljko | Book | Refined the PDF layout for Chapter 14 (ml_ops.qmd). |
| 2026-05-10 | [#1699](https://github.com/harvard-edge/cs249r_book/pull/1699) | hzeljko | Book | Refined the PDF layout for Chapter 15 (responsible_engr.qmd). |
| 2026-05-10 | [#1700](https://github.com/harvard-edge/cs249r_book/pull/1700) | hzeljko | Book | Refined the PDF layout for Chapter 16 (conclusion.qmd). |
| 2026-05-10 | [#1701](https://github.com/harvard-edge/cs249r_book/pull/1701) | hzeljko | Book | Refined the PDF layout for Appendix A (appendix_dam.qmd). |
| 2026-05-10 | [#1702](https://github.com/harvard-edge/cs249r_book/pull/1702) | hzeljko | Book | Refined the PDF layout for Appendix B (appendix_data.qmd). |
| 2026-05-10 | [#1703](https://github.com/harvard-edge/cs249r_book/pull/1703) | hzeljko | Book | Refined the PDF layout for Appendix C (appendix_algorithm.qmd). |
| 2026-05-10 | [#1704](https://github.com/harvard-edge/cs249r_book/pull/1704) | hzeljko | Book | Refined the PDF layout for Appendix D (appendix_machine.qmd). |
| 2026-05-10 | [#1705](https://github.com/harvard-edge/cs249r_book/pull/1705) | hzeljko | Book | Refined the PDF layout for Appendix E (appendix_assumptions.qmd). |
| 2026-05-12 | [#1707](https://github.com/harvard-edge/cs249r_book/pull/1707) | dependabot | MLSys·im | deps(mlsysim-ext): bump @types/node from 25.6.0 to 25.6.2 in /mlsysim/vscode-ext |
| 2026-05-12 | [#1708](https://github.com/harvard-edge/cs249r_book/pull/1708) | dependabot | MLSys·im | deps(mlsysim): update typer requirement from >=0.9.0 to >=0.25.1 in /mlsysim |
| 2026-05-12 | [#1709](https://github.com/harvard-edge/cs249r_book/pull/1709) | dependabot | TinyTorch | deps(tinytorch-ext): bump @types/node from 25.6.0 to 25.6.2 in /tinytorch/vscode-ext |
| 2026-05-12 | [#1710](https://github.com/harvard-edge/cs249r_book/pull/1710) | dependabot | TinyTorch | deps(tinytorch): update jupytext requirement from >=1.19.1 to >=1.19.2 in /tinytorch |
| 2026-05-12 | [#1711](https://github.com/harvard-edge/cs249r_book/pull/1711) | dependabot | Kits | deps(kits-ext): bump @types/node from 25.6.0 to 25.6.2 in /kits/vscode-ext |
| 2026-05-12 | [#1712](https://github.com/harvard-edge/cs249r_book/pull/1712) | dependabot | Dependencies | deps(book-ext): bump @types/node from 25.6.0 to 25.6.2 in /book/vscode-ext |
| 2026-05-12 | [#1713](https://github.com/harvard-edge/cs249r_book/pull/1713) | dependabot | Labs | deps(labs-ext): bump @types/node from 25.6.0 to 25.6.2 in /labs/vscode-ext |
| 2026-05-12 | [#1714](https://github.com/harvard-edge/cs249r_book/pull/1714) | dependabot | StaffML | deps(vault-worker): bump @cloudflare/workers-types from 4.20260504.1 to 4.20260511.1 in /interviews/staffml-vault-worker |
| 2026-05-12 | [#1715](https://github.com/harvard-edge/cs249r_book/pull/1715) | dependabot | MLSys·im | deps(mlsysim): update marimo requirement from >=0.23.3 to >=0.23.5 in /mlsysim |
| 2026-05-12 | [#1716](https://github.com/harvard-edge/cs249r_book/pull/1716) | dependabot | StaffML | deps(staffml-worker): bump @cloudflare/workers-types from 4.20260504.1 to 4.20260511.1 in /interviews/staffml/worker |
| 2026-05-12 | [#1717](https://github.com/harvard-edge/cs249r_book/pull/1717) | dependabot | TinyTorch | deps(tinytorch): update setuptools requirement from >=64.0 to >=82.0.1 in /tinytorch |
| 2026-05-12 | [#1718](https://github.com/harvard-edge/cs249r_book/pull/1718) | dependabot | MLSys·im | deps(mlsysim): update pydantic requirement from >=2.0.0 to >=2.13.4 in /mlsysim |
| 2026-05-12 | [#1720](https://github.com/harvard-edge/cs249r_book/pull/1720) | dependabot | CI/Infra | ci(deps): bump actions/upload-artifact from 4 to 7 |
| 2026-05-12 | [#1721](https://github.com/harvard-edge/cs249r_book/pull/1721) | dependabot | StaffML | deps(vault-worker): bump wrangler from 4.87.0 to 4.90.0 in /interviews/staffml-vault-worker |
| 2026-05-12 | [#1722](https://github.com/harvard-edge/cs249r_book/pull/1722) | dependabot | TinyTorch | deps(tinytorch): update rich requirement from >=13.0.0 to >=15.0.0 in /tinytorch |
| 2026-05-12 | [#1723](https://github.com/harvard-edge/cs249r_book/pull/1723) | dependabot | Dependencies | deps(book): update ipywidgets requirement from >=8.0.0 to >=8.1.8 |
| 2026-05-12 | [#1724](https://github.com/harvard-edge/cs249r_book/pull/1724) | dependabot | MLSys·im | deps(mlsysim): update pandas requirement from >=2.0.0 to >=2.3.3 in /mlsysim |
| 2026-05-12 | [#1725](https://github.com/harvard-edge/cs249r_book/pull/1725) | dependabot | StaffML | deps(staffml-worker): bump wrangler from 4.87.0 to 4.90.0 in /interviews/staffml/worker |
| 2026-05-12 | [#1726](https://github.com/harvard-edge/cs249r_book/pull/1726) | dependabot | MLSys·im | deps(mlsysim): update nbclient requirement from >=0.7 to >=0.10.4 in /mlsysim |
| 2026-05-12 | [#1727](https://github.com/harvard-edge/cs249r_book/pull/1727) | dependabot | TinyTorch | deps(tinytorch): update jupyter-book requirement from <2.0.0,>=1.0.0 to >=2.1.5,<3.0.0 in /tinytorch |
| 2026-05-12 | [#1728](https://github.com/harvard-edge/cs249r_book/pull/1728) | dependabot | Dependencies | deps(book): update attrs requirement from >=23.0.0 to >=26.1.0 |
| 2026-05-12 | [#1729](https://github.com/harvard-edge/cs249r_book/pull/1729) | dependabot | Dependencies | deps(book): update jupytext requirement from >=1.19.1 to >=1.19.2 |
| 2026-05-12 | [#1730](https://github.com/harvard-edge/cs249r_book/pull/1730) | dependabot | StaffML | deps(staffml): bump the next-react group in /interviews/staffml with 3 updates |
| 2026-05-12 | [#1731](https://github.com/harvard-edge/cs249r_book/pull/1731) | dependabot | Dependencies | deps(book): update bibtexparser requirement from >=1.4.0 to >=1.4.4 |
| 2026-05-12 | [#1732](https://github.com/harvard-edge/cs249r_book/pull/1732) | dependabot | StaffML | deps(staffml): bump tailwindcss from 4.2.4 to 4.3.0 in /interviews/staffml |
| 2026-05-12 | [#1733](https://github.com/harvard-edge/cs249r_book/pull/1733) | dependabot | Dependencies | deps(book): update yamllint requirement from >=1.35.0 to >=1.37.1 |
| 2026-05-12 | [#1734](https://github.com/harvard-edge/cs249r_book/pull/1734) | dependabot | StaffML | deps(staffml): bump @types/node from 25.6.0 to 25.6.2 in /interviews/staffml |
| 2026-05-12 | [#1735](https://github.com/harvard-edge/cs249r_book/pull/1735) | dependabot | StaffML | deps(staffml): bump eslint-config-next from 16.2.4 to 16.2.6 in /interviews/staffml |
| 2026-05-12 | [#1736](https://github.com/harvard-edge/cs249r_book/pull/1736) | dependabot | StaffML | deps(staffml): bump autoprefixer from 10.4.27 to 10.5.0 in /interviews/staffml |
| 2026-05-12 | [#1737](https://github.com/harvard-edge/cs249r_book/pull/1737) | dependabot | SocratiQ | Bump mermaid from 11.14.0 to 11.15.0 in /socratiq |
| 2026-05-12 | [#1739](https://github.com/harvard-edge/cs249r_book/pull/1739) | dependabot | StaffML | Bump next from 16.2.4 to 16.2.6 in /interviews/staffml |
| 2026-05-12 | [#1740](https://github.com/harvard-edge/cs249r_book/pull/1740) | farhan523 | Labs | fix(labs): readability for navbar dropdowns and landing-page headings |
| 2026-05-14 | [#1719](https://github.com/harvard-edge/cs249r_book/pull/1719) | dependabot | TinyTorch | deps(tinytorch): update sphinxcontrib-mermaid requirement from >=2.0.1 to >=2.0.2 in /tinytorch |
| 2026-05-14 | [#1738](https://github.com/harvard-edge/cs249r_book/pull/1738) | vedant-a-joshi | TinyTorch | tinytorch modules clarity |
| 2026-05-14 | [#1741](https://github.com/harvard-edge/cs249r_book/pull/1741) | farhan523 | MLSys·im | Fix MLSysim landing page dark mode code and references |
| 2026-05-14 | [#1742](https://github.com/harvard-edge/cs249r_book/pull/1742) | farhan523 | MLSys·im | Fix MLSysim docs inline code dark mode |
| 2026-05-14 | [#1743](https://github.com/harvard-edge/cs249r_book/pull/1743) | farhan523 | MLSys·im | Fix MLSysim bibliography dark mode |
| 2026-05-15 | [#1744](https://github.com/harvard-edge/cs249r_book/pull/1744) | hzeljko | Book | Refined the PDF layout for chapter 1 (introduction.qmd). |
| 2026-05-16 | [#1746](https://github.com/harvard-edge/cs249r_book/pull/1746) | farhan523 | MLSys·im | fix(mlsysim/docs): resolve 404s on Self-Paced Tutorial sidebar links |
| 2026-05-16 | [#1747](https://github.com/harvard-edge/cs249r_book/pull/1747) | farhan523 | Other/root | Fix navbar dropdown styling |
| 2026-05-17 | [#1748](https://github.com/harvard-edge/cs249r_book/pull/1748) | hzeljko | Book | Refined the PDF layout for chapter 2 (ml_systems.qmd). |
| 2026-05-17 | [#1749](https://github.com/harvard-edge/cs249r_book/pull/1749) | hzeljko | Book | Refined the PDF layout for chapter 3 (ml_workflow.qmd). |
| 2026-05-17 | [#1750](https://github.com/harvard-edge/cs249r_book/pull/1750) | hzeljko | Book | Refined the PDF layout for chapter 4 (data_engineering.qmd). |
| 2026-05-18 | [#1756](https://github.com/harvard-edge/cs249r_book/pull/1756) | dependabot | Dependencies | deps(book-ext): bump @types/node from 25.6.2 to 25.8.0 in /book/vscode-ext |
| 2026-05-18 | [#1758](https://github.com/harvard-edge/cs249r_book/pull/1758) | dependabot | TinyTorch | deps(tinytorch): update jupytext requirement from >=1.19.2 to >=1.19.3 in /tinytorch |
| 2026-05-18 | [#1759](https://github.com/harvard-edge/cs249r_book/pull/1759) | dependabot | MLSys·im | deps(mlsysim-ext): bump @types/node from 25.6.2 to 25.8.0 in /mlsysim/vscode-ext |
| 2026-05-18 | [#1761](https://github.com/harvard-edge/cs249r_book/pull/1761) | dependabot | Labs | deps(labs-ext): bump @types/vscode from 1.118.0 to 1.120.0 in /labs/vscode-ext |
| 2026-05-18 | [#1762](https://github.com/harvard-edge/cs249r_book/pull/1762) | dependabot | TinyTorch | deps(tinytorch-ext): bump @types/node from 25.6.2 to 25.8.0 in /tinytorch/vscode-ext |
| 2026-05-18 | [#1763](https://github.com/harvard-edge/cs249r_book/pull/1763) | dependabot | Kits | deps(kits-ext): bump @types/vscode from 1.118.0 to 1.120.0 in /kits/vscode-ext |
| 2026-05-18 | [#1764](https://github.com/harvard-edge/cs249r_book/pull/1764) | dependabot | StaffML | deps(staffml-worker): bump wrangler from 4.90.0 to 4.92.0 in /interviews/staffml/worker |
| 2026-05-18 | [#1765](https://github.com/harvard-edge/cs249r_book/pull/1765) | dependabot | StaffML | deps(staffml): bump @playwright/test from 1.59.1 to 1.60.0 in /interviews/staffml |
| 2026-05-18 | [#1766](https://github.com/harvard-edge/cs249r_book/pull/1766) | dependabot | Dependencies | deps(book): update pydantic requirement from >=2.13.3 to >=2.13.4 |
| 2026-05-18 | [#1769](https://github.com/harvard-edge/cs249r_book/pull/1769) | dependabot | StaffML | deps(staffml-worker): bump @cloudflare/workers-types from 4.20260511.1 to 4.20260518.1 in /interviews/staffml/worker |
| 2026-05-18 | [#1770](https://github.com/harvard-edge/cs249r_book/pull/1770) | dependabot | MLSys·im | deps(mlsysim): update pyright requirement from >=1.1.300 to >=1.1.409 in /mlsysim |
| 2026-05-18 | [#1771](https://github.com/harvard-edge/cs249r_book/pull/1771) | dependabot | StaffML | deps(staffml): bump @vitejs/plugin-react from 6.0.1 to 6.0.2 in /interviews/staffml |
| 2026-05-18 | [#1773](https://github.com/harvard-edge/cs249r_book/pull/1773) | dependabot | StaffML | deps(vault-worker): bump wrangler from 4.90.0 to 4.92.0 in /interviews/staffml-vault-worker |
| 2026-05-18 | [#1775](https://github.com/harvard-edge/cs249r_book/pull/1775) | dependabot | StaffML | deps(vault-worker): bump @cloudflare/workers-types from 4.20260511.1 to 4.20260518.1 in /interviews/staffml-vault-worker |
| 2026-05-18 | [#1776](https://github.com/harvard-edge/cs249r_book/pull/1776) | dependabot | StaffML | deps(staffml): bump vitest from 4.1.5 to 4.1.6 in /interviews/staffml |
| 2026-05-18 | [#1777](https://github.com/harvard-edge/cs249r_book/pull/1777) | dependabot | Dependencies | deps(book): update jupytext requirement from >=1.19.2 to >=1.19.3 |
| 2026-05-18 | [#1778](https://github.com/harvard-edge/cs249r_book/pull/1778) | dependabot | StaffML | deps(staffml): bump lucide-react from 1.14.0 to 1.16.0 in /interviews/staffml |
| 2026-05-18 | [#1787](https://github.com/harvard-edge/cs249r_book/pull/1787) | dependabot | StaffML | chore(deps-dev): bump brace-expansion from 5.0.5 to 5.0.6 in /interviews/staffml |
| 2026-05-20 | [#1788](https://github.com/harvard-edge/cs249r_book/pull/1788) | hzeljko | Book | Refined the PDF layout for chapter 5 (nn_computation.qmd). |
| 2026-05-20 | [#1789](https://github.com/harvard-edge/cs249r_book/pull/1789) | hzeljko | Book | Refined the PDF layout for chapter 6 (nn_architectures.qmd). |
| 2026-05-22 | [#1793](https://github.com/harvard-edge/cs249r_book/pull/1793) | hzeljko | Book | Refined the PDF layout for chapter 1 (introduction.qmd). |
| 2026-05-24 | [#1796](https://github.com/harvard-edge/cs249r_book/pull/1796) | hzeljko | Book | Refined the PDF layout for chapter 2 (/ml_systems.qmd). |
| 2026-05-24 | [#1800](https://github.com/harvard-edge/cs249r_book/pull/1800) | hzeljko | Book | Refined the PDF layout for chapter 3 (ml_workflow.qmd). |
| 2026-05-24 | [#1801](https://github.com/harvard-edge/cs249r_book/pull/1801) | hzeljko | Book | Refined the PDF layout for chapter 4 (data_engineering.qmd) |
| 2026-05-24 | [#1802](https://github.com/harvard-edge/cs249r_book/pull/1802) | hzeljko | Book | Refined the PDF layout for chapter 5 (nn_computation.qmd) |
| 2026-05-24 | [#1803](https://github.com/harvard-edge/cs249r_book/pull/1803) | hzeljko | Book | Refined the PDF layout for chapter 6 (nn_architectures.qmd) |
| 2026-05-26 | [#1804](https://github.com/harvard-edge/cs249r_book/pull/1804) | hzeljko | Book | Refined the PDF layout for chapter 7 (frameworks.qmd) |
| 2026-05-26 | [#1805](https://github.com/harvard-edge/cs249r_book/pull/1805) | hzeljko | Book | Refined the PDF layout for chapter 8 (training.qmd) |
| 2026-05-26 | [#1806](https://github.com/harvard-edge/cs249r_book/pull/1806) | hzeljko | Book | Refined the PDF layout for chapter 9 (data_selection.qmd) |
| 2026-05-27 | [#1751](https://github.com/harvard-edge/cs249r_book/pull/1751) | Shashank-Tripathi-07 | Book | fix(book): add body.quarto-dark selectors to foldbox dark mode CSS |
| 2026-05-27 | [#1752](https://github.com/harvard-edge/cs249r_book/pull/1752) | Shashank-Tripathi-07 | Book | fix(book): add body.quarto-dark selectors to video-enhanced.css dark mode |
| 2026-05-27 | [#1753](https://github.com/harvard-edge/cs249r_book/pull/1753) | dependabot | Dependencies | deps(book-ext): bump @types/vscode from 1.118.0 to 1.120.0 in /book/vscode-ext |
| 2026-05-27 | [#1754](https://github.com/harvard-edge/cs249r_book/pull/1754) | dependabot | MLSys·im | deps(mlsysim-ext): bump @types/vscode from 1.118.0 to 1.120.0 in /mlsysim/vscode-ext |
| 2026-05-27 | [#1755](https://github.com/harvard-edge/cs249r_book/pull/1755) | dependabot | TinyTorch | deps(tinytorch-ext): bump @types/vscode from 1.118.0 to 1.120.0 in /tinytorch/vscode-ext |
| 2026-05-27 | [#1757](https://github.com/harvard-edge/cs249r_book/pull/1757) | dependabot | Labs | deps(labs-ext): bump @types/node from 25.6.2 to 25.9.0 in /labs/vscode-ext |
| 2026-05-27 | [#1760](https://github.com/harvard-edge/cs249r_book/pull/1760) | dependabot | Kits | deps(kits-ext): bump @types/node from 25.6.2 to 25.9.0 in /kits/vscode-ext |
| 2026-05-27 | [#1774](https://github.com/harvard-edge/cs249r_book/pull/1774) | dependabot | StaffML | deps(staffml): bump eslint from 9.39.4 to 10.4.0 in /interviews/staffml |
| 2026-05-27 | [#1779](https://github.com/harvard-edge/cs249r_book/pull/1779) | Shashank-Tripathi-07 | Site | fix(404): support Quarto dark mode toggle on all 404 pages |
| 2026-05-27 | [#1780](https://github.com/harvard-edge/cs249r_book/pull/1780) | Shashank-Tripathi-07 | Site | fix(site): dark mode for nav-footer, dropdown-menu, and star-history image |
| 2026-05-27 | [#1781](https://github.com/harvard-edge/cs249r_book/pull/1781) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): correct MatmulBackward gradients for 1D vector inputs |
| 2026-05-27 | [#1783](https://github.com/harvard-edge/cs249r_book/pull/1783) | Shashank-Tripathi-07 | Kits | fix(kits): correct Pi power specs, Pi Zero 2W CPU, and Nicla Vision memory |
| 2026-05-27 | [#1785](https://github.com/harvard-edge/cs249r_book/pull/1785) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): fix GPT causal mask convention in module 13 |
| 2026-05-27 | [#1786](https://github.com/harvard-edge/cs249r_book/pull/1786) | Shashank-Tripathi-07 | Labs | fix(labs-vol2): correct numerical bugs in labs 08, 09, 11, 14 |
| 2026-05-27 | [#1791](https://github.com/harvard-edge/cs249r_book/pull/1791) | dependabot | Dependencies | build(deps): bump ws from 8.20.0 to 8.20.1 |
| 2026-05-27 | [#1797](https://github.com/harvard-edge/cs249r_book/pull/1797) | farhan523 | StaffML | fix(staffml): make mobile ecosystem nav menu scroll internally |
| 2026-05-27 | [#1798](https://github.com/harvard-edge/cs249r_book/pull/1798) | farhan523 | Book | fix(book): wrap long URLs to stop horizontal page overflow |
| 2026-05-27 | [#1799](https://github.com/harvard-edge/cs249r_book/pull/1799) | farhan523 | Book | fix(book): contain wide tables in their own scroll instead of widening the page |
| 2026-05-27 | [#1809](https://github.com/harvard-edge/cs249r_book/pull/1809) | dependabot | StaffML | chore(deps): bump ws and wrangler in /interviews/staffml-vault-worker |
| 2026-05-28 | [#1811](https://github.com/harvard-edge/cs249r_book/pull/1811) | hzeljko | Book | Refined the PDF layout for chapter 10 (model_compression.qmd) |
| 2026-05-28 | [#1816](https://github.com/harvard-edge/cs249r_book/pull/1816) | hzeljko | Book | Refined the PDF layout for chapter 11 (hw_acceleration.qmd) |
| 2026-05-28 | [#1817](https://github.com/harvard-edge/cs249r_book/pull/1817) | hzeljko | Book | Refined the PDF layout for chapter 12 (benchmarking.qmd) |
| 2026-05-28 | [#1818](https://github.com/harvard-edge/cs249r_book/pull/1818) | hzeljko | Book | Refined the PDF layout for chapter 13 (model_serving.qmd) |
| 2026-06-02 | [#1819](https://github.com/harvard-edge/cs249r_book/pull/1819) | farhan523 | StaffML | fix(staffml): reset QuestionVisual failed state on visual.path change |
| 2026-06-02 | [#1820](https://github.com/harvard-edge/cs249r_book/pull/1820) | farhan523 | StaffML | fix(staffml): drop misleading role=button from MetaTooltip and stop nesting it inside a real button |
| 2026-06-02 | [#1821](https://github.com/harvard-edge/cs249r_book/pull/1821) | farhan523 | StaffML | fix(staffml): give ChainStrip dots accessible names and a current-step marker |
| 2026-06-02 | [#1823](https://github.com/harvard-edge/cs249r_book/pull/1823) | kai4avaya | Other/root | refactor(community): improve map legend and data fetching logic |
| 2026-06-02 | [#1824](https://github.com/harvard-edge/cs249r_book/pull/1824) | kai4avaya | Other/root | Kai/fixing profile setting and map |
| 2026-06-02 | [#1826](https://github.com/harvard-edge/cs249r_book/pull/1826) | hzeljko | Book | Refined the PDF layout for chapter 14 (ml_ops.qmd) |
| 2026-06-02 | [#1828](https://github.com/harvard-edge/cs249r_book/pull/1828) | hzeljko | Book | Refined the PDF layout for chapter 15 (responsible_engr.qmd) |
| 2026-06-02 | [#1829](https://github.com/harvard-edge/cs249r_book/pull/1829) | hzeljko | Book | Refined the PDF layout for chapter 16 (conclusion.qmd) |
| 2026-06-02 | [#1830](https://github.com/harvard-edge/cs249r_book/pull/1830) | hzeljko | Book | Refined the PDF layout for Appendix A (appendix_dam.qmd) |
| 2026-06-02 | [#1831](https://github.com/harvard-edge/cs249r_book/pull/1831) | hzeljko | Book | Refined the PDF layout for Appendix B (appendix_data.qmd) |
| 2026-06-03 | [#1836](https://github.com/harvard-edge/cs249r_book/pull/1836) | hzeljko | Book | Refined the PDF layout for Appendix D (appendix_machine.qmd) |
| 2026-06-03 | [#1837](https://github.com/harvard-edge/cs249r_book/pull/1837) | hzeljko | Book | Refined the PDF layout for Appendix E (appendix_assumptions.qmd) |
| 2026-06-05 | [#1835](https://github.com/harvard-edge/cs249r_book/pull/1835) | hzeljko | Book | Refined the PDF layout for Appendix C (appendix_algorithm.qmd) |
| 2026-06-05 | [#1838](https://github.com/harvard-edge/cs249r_book/pull/1838) | hzeljko | Other/root | Update Volume I front matter for MIT Press requirements |
| 2026-06-05 | [#1839](https://github.com/harvard-edge/cs249r_book/pull/1839) | hzeljko | Book | Refined the PDF layout for chapter 1 (introduction.qmd) |
| 2026-06-05 | [#1840](https://github.com/harvard-edge/cs249r_book/pull/1840) | hzeljko | Book | Refined the PDF layout for chapter 2 (ml_systems.qmd) |
| 2026-06-05 | [#1841](https://github.com/harvard-edge/cs249r_book/pull/1841) | hzeljko | Book | Refined the PDF layout for chapter 3 (ml_workflow.qmd) |
| 2026-06-05 | [#1842](https://github.com/harvard-edge/cs249r_book/pull/1842) | hzeljko | Book | Refined the PDF layout fogit branchr chapter 4 (data_engineering.qmd) |
| 2026-06-09 | [#1846](https://github.com/harvard-edge/cs249r_book/pull/1846) | hzeljko | Book | Refined the PDF layout for chapter 5 (nn_computation.qmd) |
| 2026-06-09 | [#1847](https://github.com/harvard-edge/cs249r_book/pull/1847) | hzeljko | Book | Refined the PDF layout for chapter 6 (nn_architectures.qmd) |
| 2026-06-09 | [#1850](https://github.com/harvard-edge/cs249r_book/pull/1850) | hzeljko | Book | Refined the PDF layout for chapter 8 (training.qmd) |
| 2026-06-09 | [#1852](https://github.com/harvard-edge/cs249r_book/pull/1852) | hzeljko | Book | Refined the PDF layout for chapter 9 (data_selection.qmd) |
| 2026-06-10 | [#1832](https://github.com/harvard-edge/cs249r_book/pull/1832) | octo-patch | Other/root | feat(captions): upgrade MiniMax example to M3 default |
| 2026-06-10 | [#1833](https://github.com/harvard-edge/cs249r_book/pull/1833) | farhan523 | StaffML | fix(staffml): pin bootstrap-icons CSS with SRI + crossorigin |
| 2026-06-10 | [#1834](https://github.com/harvard-edge/cs249r_book/pull/1834) | farhan523 | StaffML | fix(staffml): pause Nav due-count poll on hidden tabs + sync across tabs |
| 2026-06-10 | [#1843](https://github.com/harvard-edge/cs249r_book/pull/1843) | farhan523 | StaffML | fix(staffml): derive PaperCitationCard year from buildDate, not `new Date()` |
| 2026-06-10 | [#1845](https://github.com/harvard-edge/cs249r_book/pull/1845) | farhan523 | StaffML | fix(staffml): trap focus inside the AskInterviewer waitlist modal |
| 2026-06-16 | [#1851](https://github.com/harvard-edge/cs249r_book/pull/1851) | farhan523 | StaffML | feat(staffml): link each question to recommended textbook reading (topic → chapter) |
| 2026-06-16 | [#1856](https://github.com/harvard-edge/cs249r_book/pull/1856) | farhan523 | StaffML | fix(staffml): mount the command palette and shortcuts overlay |
| 2026-06-16 | [#1857](https://github.com/harvard-edge/cs249r_book/pull/1857) | farhan523 | StaffML | fix(staffml): keep command-palette active row in view during keyboard nav |
| 2026-06-16 | [#1858](https://github.com/harvard-edge/cs249r_book/pull/1858) | farhan523 | StaffML | fix(staffml): announce toasts to assistive tech and label the dismiss button |
| 2026-06-16 | [#1859](https://github.com/harvard-edge/cs249r_book/pull/1859) | profvjreddi | Labs | fix(ci): stage mlsysbook-labs wheel for the WASM browser smoke test |
| 2026-06-16 | [#1861](https://github.com/harvard-edge/cs249r_book/pull/1861) | dependabot | StaffML | build(deps): bump vite from 8.0.6 to 8.0.16 in /interviews/staffml |
| 2026-06-16 | [#1864](https://github.com/harvard-edge/cs249r_book/pull/1864) | dependabot | Dependencies | build(deps): bump js-yaml from 4.1.1 to 4.2.0 |
| 2026-06-16 | [#1866](https://github.com/harvard-edge/cs249r_book/pull/1866) | Shashank-Tripathi-07 | Other/root | fix(conv2d): Conv2dBackward return tuple must match saved_tensors length |
| 2026-06-16 | [#1868](https://github.com/harvard-edge/cs249r_book/pull/1868) | Shashank-Tripathi-07 | Other/root | fix(conv): remove misleading super().__init__() on bare Conv2d/MaxPool2d/AvgPool2d |
| 2026-06-16 | [#1869](https://github.com/harvard-edge/cs249r_book/pull/1869) | Shashank-Tripathi-07 | Other/root | fix(layers): Dropout uses global unseeded np.random instead of module rng |
| 2026-06-16 | [#1871](https://github.com/harvard-edge/cs249r_book/pull/1871) | Shashank-Tripathi-07 | Other/root | fix(autograd): tracked_mul passes raw scalar to MulBackward, causing .data crash on backward |
| 2026-06-16 | [#1872](https://github.com/harvard-edge/cs249r_book/pull/1872) | Shashank-Tripathi-07 | Other/root | fix(tensor): reshape -1 silently truncates when size is not divisible |
| 2026-06-16 | [#1873](https://github.com/harvard-edge/cs249r_book/pull/1873) | Shashank-Tripathi-07 | Other/root | fix(tensor): transpose test uses symmetric tensor that can't detect wrong axis swap |
| 2026-06-16 | [#1876](https://github.com/harvard-edge/cs249r_book/pull/1876) | Shashank-Tripathi-07 | CI/Infra | fix CI: add [tool.nbdev] to pyproject.toml so nbdev finds its config without falling back to settings.ini |
| 2026-06-16 | [#1879](https://github.com/harvard-edge/cs249r_book/pull/1879) | profvjreddi | StaffML | style(vault-cli): sort imports in legacy_export.py (ruff I001) |
| 2026-06-16 | [#1880](https://github.com/harvard-edge/cs249r_book/pull/1880) | profvjreddi | SocratiQ | build(deps): bump socratiq markdown-it + dompurify, rebuild bundle |
| 2026-06-16 | [#1881](https://github.com/harvard-edge/cs249r_book/pull/1881) | profvjreddi | StaffML | deps(vault-worker): bump vitest 4.1.5 -> 4.1.6 |
| 2026-06-16 | [#1883](https://github.com/harvard-edge/cs249r_book/pull/1883) | profvjreddi | SocratiQ | build(deps-dev): bump socratiq vite 8.0.10 -> 8.0.16, rebuild bundle |
| 2026-06-17 | [#1882](https://github.com/harvard-edge/cs249r_book/pull/1882) | dependabot | design-grammar | build(deps): bump js-yaml from 4.1.1 to 4.2.0 in /design-grammar |
| 2026-06-17 | [#1884](https://github.com/harvard-edge/cs249r_book/pull/1884) | profvjreddi | StaffML | ci(staffml): re-run preview-dev on vault-cli changes so the badge can't stale |
| 2026-06-21 | [#1887](https://github.com/harvard-edge/cs249r_book/pull/1887) | farhan523 | Other/root | fix(community): repair profile setup form + dashboard access for email logins |
| 2026-06-21 | [#1889](https://github.com/harvard-edge/cs249r_book/pull/1889) | dependabot | StaffML | build(deps): bump undici and wrangler in /interviews/staffml/worker |
| 2026-06-21 | [#1890](https://github.com/harvard-edge/cs249r_book/pull/1890) | dependabot | StaffML | build(deps): bump undici from 7.25.0 to 7.28.0 in /interviews/staffml |
| 2026-06-21 | [#1891](https://github.com/harvard-edge/cs249r_book/pull/1891) | dependabot | StaffML | build(deps): bump undici and wrangler in /interviews/staffml-vault-worker |
| 2026-06-24 | [#1894](https://github.com/harvard-edge/cs249r_book/pull/1894) | farhan523 | Book | fix(book): render pseudocode algorithms and contain their overflow |
| 2026-06-25 | [#1895](https://github.com/harvard-edge/cs249r_book/pull/1895) | hzeljko | Book | Refine PDF layout for front matter pages and Chapter 1 |
| 2026-06-25 | [#1896](https://github.com/harvard-edge/cs249r_book/pull/1896) | hzeljko | Book | Refined the PDF layout for chapter 2 (ml_systems.qmd) |
| 2026-06-25 | [#1898](https://github.com/harvard-edge/cs249r_book/pull/1898) | hzeljko | Book | Refined the PDF layout for chapter 3 (ml_workflow.qmd) |
| 2026-06-25 | [#1899](https://github.com/harvard-edge/cs249r_book/pull/1899) | hzeljko | Book | Refined the PDF layout for chapter 4 (data_engineering.qmd) |
| 2026-06-25 | [#1900](https://github.com/harvard-edge/cs249r_book/pull/1900) | hzeljko | Book | Refined the PDF layout for chapter 5 (nn_computation.qmd) |
| 2026-06-27 | [#1908](https://github.com/harvard-edge/cs249r_book/pull/1908) | hzeljko | Book | Refined the PDF layout for chapter 6 (nn_architectures.qmd) |
| 2026-06-27 | [#1909](https://github.com/harvard-edge/cs249r_book/pull/1909) | hzeljko | Book | Refined the PDF layout for chapter 7 (frameworks.qmd) |
| 2026-06-29 | [#1910](https://github.com/harvard-edge/cs249r_book/pull/1910) | hzeljko | Book | Refined the PDF layout for chapter 8 (training.qmd) |
| 2026-06-29 | [#1914](https://github.com/harvard-edge/cs249r_book/pull/1914) | hzeljko | Book | Refined the PDF layout for chapter 9 (data_selection.qmd) |
| 2026-06-29 | [#1916](https://github.com/harvard-edge/cs249r_book/pull/1916) | hzeljko | Book | Refined the PDF layout for chapter 10 (model_compression.qmd) |
| 2026-06-30 | [#1918](https://github.com/harvard-edge/cs249r_book/pull/1918) | hzeljko | Book | Refined the PDF layout for chapter 11 (hw_acceleration.qmd) |
| 2026-06-30 | [#1919](https://github.com/harvard-edge/cs249r_book/pull/1919) | hzeljko | Book | Refined the PDF layout for chapter 12 (benchmarking.qmd) |
| 2026-07-02 | [#1915](https://github.com/harvard-edge/cs249r_book/pull/1915) | dependabot | StaffML | chore(deps-dev): bump js-yaml from 4.1.1 to 4.2.0 in /interviews/staffml |
| 2026-07-02 | [#1920](https://github.com/harvard-edge/cs249r_book/pull/1920) | hzeljko | Book | Refined the PDF layout for chapter 13 (model_serving.qmd) |
| 2026-07-02 | [#1921](https://github.com/harvard-edge/cs249r_book/pull/1921) | hzeljko | Book | Refined the PDF layout for chapter 14 (ml_ops.qmd) |
| 2026-07-03 | [#1927](https://github.com/harvard-edge/cs249r_book/pull/1927) | hzeljko | Book | Refined the PDF layout for chapter 15 (responsible_engr.qmd) |
| 2026-07-03 | [#1928](https://github.com/harvard-edge/cs249r_book/pull/1928) | hzeljko | Book | Refined the PDF layout for chapter 16 (conclusion.qmd) |
| 2026-07-04 | [#1929](https://github.com/harvard-edge/cs249r_book/pull/1929) | hzeljko | Other/root | Refined the PDF layout for Appendices A to E |
| 2026-07-10 | [#1911](https://github.com/harvard-edge/cs249r_book/pull/1911) | farhan523 | StaffML | fix(staffml): format Footer build date in UTC to avoid hydration mismatch |
| 2026-07-10 | [#1912](https://github.com/harvard-edge/cs249r_book/pull/1912) | farhan523 | StaffML | fix(staffml): keep practice-page Cmd+Enter handler fresh via latest-callback refs |
| 2026-07-10 | [#1913](https://github.com/harvard-edge/cs249r_book/pull/1913) | farhan523 | StaffML | fix(staffml): cancel StarGate's verify-delay timer on unmount |
| 2026-07-10 | [#1931](https://github.com/harvard-edge/cs249r_book/pull/1931) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): quality-target check silently skipped for real submissions |
| 2026-07-10 | [#1932](https://github.com/harvard-edge/cs249r_book/pull/1932) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): remove vacuous deterministic-seed check from quick_check_model |
| 2026-07-10 | [#1934](https://github.com/harvard-edge/cs249r_book/pull/1934) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): CSV leaderboard export crashes on mixed grade result shapes |
| 2026-07-10 | [#1936](https://github.com/harvard-edge/cs249r_book/pull/1936) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): p99 decode-latency formula wrong for small sample counts |
| 2026-07-10 | [#1938](https://github.com/harvard-edge/cs249r_book/pull/1938) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): plateau detection mixes losses across NAS attempts |
| 2026-07-10 | [#1939](https://github.com/harvard-edge/cs249r_book/pull/1939) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): NameError masks real model-load failure in auto_trainer |
| 2026-07-10 | [#1940](https://github.com/harvard-edge/cs249r_book/pull/1940) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): dataset download race on shared fixed temp filename |
| 2026-07-10 | [#1941](https://github.com/harvard-edge/cs249r_book/pull/1941) | Shashank-Tripathi-07 | design-grammar | fix(design-grammar): rewrite-rules.yml was never validated at all |
| 2026-07-10 | [#1942](https://github.com/harvard-edge/cs249r_book/pull/1942) | Shashank-Tripathi-07 | design-grammar | fix(design-grammar): known_collisions notes cite wrong (row, col) coordinates |
| 2026-07-10 | [#1943](https://github.com/harvard-edge/cs249r_book/pull/1943) | Shashank-Tripathi-07 | design-grammar | fix(design-grammar): Fc incorrectly bundled into sharding's symbols |
| 2026-07-10 | [#1946](https://github.com/harvard-edge/cs249r_book/pull/1946) | farhan523 | SocratiQ | fix(socratiq): correct IndexedDB store name and key in getIncorrectQuestions() |
| 2026-07-10 | [#1947](https://github.com/harvard-edge/cs249r_book/pull/1947) | farhan523 | Book | perf(book): stop unbounded full-document rescans in quiz numbering fix |
| 2026-07-10 | [#1949](https://github.com/harvard-edge/cs249r_book/pull/1949) | farhan523 | Site | fix(site): make All-Reduce Rhythm chunks animate, taps flash, and game clean up |
| 2026-07-10 | [#1950](https://github.com/harvard-edge/cs249r_book/pull/1950) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): nbgrader generate can silently overwrite a student's notebook |
| 2026-07-10 | [#1952](https://github.com/harvard-edge/cs249r_book/pull/1952) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): UnboundLocalError when reading an HTTP error body fails |
| 2026-07-10 | [#1953](https://github.com/harvard-edge/cs249r_book/pull/1953) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): cached generation step excludes current token from self-attention |
| 2026-07-10 | [#1955](https://github.com/harvard-edge/cs249r_book/pull/1955) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): capstone latency divisions crash on coarse timer resolution |
| 2026-07-12 | [#1957](https://github.com/harvard-edge/cs249r_book/pull/1957) | hzeljko | Other/root | Finalize Volume I layout for full PDF draft |
| 2026-07-15 | [#1933](https://github.com/harvard-edge/cs249r_book/pull/1933) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): stop silently bypassing anti-cheat when mlperf CLI is missing |
| 2026-07-15 | [#1935](https://github.com/harvard-edge/cs249r_book/pull/1935) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): SLM decode metrics polluted by generate()'s internal prefill |
| 2026-07-15 | [#1937](https://github.com/harvard-edge/cs249r_book/pull/1937) | Shashank-Tripathi-07 | MLPerf EDU | fix(mlperf-edu): anomaly-ae-train model-size gate impossible to pass |
| 2026-07-15 | [#1948](https://github.com/harvard-edge/cs249r_book/pull/1948) | farhan523 | Other/root | a11y(shared): make subscribe modal a real dialog for keyboard and screen-reader users |
| 2026-07-15 | [#1954](https://github.com/harvard-edge/cs249r_book/pull/1954) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): unguarded accuracy_retention division crashes on zero baseline |
| 2026-07-15 | [#1959](https://github.com/harvard-edge/cs249r_book/pull/1959) | coyaSONG | Instructors | docs(instructors): fix broken lab links |
| 2026-08-03 | [#1951](https://github.com/harvard-edge/cs249r_book/pull/1951) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): integration test collection failure silently treated as no-tests |
| 2026-08-03 | [#1966](https://github.com/harvard-edge/cs249r_book/pull/1966) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito CLI crashes on Windows due to no-op encoding fix |
| 2026-08-03 | [#1967](https://github.com/harvard-edge/cs249r_book/pull/1967) | dependabot | StaffML | Bump next from 16.2.6 to 16.2.11 in /interviews/staffml |
| 2026-08-03 | [#1968](https://github.com/harvard-edge/cs249r_book/pull/1968) | dependabot | SocratiQ | Bump seroval from 1.5.2 to 1.5.6 in /socratiq |
| 2026-08-03 | [#1969](https://github.com/harvard-edge/cs249r_book/pull/1969) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): installer can hang forever with no error on Windows |
| 2026-08-03 | [#1970](https://github.com/harvard-edge/cs249r_book/pull/1970) | dependabot | design-grammar | Bump js-yaml from 4.2.0 to 4.3.0 in /design-grammar |
| 2026-08-03 | [#1972](https://github.com/harvard-edge/cs249r_book/pull/1972) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito olympics logo crashes with NameError |
| 2026-08-03 | [#1973](https://github.com/harvard-edge/cs249r_book/pull/1973) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): benchmark capstone always reports Module 20 as incomplete |
| 2026-08-03 | [#1974](https://github.com/harvard-edge/cs249r_book/pull/1974) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito dev clean/build crash with raw traceback when make is missing |
| 2026-08-03 | [#1975](https://github.com/harvard-edge/cs249r_book/pull/1975) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito package nbdev --test/--clean/--build-docs crash, wrong command names |
| 2026-08-03 | [#1979](https://github.com/harvard-edge/cs249r_book/pull/1979) | Shashank-Tripathi-07 | TinyTorch | fix: swap Acceleration/Memoization descriptions on TinyTorch dashboard |
| 2026-08-03 | [#1980](https://github.com/harvard-edge/cs249r_book/pull/1980) | Shashank-Tripathi-07 | StaffML | fix: allow GitHub Pages origin in vault worker CORS allowlist |
| 2026-08-03 | [#1982](https://github.com/harvard-edge/cs249r_book/pull/1982) | Shashank-Tripathi-07 | StaffML | fix(staffml): make local-corpus vault-CLI probe work on Windows |
| 2026-08-03 | [#1983](https://github.com/harvard-edge/cs249r_book/pull/1983) | Shashank-Tripathi-07 | StaffML | fix(staffml): fix static-corpus fetch race and unhandled rejection in Gauntlet |
| 2026-08-03 | [#1984](https://github.com/harvard-edge/cs249r_book/pull/1984) | dependabot | SocratiQ | Bump postcss from 8.5.15 to 8.5.25 in /socratiq |
| 2026-08-03 | [#1986](https://github.com/harvard-edge/cs249r_book/pull/1986) | imrehg | Kits | fix(kits): correct table rendering |
| 2026-08-10 | [#1917](https://github.com/harvard-edge/cs249r_book/pull/1917) | vedant-a-joshi | TinyTorch | tinytorch module 09 diagrams and spacing |
| 2026-08-10 | [#1924](https://github.com/harvard-edge/cs249r_book/pull/1924) | nyxst4ck | Other/root | docs: quote pip extras install examples |
| 2026-08-10 | [#1945](https://github.com/harvard-edge/cs249r_book/pull/1945) | farhan523 | StaffML | feat(staffml): make the app an installable PWA |
| 2026-08-10 | [#1962](https://github.com/harvard-edge/cs249r_book/pull/1962) | DIYA73 | StaffML | feat(staffml): add recent-papers section to About page |
| 2026-08-10 | [#1963](https://github.com/harvard-edge/cs249r_book/pull/1963) | dependabot | SocratiQ | Bump linkify-it from 5.0.1 to 5.0.2 in /socratiq |
| 2026-08-10 | [#1965](https://github.com/harvard-edge/cs249r_book/pull/1965) | dependabot | StaffML | Bump sharp and wrangler in /interviews/staffml-vault-worker |
| 2026-08-10 | [#1971](https://github.com/harvard-edge/cs249r_book/pull/1971) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito crashes on Windows when a subprocess's output can't be decoded as cp1252 |
| 2026-08-10 | [#1978](https://github.com/harvard-edge/cs249r_book/pull/1978) | farhan523 | SocratiQ | fix(socratiq): enable AI companion and free Ctrl+/ from search |
| 2026-08-10 | [#1981](https://github.com/harvard-edge/cs249r_book/pull/1981) | Shashank-Tripathi-07 | StaffML | fix(staffml): restore working eslint config and fix errors it surfaces |
| 2026-08-10 | [#1988](https://github.com/harvard-edge/cs249r_book/pull/1988) | aadityansha06 | Labs | fix(labs): stop silently dropping WASM DesignLedger saves (#1985) |
| 2026-08-10 | [#1989](https://github.com/harvard-edge/cs249r_book/pull/1989) | dependabot | Dependencies | Bump ip-address from 10.2.0 to 10.4.0 |
| 2026-08-10 | [#1990](https://github.com/harvard-edge/cs249r_book/pull/1990) | dependabot | StaffML | Bump undici from 7.28.0 to 7.29.0 in /interviews/staffml |
| 2026-08-10 | [#1992](https://github.com/harvard-edge/cs249r_book/pull/1992) | dependabot | MLPerf EDU | Bump aiohttp from 3.14.1 to 3.14.3 in /mlperf-edu |
| 2026-08-10 | [#1993](https://github.com/harvard-edge/cs249r_book/pull/1993) | dependabot | MLPerf EDU | Bump cryptography from 49.0.0 to 50.0.0 in /mlperf-edu |
| 2026-08-10 | [#1996](https://github.com/harvard-edge/cs249r_book/pull/1996) | dependabot | SocratiQ | Bump mermaid from 11.15.0 to 11.16.1 in /socratiq |
| 2026-08-10 | [#1997](https://github.com/harvard-edge/cs249r_book/pull/1997) | dependabot | SocratiQ | build(deps): bump dompurify from 3.4.11 to 3.4.13 in /socratiq |
| 2026-08-10 | [#1999](https://github.com/harvard-edge/cs249r_book/pull/1999) | dependabot | StaffML | build(deps-dev): bump nanoid from 3.3.12 to 3.3.18 in /interviews/staffml-vault-worker |
| 2026-08-10 | [#2000](https://github.com/harvard-edge/cs249r_book/pull/2000) | dependabot | MLPerf EDU | build(deps): bump pymdown-extensions from 10.21.3 to 11.0.1 in /mlperf-edu |
| 2026-08-10 | [#2001](https://github.com/harvard-edge/cs249r_book/pull/2001) | dependabot | MLPerf EDU | build(deps): bump pypdf from 6.14.2 to 6.15.0 in /mlperf-edu |
| 2026-08-10 | [#2002](https://github.com/harvard-edge/cs249r_book/pull/2002) | hzeljko | Other/root | Apply Volume I publisher corrections for second draft |
| 2026-08-10 | [#2003](https://github.com/harvard-edge/cs249r_book/pull/2003) | Fabio-RibeiroB | TinyTorch | docs(tinytorch): update outdated export commands |
| 2026-08-10 | [#2004](https://github.com/harvard-edge/cs249r_book/pull/2004) | dependabot | design-grammar | build(deps): bump js-yaml from 4.3.0 to 4.3.1 in /design-grammar |
| 2026-08-11 | [#2012](https://github.com/harvard-edge/cs249r_book/pull/2012) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito login does not exist, real command is tito community login |
| 2026-08-11 | [#2014](https://github.com/harvard-edge/cs249r_book/pull/2014) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito benchmark baseline crashes with raw EOFError, NameError on 'no' |
| 2026-08-11 | [#2016](https://github.com/harvard-edge/cs249r_book/pull/2016) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito system health's Module Status section shows the wrong thing |
| 2026-08-11 | [#2018](https://github.com/harvard-edge/cs249r_book/pull/2018) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): milestone achievement panel claims autograd before it's built |
| 2026-08-11 | [#2019](https://github.com/harvard-edge/cs249r_book/pull/2019) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito milestone info duplicates the year in its title |
| 2026-08-14 | [#2006](https://github.com/harvard-edge/cs249r_book/pull/2006) | Shashank-Tripathi-07 | StaffML | docs(staffml): document cancel-in-progress badge behavior |
| 2026-08-14 | [#2008](https://github.com/harvard-edge/cs249r_book/pull/2008) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito module complete silently skips integration tests for 5 modules |
| 2026-08-14 | [#2009](https://github.com/harvard-edge/cs249r_book/pull/2009) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito milestone status falsely claims full mastery |
| 2026-08-14 | [#2010](https://github.com/harvard-edge/cs249r_book/pull/2010) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito module start/view falsely report success on a missing notebook |
| 2026-08-14 | [#2013](https://github.com/harvard-edge/cs249r_book/pull/2013) | Shashank-Tripathi-07 | TinyTorch | fix(tinytorch): tito setup ignores TINYTORCH_NON_INTERACTIVE, crashes with EOFError |
| 2026-08-14 | [#2024](https://github.com/harvard-edge/cs249r_book/pull/2024) | Shashank-Tripathi-07 | StaffML | fix(staffml): correct simulator training-time units and tablet nav overflow |
| 2026-08-14 | [#2025](https://github.com/harvard-edge/cs249r_book/pull/2025) | Shashank-Tripathi-07 | TinyTorch | docs: fix nonexistent tito src export command in CONTRIBUTING.md |
| 2026-08-14 | [#2027](https://github.com/harvard-edge/cs249r_book/pull/2027) | dependabot | Dependencies | chore(deps): bump nltk from 3.9.2 to 3.10.0 |
| 2026-08-14 | [#2028](https://github.com/harvard-edge/cs249r_book/pull/2028) | dependabot | Dependencies | chore(deps): bump cryptography from 47.0.0 to 50.0.0 |

</details>

---

*Generated from a full paginated GraphQL pull against `harvard-edge/cs249r_book`'s merged PR history, run 2026-08-21. Sub-project and change-type categorization is a title-keyword heuristic and will misclassify some genuinely ambiguous or generically-titled PRs, treat category/kind columns as approximate, dates/authors/line-counts as exact.*
