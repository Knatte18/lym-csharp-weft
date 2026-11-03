# Round 1 fixer-report

## Changes

Commit `4b31b22`, one commit for all three findings because they rewrite the same formatting path.

- **F1 (fixed as proposed).**
  `ResultFormatter.Format` now formats each value through `FormatToSignificantDigits`, which prints fixed-point text with `ToString("0.###…", CultureInfo.InvariantCulture)`.
  No exponent notation appears and trailing zeros are trimmed.
  Added a row with a pressure drop of 0.0000123456 Pa expecting `dP = 0.00001235 Pa`.
- **F2 (fixed as proposed).**
  `RoundToDecimals` rounds by scaling for decimals above 15, as it already did for negative decimals, so tiny values keep 4 significant digits.
  Added a row with velocity 1.23456e-14 expecting `v = 0.00000000000001235 m/s`.
- **F3 (fixed as proposed).**
  Added a row with a pressure drop of -123456 Pa expecting `dP = -123500 Pa`.

Gate: `lym gate test ./tests/LymCsharp.Hydraulics.Tests` passed, 70 of 70.

## Self-check

- fix-code-correctness: no findings.
- fix-consistency: no findings.
- fix-proposal: no findings.
- fix-affected-set: no findings.

## Disputed

None.

## Deferred

None.
