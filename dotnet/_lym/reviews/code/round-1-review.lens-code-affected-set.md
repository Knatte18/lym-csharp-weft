```yaml
lens: code-affected-set
outcome: reported
```

Task: lens code-affected-set. For each change, I list the set it forces outside the touched lines and say whether the diff covers it.

**Per-change set**

1. `ResultFormatter.Format` output gains the note for `FlowRegime.Transitional`.
   - Callers: `RegressionCases.cs:68` is the only production caller. It builds the `report = …` line for each case through `Format`. Its two transitional cases, `water-pvc-2in-transitional` and `water-pvc-2in-transitional-churchill`, get the note. The other four cases are laminar or turbulent and are unchanged.
   - Fixtures and baselines: the final search found no tracked or untracked baseline `.txt` files, golden files or `baseline*` files. Baselines are generated at run time by `BaselineHarness.Capture`, so nothing checked in needs updating. Covered.
   - Tests that read or assert the line: `ResultFormatterTests.cs:27` is the only one. The grep of the Regression tests for transitional or report text found nothing, so no test there asserts the old line. Covered.
   - Docs and stencils: the `Format` doc comment is updated in the diff. No README or other doc describes the report line. Covered.
   - Repository rule documents: the search found no `PATTERN.md`, `pattern/` directory, `README` or tracked `CLAUDE*` file. `CLAUDE.local.md` holds only workflow rules, so there are no PATTERN declarations to land. Not applicable.
   - Commit message: the `6b1db61` message matches the diff. It names the note, the 2300–4000 range and the unchanged laminar and turbulent lines. Covered.

2. Change description, `_lym/landing/summary.md`.
   - Its claims match the diff: the two named transitional cases, the new test row, and the doc comment naming the note.

3. The `Format` doc comment now mentions the note.
   - It follows the C# XML-doc conventions. It has no end-of-line comment and no reference to another symbol.

**Findings:** none.

**Tool calls**
1. One `git ls-files` and `find`/`grep` call, to check for PATTERN or CLAUDE docs, a README, baseline or golden files and Regression tests referencing the report line. My context lacked a listing of tracked non-source files and any ignored baseline files.
