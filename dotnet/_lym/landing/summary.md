# Print the pipe flow report's values to 4 significant digits

`ResultFormatter.Format` now prints velocity, Reynolds number and pressure drop to 4 significant digits, without trailing zeros.
Before, velocity used `0.###`, Reynolds used `0`, and the pressure drop used the default `Quantity` text, so precision varied by value and by field.
For example, `v = 1.23456` now prints as `1.235 m/s`, and `Re = 123456` as `123500`.

No issue is closed by this change.

## Design decisions

- Rounding is done in `FormatToSignificantDigits`, which derives the decimal count from the value's leading-digit exponent.
  The result is printed as fixed-point text with `CultureInfo.InvariantCulture`, so tiny values such as `1.235e-14` print as `0.00000000000001235` instead of exponent notation.
- `Math.Round(value, decimals)` accepts only 0 to 15 decimals, so `RoundToDecimals` rounds by scaling with a power of ten for values outside that range (large magnitudes round to the left of the decimal point, tiny ones need more than 15 decimals).
- The pressure drop is formatted from `drop.Value` and `drop.Unit.Symbol` rather than `Quantity`'s own text, so it follows the same rule as the other values.
- Zero prints as `0`, and negative values keep their sign.

## Tests

`ResultFormatterTests` replaces its three tests with one parameterised `FormatsEveryValueToFourSignificantDigits`.
Its cases cover Pa and bar, rounding up, sub-unit values, a value near `1e-14`, a negative pressure drop and all-zero input.

## To check by hand

- The regression cases that render the formatted report are expected to drift on purpose.
  This diff does not touch them, and the Drift verify was not run here: confirm the drift is reported and accepted with the stated reason.
- Open review finding `formatter-exponent-notation` (class design, medium) in `ResultFormatter.FormatToSignificantDigits`:
  check how the fixed-point output handles values at the extremes of the `double` range, where exponent notation could still appear or the power-of-ten scaling in `RoundToDecimals` could lose precision.
