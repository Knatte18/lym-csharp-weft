```yaml
lens: fix-consistency
outcome: reported
```

no findings

Checked against the fixes:
- The F4 renames are complete: no un-prefixed `workDirectory`, `baselineDirectory` or `skipFile` remains in the tests.
- The README command lines and the `Usage` string in `RegressionCommand` still agree with the F1 empty-argument check.
- The new `EmptyBaselineDirectoryIsAUsageError` test asserts the same usage text as `MalformedArgumentsAreAUsageError`.
- The F5 regex change keeps the `OutputScrubber` class and constructor docs accurate.
- The new `/machinery/other` row sits beside its `/machine/other` sibling.
- The scrubber's private field `machineRootPatterns` has no underscore, like `scrubber` in `BaselineHarness`. No `src` project has an underscore-prefixed field to compare against, so I did not count this as a finding.

Tool calls:
1. `grep` of private fields in `src`, plus README mentions: to confirm the field-naming twin and the README's usage text.
2. `git diff --stat` and a `grep` for leftover un-prefixed field names in the tests: to confirm the F4 rename is complete.
