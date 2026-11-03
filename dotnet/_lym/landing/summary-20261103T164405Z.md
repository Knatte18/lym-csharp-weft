# Print the pipe flow report's values to 4 significant digits

`ResultFormatter.Format` now rounds every numeric value in the report — velocity, Reynolds number and pressure drop — to 4 significant digits.
Before, velocity and pressure drop used fixed `0.###` decimals and the Reynolds number `0`,
so a small pressure drop such as `0.007 psi` kept one significant digit while `Re = 127069` printed six.

## Design decisions

- Rounded values print without trailing zeros (`1.5`, not `1.500`), matching the report's earlier style.
- The pressure drop is formatted in `ResultFormatter` from the converted `Quantity`'s value and unit symbol.
  `Quantity.ToString` keeps its `0.###` format, since other callers of it are outside this change.
- Values of 10 000 and up round to a whole power of ten by scaling, since `Math.Round` takes no negative decimals; the result stays an exact integer.

## Tests

`ResultFormatterTests` is one table-driven test asserting the full report line:
values already at 4 digits, rounding in Pa and bar, values below 1 that the old format truncated, and zero.

## Intended regression drift

The `report = ...` line (line 12) of each regression case changes on purpose; every raw value line is unchanged.

| Case | Before | After |
| --- | --- | --- |
| `water-steel-dn100-swamee-jain` | `v = 1.273 m/s, Re = 127069, dP = 7925.267 Pa` | `v = 1.273 m/s, Re = 127100, dP = 7925 Pa` |
| `water-steel-dn100-haaland` | `v = 1.273 m/s, Re = 127069, dP = 0.078 bar` | `v = 1.273 m/s, Re = 127100, dP = 0.07793 bar` |
| `oil-steel-dn100-laminar` | `v = 0.013 m/s, Re = 22, dP = 0.102 kPa` | `v = 0.01273 m/s, Re = 22.15, dP = 0.1019 kPa` |
| `water-pvc-2in-transitional` | `v = 0.059 m/s, Re = 3002, dP = 0.007 psi` | `v = 0.05921 m/s, Re = 3002, dP = 0.006776 psi` |
| `water-pvc-2in-transitional-churchill` | `v = 0.059 m/s, Re = 3002, dP = 0.007 psi` | `v = 0.05921 m/s, Re = 3002, dP = 0.006546 psi` |

`oil-steel-6in-turbulent` does not drift: its report (`v = 2.001 m/s, Re = 5306, dP = 1.299 bar`) already had 4 significant digits.
