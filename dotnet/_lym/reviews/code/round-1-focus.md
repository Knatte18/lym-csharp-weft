---
round: 1
focus:
  - "ResultFormatter.cs RoundToSignificantDigits: settle whether NaN, infinity, negative and very small or very large values round or print correctly, given the (int) cast of Math.Log10 and the Math.Min(decimals, 15) clamp."
  - "ResultFormatter.Format: settle whether interpolating raw doubles is culture-sensitive or switches to exponent notation for large or tiny values, and whether the old Quantity.ToString output for the pressure drop is reproduced apart from the intended digit change."
  - "Goal item 3 (regression cases that render the formatted report drift on purpose, and the run records the drift as intended): settle whether the diff touches any regression capture or expected-output files, and whether leaving them untouched meets or contradicts the goal."
  - "ResultFormatterTests.cs: settle whether the rewritten cases cover every behavior the new rounding adds (negative values, the non-positive-decimals branch beyond one case, the 15-decimal clamp), and whether UnitRegistry.Find resolves the symbols the cases use."
  - "Commit message and ResultFormatter doc comment: settle whether each claim (velocity, Reynolds number and pressure drop rounded; 'every numeric value'; no trailing zeros) is borne out by the diff, and whether the comments follow the repository's C# comment conventions."
---

Seed focus for round 1 of the review of `9017f2d..HEAD`, which changes `ResultFormatter` and its tests.
The decision record holds only the board entry under `## Goal`, so it is the measuring stick.
