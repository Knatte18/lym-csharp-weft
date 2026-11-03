# Round 1 fixer-report

## Changes

- F1: `Quantity.Parse` reads the value with `NumberStyles.Float` and `CultureInfo.CurrentCulture`, and `Quantity.cs` gains `using System.Globalization;`. Fixed as proposed. Commit `edbda77`.
- F2: `ParsingTextWithoutSpaceThrows` became the table test `ParsingTextThatCannotBeSplitOrReadThrows` with rows `1.5bar`, `abc bar` and `1,5 bar`, all asserting `FormatException`. Fixed as proposed, plus the `1,5 bar` row as the regression row for F1. Commit `8a2852c`, shared with F3 since both change `QuantityTests.cs`.
- F3: fixed with my own change. The review proposed setting `CultureInfo.CurrentCulture` in a try/finally.
  I used NUnit's `[SetCulture]`, which restores the culture itself and matches the `[SetCulture("nb-NO")]` precedent in `BaselineHarnessTests.cs`.
  `ParseReadsTheCurrentCulturesDecimalSeparator` runs under `de-DE` and asserts that `Parse` of the 3.25 psi `ToString` output equals the quantity and that `1.5 bar` throws `FormatException`.
  A comment names the process-global state.
  The class carries `[SetCulture("")]`, the invariant culture, which pins every parse test.
  Commit `8a2852c`.
- F4: the summary's culture bullet limits the round-trip claim to values with at most 3 decimals.
  Fixed as proposed, and I added a bullet on the thousands separator and updated the `## Tests` paragraph, which the fix-consistency and fix-proposal forks found stale.
  `_lym/landing/summary.md` is under `_lym`, so it is edited in the working tree and not committed.

The gate (`lym gate test tests/LymCsharp.Units.Tests`) passed: 29 tests, 0 failed.

## Self-check

- fix-consistency, LOW: summary `## Tests` paragraph stale. Fixed as proposed.
- fix-proposal, LOW: same finding, merged with the one above.
- fix-consistency, LOW: `OutputScrubber.cs` also matches `double.Parse`. Disputed, premise false: it already passes `CultureInfo.InvariantCulture`, per the fix-affected-set fork's grep, and `Quantity.Parse` does not call it. Nothing to change.
- fix-code-correctness: no findings.
- fix-affected-set: no findings.

## Disputed

None. The `OutputScrubber.cs` remark above was a no-action note, not a finding.

## Deferred

None.
