```yaml
lens: code-tests
outcome: reported
```

Task: for each behaviour the diff adds or changes, name the test row that pins it, or none (lens code-tests).

**Per-behaviour list**

| Behaviour | Pinning test row |
| --- | --- |
| Round to 4 digits when `decimals >= 0` (values in [0.001, 10000) and below 1) | `ResultFormatterTests.cs:15` (1.23456 → 1.235), `:19` (0.000123456 → 0.0001235, 1.23456 → 1.235, 0.0123456 → 0.01235) |
| Round a value of 10 000 or more to a whole power of ten (`decimals < 0` branch) | `:15` (123456 → 123500 for Re and for Pa), `:17` (the same value in bar) |
| Value at or below 4 digits prints unchanged, with no trailing zeros | `:8` (1.5, 50000, 200000), `:10` (2 bar) |
| Zero short-circuit | `:21` (all three values 0) |
| Pressure drop converted to the requested unit, printed as value plus `drop.Unit.Symbol` | `:8` (Pa), `:10` (bar), `:19` (bar, 1.235) |
| Regime text | Laminar and Turbulent are pinned. Transitional has no row, but the diff did not change that behaviour. |
| Negative values (`Math.Abs` inside `Math.Log10`) | none |
| `Math.Min(decimals, 15)` clamp | none |
| Very large values (E-notation from double interpolation) | none |
| Dropped tests `FormatsInPascals`, `FormatsInBar`, `IncludesVelocityAndReynolds` | Their assertions are covered by rows `:8` and `:10`. No coverage was lost. |

**Findings**

1. LOW, class `design`, `ResultFormatter.cs:32` (`Math.Min(decimals, 15)`) and `ResultFormatterTests.cs:11-24`.
   - The diff adds the 15-decimal clamp, and no row reaches it.
   - Every small value in the table has a leading exponent of -4 or higher, so decimals stay at 7 or below.
   - Removing the clamp would throw `ArgumentOutOfRangeException` for any value below about 1e-12 and the suite would stay green.
   - A row such as `1e-14` would pin the clamp, with the expected text written out.

2. LOW, class `design`, `ResultFormatter.cs:27` (`Math.Abs(value)`) and `ResultFormatterTests.cs:11-24`.
   - The diff handles negative values explicitly, and every row uses a non-negative input.
   - Replacing `Math.Abs(value)` with `value` makes `Log10` return NaN, so `(int)` becomes `int.MinValue`. That makes `decimals` overflow into a nonsensical value.
   - No row would catch it.
   - A negative pressure-drop row, such as `-123456` Pa giving `-123500 Pa`, would pin it.
   - If the repository's trust-the-caller rule holds that a negative result cannot occur, this is moot; the types do not rule it out.

3. NIT, class `design`, `ResultFormatterTests.cs:11-24`.
   - No row covers a magnitude of 1e15 or more, where double interpolation switches to `E+` notation and the "no exponent" claim in the doc comment would break.
   - This is only reachable with values far outside the hydraulic range, so it is optional.

**Tool calls:** none made. The diff, `ResultFormatter.cs` and `ResultFormatterTests.cs` were already in context.
