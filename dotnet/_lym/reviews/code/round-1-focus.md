---
round: 1
focus:
  - "`ResultFormatter.Format` in `dotnet/src/LymCsharp.Reporting/ResultFormatter.cs`: does the note land only when `result.Regime` is `FlowRegime.Transitional`, with the exact text ` (transitional: friction factor uncertain)`, and do laminar and turbulent lines stay byte-identical?"
  - "The `Format` doc comment: does it name the note as the board entry asks, and does it follow the repository's C# comment conventions?"
  - "`ResultFormatterTests.FormatsEveryValueToFourSignificantDigits`: does the new transitional case expect the example line from the board entry, and are the laminar and turbulent cases untouched?"
  - "Regression output: `RegressionCases.cs` builds its `report = …` line through `ResultFormatter.Format`. Does the diff move only the transitional cases' `report` lines, and does any checked-in expected output or golden file for those cases need the note and go unchanged?"
  - "Commit message of `6b1db61`: does its account match the diff?"
---

The diff is two files: the formatter and its test.
No `_lym/landing/summary.md` or `PATTERN.md` change was seen, and no plan exists, so the board entry in the decision record is the measuring stick.
