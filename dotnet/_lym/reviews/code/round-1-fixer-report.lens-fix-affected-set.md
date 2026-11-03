```yaml
lens: fix-affected-set
outcome: reported
```

No findings.

- F1 and F2: `ResultFormatter.Format` is the only caller of the formatting path, and the only `Math.Round` sites are in `ResultFormatter.cs`. The one external caller, `RegressionCases.cs:68`, goes through `Format`, so it gets the fix. `Quantity.ToString` (`Quantity.cs:45`) still uses `0.###`. It is outside the report line and the review did not cite it.
- F3: the negative row sits in the single test table, which is the only test of this formatter.

Unchecked: none.

Tool calls:
1. `grep` for `RoundToSignificantDigits`, `ResultFormatter`, `0.###` and `Math.Round` over `src` and `tests`. My context did not hold the repository's other callers or rounding sites.
