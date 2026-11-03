```yaml
lens: code-tests
outcome: reported
```

Task: lens code-tests. For each behaviour `Quantity.Parse` adds, name the test that pins it, or none.

**Per-behaviour list**

| Behaviour (`Quantity.cs`) | Pinning test | Status |
|---|---|---|
| Splits at the space, number before it and symbol after it (`1.5 bar`) | `ParseReadsValueAndUnit` row `1.5 bar` (`QuantityTests.cs:75,79-83`) | pinned |
| Symbol lookup through `UnitRegistry.Find` for a symbol containing `/` (`7.2 l/min`) | `ParseReadsValueAndUnit` row `7.2 l/min` (`QuantityTests.cs:76`) | pinned |
| Round trip of `ToString` output (`3.25 psi`) | `ParseReadsBackToStringOutput` (`QuantityTests.cs:85-91`) | pinned |
| Unknown symbol throws `KeyNotFoundException` | `ParsingUnknownSymbolThrows` (`QuantityTests.cs:93-97`) | pinned |
| No space throws `FormatException` | `ParsingTextWithoutSpaceThrows` (`QuantityTests.cs:99-103`) | pinned |
| Non-numeric value part throws `FormatException`. This path comes from `double.Parse`, and the doc comment and summary both claim it. | none | **uncovered** |
| Value read in the current culture. The doc comment and summary claim this. | none | **uncovered** |
| `Find` ignores case, so `1.5 BAR` parses. The summary claims it. | none here; `UnitRegistryTests.FindsKnownSymbols` row `BAR` (`UnitRegistryTests.cs:8`) pins `TryFind` | covered by the registry test. `Parse` adds no case logic. |
| Null or empty `text` | none | not a behaviour the diff defines (the entry lists no guard), so no finding |

**Findings**

1. **MEDIUM, class `design`**, `src/LymCsharp.Units/Quantity.cs:61` with the doc comment at `:51`.
   - The non-numeric value path (`double.Parse` throwing `FormatException`) is added and documented, and no test fails if it breaks.
   - The only `FormatException` test uses `"1.5bar"`, which fails at the missing space, before `double.Parse` runs.
   - Rubric "Test economy": a behaviour the diff adds with no coverage.
   - Fix: add a row such as `"abc bar"` to a `FormatException` test. Make it a `TestCase` row on `ParsingTextWithoutSpaceThrows` or a new table, not a separate test for each input.

2. **LOW, class `design`**, `src/LymCsharp.Units/Quantity.cs:49,61`.
   - The current-culture behaviour is documented and has no test. Switching to `InvariantCulture` would break no test, and neither would any other change to the culture.
   - The round-trip test passes in any culture, because `ToString` and `Parse` both use the current culture. It pins the pairing only on a culture whose decimal separator is a comma.
   - Fix: add a test that sets `CultureInfo.CurrentCulture` to a comma-decimal culture such as `de-DE`, round-trips `3.25 psi`, and restores the culture in teardown. The test touches process-global state, so name that in it.
   - The repository already does this in `BaselineHarnessTests.cs:64-71`.

**Duplicate coverage:** none found. The five new tests cover distinct behaviours, and `ParseCases` is a table.

**Tool calls:** none. The manifest already held both files, `UnitRegistryTests.cs` and the `BaselineHarnessTests` grep line.
