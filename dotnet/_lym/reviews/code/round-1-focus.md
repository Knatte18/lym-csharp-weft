---
round: 1
focus:
  - "OutputScrubber.Scrub: check that number canonicalisation to 10 significant digits cannot throw or corrupt output on lines the cases really render (very large or tiny values, 'E' notation, negative zero, integers such as ids or counts that G10 would turn into exponent form) and that machine-root replacement runs before the number rewrite without mangling paths."
  - "RegressionCommand.Run argument validation and skip handling against the decision record's acceptance criteria: unknown mode, missing baseline dir, extra or misordered arguments, a missing skip file, and an unknown skip name all exit 2; confirm a skipped case is neither written nor compared."
  - "BaselineHarness.Compare: confirm the first deviation per case reports case name, line number and both values, that a missing baseline file and a differing line count are deviations, and that the exit code is 1 only when some case drifted."
  - "Culture handling in BaselineHarness.RenderScrubbed and RegressionCases: confirm output is identical under a non-invariant current culture, including any formatting that runs outside the temporarily swapped CurrentCulture (for example cases that capture culture-dependent values at construction)."
  - "Scope against the board entry and decision record: the diff adds only the two projects, solution entries, the .gitignore line and the README command; confirm no existing hydraulics, units or reporting behaviour changed and the README names the exact command the record specifies."
  - "Test economy in tests/LymCsharp.Regression.Tests: look for tests duplicating coverage across BaselineHarnessTests, OutputScrubberTests and RegressionCommandTests, and for any decision-record acceptance criterion (non-invariant culture, repeat-render equality, creating an absent directory, leaving no files behind) with no covering test."
---

Seed pass; the focus points at the harness logic where the acceptance criteria are most likely to break.
