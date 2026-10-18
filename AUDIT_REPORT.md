# Matric Mate Pakistan — Audit Findings & Resolution Backlog

**Project:** Matric Mate Pakistan  
**Target:** Android exam-preparation app for Sindh Board (Karachi), Class 9–10, Urdu + English  
**Status:** AUDIT PAUSED at user's request; no code/content/config changes were made during this audit phase.  
**Workflow requested:** Record known issues first, then resolve them one at a time, verify each fix, and only after the agreed backlog is resolved build/download/install/test the APK. Preserve existing working features.

## How to use this file
- Treat this as the central issue/backlog file.
- Resolve items in priority order, one step at a time.
- Do not delete root-level files, backups, APK/ZIP artifacts, or change app code until the relevant issue is selected for resolution.
- Items marked **Needs runtime verification** are source-inspection findings, not confirmed end-user failures.

## Priority 0 — Build/package correctness

### P0-1. Flutter asset manifest mismatch
- **Finding:** `assets/web/index.html` references `simultaneous-solver.js`, but `pubspec.yaml` was found to omit `assets/web/simultaneous-solver.js`.
- **Risk:** Android WebView may fail to load the simultaneous-equation solver in the packaged APK even if browser preview works.
- **Status:** Configuration gap identified; APK runtime failure not yet confirmed.
- **Resolution/verification:** Check exact current `pubspec.yaml` and HTML script path; add the asset only when resolving this item; run Flutter asset/build checks; test simultaneous equations in the built APK.

## Priority 1 — Core learning content and functionality

### P1-1. Chapter-wise study content is very limited
- **Finding:** Audit found only one study-content entry: Class 9 Mathematics, “1. Real and Complex Numbers,” with 4 English and 4 Urdu MCQs.
- **Risk:** Most chapter selections will show fallback messages or no chapter questions.
- **Status:** Confirmed by source/data inspection.
- **Resolution:** Create a coverage matrix for every supported class/subject/chapter before adding content; verify content against the correct Sindh Board syllabus/model papers; then add content incrementally in both languages and validate answer/explanation accuracy.

### P1-2. Chapter lists missing for most subjects
- **Finding:** Class 9 and 10 chapter lists exist for Mathematics (16 and 14 chapters) and Biology (9 each). Lists were not found for Physics, Chemistry, Computer Science, English, Urdu, Islamiat, or Pakistan Studies.
- **Risk:** Study Resources and planner chapter selectors cannot offer chapters for those subjects.
- **Status:** Confirmed by source/data inspection.
- **Resolution:** Verify official/current Sindh Board chapter lists, then add one subject at a time and test selector-to-content matching. Do not invent chapter names.

### P1-3. Chapter-title consistency needs verification
- **Finding:** Some Mathematics chapter strings may differ between the roadmap, syllabus labels, and data (for example “Algebraic Expression and Formulas,” “Algebraic Manipulation,” “Linear Equation and Inequalities”).
- **Risk:** Exact-string content lookup may fail even when similar content exists.
- **Status:** Potential issue; verify actual strings across data files before changing.
- **Resolution:** Build a comparison table and standardize names only after syllabus verification.

### P1-4. General Practice quiz is structurally valid, but content quality is not fully verified
- **Finding:** 24 questions total: Mathematics 4, Biology 4, Physics 4, Chemistry 8, English 4. Structural validation found 0 issues (question, options, answer-in-options, explanation present).
- **Risk:** Structural checks do not confirm scientific accuracy, board alignment, Urdu translation, or explanation quality. Chemistry includes Class 9 and Class 10 topics mixed. Some explanation strings have missing spaces (e.g. “protectthe body”, “velocityis constant”, “chemical identity”, “atom'snucleus”).
- **Status:** Structural validation passed; academic/language QA pending.
- **Resolution:** Review correctness, class/board alignment, answer explanations, and Urdu/English language quality; fix spacing as part of that approved task.

### P1-5. Language toggle requires runtime verification
- **Finding:** Source implements English/Urdu toggle, RTL/LTR mode, and localStorage persistence; click handler also re-renders questions.
- **Risk:** Not all sections, solver output, controls, and persisted language have been verified in the running app.
- **Status:** Source inspection only; runtime behavior pending.
- **Resolution:** Test toggle on dashboard, resources, chapter/general quiz, planner, and solver; reload and confirm saved language; inspect Urdu RTL layout and translations.

### P1-6. Math Solver parser/coverage limitations
- **Finding:** Polynomial solver supports basic linear and quadratic forms but parser appears limited for parentheses, expanded expressions, and unusual coefficient formats. Simultaneous solver expects two equations separated by semicolon and standard forms such as `a*x + b*y = c`; parser may not support all decimal or `2*x` forms.
- **Risk:** Students may enter valid-looking Class 9–10 equations that fail or receive misleading steps.
- **Status:** Source inspection; broader runtime edge-case tests pending.
- **Resolution:** Define supported input formats; test positive/negative/fraction/decimal coefficients, variable terms on both sides, identities/no-solution cases, repeated roots, no-real roots, malformed input, and Urdu output. Expand parser only after tests are defined.
- **Additional finding:** `solveBasicAlgebra()` appears defined once but has no call sites; review whether it is obsolete or intended before making any change.
- **Additional finding:** Urdu solver translation includes a Devanagari glyph in the string `"مرحلہ $1: عددی سرِ गुण معلوم کریں۔"`; language QA required.
- **Additional risk to review:** Arithmetic solver uses `Function(...)` to evaluate an input expression. Review input safety and replace with a restricted arithmetic parser if needed; do not change until this item is selected and tested.
- **Existing test evidence:** `node --check` passed for `script.js`, `simultaneous-solver.js`, and `study-content.js`. Existing simultaneous-equation tests passed for basic unique solutions, fractional answers, infinite/no solution, and invalid input. This does not cover the full solver UI.

## Priority 2 — Planner and dashboard behavior

### P2-1. Dashboard metrics are hardcoded
- **Finding:** HTML hardcodes “12” chapters ready, “7-day” streak, “84%” accuracy, “72% complete”, “25 min”, and “3” practice sets; no clear dynamic learner-progress update was found in source inspection.
- **Risk:** Numbers may look like actual student progress even when they are placeholders.
- **Status:** Source inspection; runtime behavior pending.
- **Resolution:** Decide whether to label them as sample/demo metrics or calculate them from real saved data; verify labels and numbers in both languages. Do not claim live progress unless it is truly calculated.

### P2-2. Weekly Planner chapter selection is incomplete
- **Finding:** Planner chapter selection depends on chapter data, and the UI says chapter titles will be enabled after textbook verification. If unavailable, it can save “Chapter list pending verification.”
- **Risk:** Planner entries may not point to real chapters.
- **Status:** Source inspection; runtime tests pending.
- **Resolution:** Verify subject/chapter choices; test edit, save, cancel, validation, reload persistence, and offline behavior.

### P2-3. General Practice result persistence is limited
- **Finding:** General Practice stores one score/total/completed record per subject in localStorage, overwritten by the next attempt; no timestamp or attempt history. Chapter-wise results are intentionally not saved.
- **Risk:** Students cannot view history or compare past attempts.
- **Status:** Source inspection; confirm whether this is intended product scope.
- **Resolution:** Decide desired behavior; test score display and persistence. Do not add history until approved.

## Priority 3 — Project maintenance and documentation

### P3-1. Root web files differ from active Android web assets
- **Finding:** Root `index.html`, `script.js`, and `styles.css` differ from the active `assets/web/` versions. Root HTML was still branded “Examprep App”; Android loads `assets/web/index.html`.
- **Risk:** Future edits may target inactive files and not affect the APK, or cause confusion.
- **Status:** Confirmed source mismatch.
- **Resolution:** Document the active source-of-truth paths. Decide later whether root files should be synchronized, retained, or archived; do not delete them without approval.

### P3-2. README/ROADMAP documentation is stale
- **Finding:** README still uses the old “Examprep App” name. ROADMAP reportedly says board confirmation is pending, although the current agreed spec is Sindh Board Karachi, Classes 9–10, Urdu + English. Existing audit report also contains an outdated statement that `subjects-data.js` and `study-content.js` are missing from `pubspec.yaml`; current inspection found those two included.
- **Risk:** Documentation does not reflect current app scope or latest audit findings.
- **Status:** Confirmed stale documentation.
- **Resolution:** Update docs after the technical audit backlog is recorded and before final release documentation.

### P3-3. Git working tree contains many untracked files and build artifacts
- **Finding:** `git status --short` showed many untracked project files/directories and multiple APK/ZIP artifacts; `.gitignore` did not appear to ignore `*.apk` or `*.zip`. Earlier size inspection found roughly 3.5 GB total, `.git` around 2.1 GB, `build` around 766 MB, and `.dart_tool` around 38 MB.
- **Risk:** Repository bloat, accidental committing of binaries, and unclear source/artifact tracking.
- **Status:** Needs fresh read-only confirmation before any cleanup.
- **Resolution:** Review each category and existing Git history; create a safe backup/retention plan; update ignore rules only after approval. Do not delete files during audit.

### P3-4. HTML IDs and event wiring
- **Finding:** No duplicate HTML IDs; 37 unique IDs. Only `dashboard` was not found in JS text, likely because it is an anchor/section ID. No inline event handlers were found.
- **Status:** No confirmed defect from this check; runtime navigation still needs verification.
- **Resolution:** Test navigation links and every interactive control in the actual browser/WebView.

## Priority 4 — Testing gaps / release readiness

### P4-1. Automated test coverage is insufficient
- **Finding:** `test/widget_test.dart` only checks that “Matric Mate Pakistan” appears after `pumpWidget`; it does not test WebView UI, language, solver, or controls. `tests/math-solver-tests.js` covers a limited set of solver cases.
- **Risk:** Regressions may escape tests.
- **Resolution:** Add/extend tests as features are fixed, including data validation, language switching, navigation, quiz scoring, planner persistence, and solver edge cases.

### P4-2. Runtime and device verification remains outstanding
- **Needed checks:** Browser preview; all navigation; dashboard/subject cards; class/subject/chapter dropdowns; notes/formulas/examples fallbacks; chapter and general quiz; score/result persistence; language persistence and RTL; planner edit/save/cancel/reload; Math Solver common and edge cases; offline operation; JS console errors; Flutter analysis/build; install and test the final APK on Android.
- **Status:** Not yet fully verified in the current audit.
- **Resolution:** After code/content issues are resolved, run this regression checklist. Build APK only after the user-approved backlog is ready.

## Known passing checks (limited scope)
- General Practice quiz structural validation: 24 questions, 0 structural issues.
- One chapter-content entry structurally valid: Class 9 Mathematics, Real and Complex Numbers, 4 English + 4 Urdu MCQs.
- JavaScript syntax checks passed for `script.js`, `simultaneous-solver.js`, and `study-content.js`.
- Existing simultaneous-equation unit tests passed for basic unique, fractional, infinite/no-solution, and invalid-input cases.
- HTML ID audit found no duplicate IDs.

## Suggested step-by-step resolution order
1. **P0-1** Confirm/fix asset manifest mismatch and verify packaged asset loading.
2. **P1-6** Define and test Math Solver supported inputs, step correctness, and arithmetic input safety.
3. **P1-5** Verify Urdu/English behavior across all sections.
4. **P1-4** Academic/language review of the 24-question quiz bank.
5. **P1-1 / P1-2 / P1-3** Verify syllabus chapter lists and build a complete chapter-content coverage matrix before expanding content.
6. **P2-1 / P2-2 / P2-3** Resolve dashboard metric meaning and verify planner/results persistence.
7. **P3-1 / P3-2 / P3-3** Resolve source-of-truth/docs/repository hygiene safely.
8. **P4-1 / P4-2** Complete regression tests, then build/download/install/test the APK.

**Current action:** Audit stopped. This file records findings already observed; it does not claim every possible issue has been discovered. No application code was changed as part of creating this backlog.
