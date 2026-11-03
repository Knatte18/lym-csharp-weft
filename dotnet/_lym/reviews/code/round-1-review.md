---
verdict: APPROVED
findings:
  - id: F1
    severity: MEDIUM
    class: design
    origin: lens:numerics
    location: src/LymCsharp.Reporting/ResultFormatter.cs:22
    summary: Interpolating the rounded doubles prints exponent notation such as 1.235E-05 for values below 1e-4, which the old 0.### format never did
    fix: In ResultFormatter.Format, format each rounded value as fixed-point text instead of interpolating the raw double, for example with a private helper that calls value.ToString("0.#################", CultureInfo.InvariantCulture) on the result of RoundToSignificantDigits, so no exponent text appears and no trailing zeros print. Add a row to FormatsEveryValueToFourSignificantDigits whose small value is below 1e-4, such as a pressure drop of 0.0000123456 Pa expecting dP = 0.00001235 Pa.
  - id: F2
    severity: LOW
    class: design
    origin: lens:code-correctness
    location: src/LymCsharp.Reporting/ResultFormatter.cs:32
    summary: The Math.Min(decimals, 15) clamp leaves values below about 1e-12 with fewer than 4 significant digits and turns values below 5e-16 into 0 or -0
    fix: In RoundToSignificantDigits, when decimals exceeds 15, round by scaling as the negative-decimals branch does (multiply by a power of ten, round, divide) instead of clamping, so the 4 significant digits survive. Add a test row with a value near 1e-14 and an expected string written out, which pins the branch.
  - id: F3
    severity: LOW
    class: design
    origin: lens:code-tests
    location: tests/LymCsharp.Hydraulics.Tests/ResultFormatterTests.cs:11
    summary: No test row has a negative value, so removing the Math.Abs in the exponent computation would keep the suite green
    fix: Add a row to FormatsEveryValueToFourSignificantDigits with a negative pressure drop, for example -123456 Pa expecting dP = -123500 Pa.
---

### [MEDIUM] Exponent notation in the report

**Location:** `src/LymCsharp.Reporting/ResultFormatter.cs:22`

**Issue:** `Format` interpolates `double` values directly.
.NET prints a `double` with exponent notation below 1e-4 and from 1e15 up, so a small velocity or pressure drop such as 0.00001235 prints as `1.235E-05`.
The old `0.###` and `Quantity.ToString` never printed exponent text, and the goal asks for 4 significant digits, not a notation change.
The test table stops at 0.0001235, one step above the switch, so no row catches it.
The affected values are plausible in the regression cases' units, for example a few pascals converted to bar.

### [LOW] Clamp loses significant digits for tiny values

**Location:** `src/LymCsharp.Reporting/ResultFormatter.cs:32`

**Issue:** `Math.Round` accepts at most 15 decimals, so the clamp gives a value of 1e-13 only 2 significant digits and prints a nonzero value below 5e-16 as `0`.
This breaks "every numeric value to 4 significant digits" for tiny values.
No test row reaches the clamp.

### [LOW] Negative values have no test row

**Location:** `tests/LymCsharp.Hydraulics.Tests/ResultFormatterTests.cs:11`

**Issue:** The diff takes the exponent from `Math.Abs(value)`, and every row uses a non-negative input.
Replacing `Math.Abs(value)` with `value` makes `Math.Log10` return NaN and corrupts `decimals`, yet the suite stays green.

## Rejected

- Banker's rounding on exact ties (`MidpointRounding.ToEven`, lenses code-correctness and numerics): the goal states no tie rule, and ties are rare in doubles, so the default is a legitimate choice.
- Infinity prints `NaN` (lenses code-correctness and numerics): neither fork read `PipeFlowCalculator`, so reachability is unshown, and internal callers are trusted per the code-quality rules.
- Culture-sensitive interpolation (lens numerics, NIT): the old `0.###` format was equally culture-sensitive, so this is not a change the diff introduces.
- No committed regression baseline files in the diff (focus item 3): baselines live in the external directory `BaselineHarness` reads, and the run records the drift through Drift verify; the diff has nothing to update, so leaving them untouched meets the goal.
- The 1e15 and above test row (lens code-tests, NIT): hydraulic values do not reach that range, and F1's fix covers the notation behavior.
- Change description's drift table (`_lym/landing/summary.md`): no claim in it contradicts the diff, and the values depend on a run this review did not repeat.
- Test economy: the three replaced tests' assertions are all covered by the table's first two rows, so no coverage was lost and no new test duplicates another.
- `UnitRegistry.Find` resolving `Pa` and `bar`: `UnitRegistry.TryFind` matches `UnitCatalog.All` symbols case-insensitively, and the old tests used `UnitCatalog.Pascal` and `UnitCatalog.Bar`, so the symbols resolve.
