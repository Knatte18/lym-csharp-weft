---
verdict: APPROVED
findings:
  - id: F1
    severity: LOW
    class: design
    location: tests/LymCsharp.Hydraulics.Tests/ColebrookFrictionTests.cs:57
    summary: The Slow sweep stops at about Re 9.8e7 and never evaluates Re 1e8, the upper end D5 states.
    fix: In ConvergesAcrossReynoldsAndRoughnessGrid replace the floating-point loop `for (var exponent = Math.Log10(4000); exponent <= 8.0; exponent += 0.01)` with an integer-indexed loop, for example `const int steps = 440; for (var step = 0; step <= steps; step++) { var exponent = Math.Log10(4000) + step * (8.0 - Math.Log10(4000)) / steps; ... }`, so the last point is exactly Re 1e8 and the first is Re 4000.
    origin: lens:code-correctness
  - id: F2
    severity: LOW
    class: scope
    location: src/LymCsharp.Hydraulics/ColebrookFriction.cs:38
    summary: The cap-exhaustion throw of D3 has no test row; removing it or returning the last iterate leaves every test green.
    fix: Add one test to ColebrookFrictionTests, for example `NonConvergingInputThrows`, asserting `Assert.Throws<InvalidOperationException>(() => _model.FrictionFactor(double.NaN, 0.001))`, since a NaN Reynolds number never satisfies the exit test and exhausts the cap.
    origin: lens:code-tests
---

### [LOW] Slow sweep never reaches Re 1e8

**Location:** `tests/LymCsharp.Hydraulics.Tests/ColebrookFrictionTests.cs:57`, the loop in `ConvergesAcrossReynoldsAndRoughnessGrid`.
**Issue:** The exponent starts at log10(4000), about 3.602, and steps by 0.01. The span is 439.8 steps, so the last exponent is about 7.992 (Re about 9.8e7) and the next, about 8.002, fails the `<= 8.0` bound. Decision D5 and the change description both state the grid as Re 4000 to 1e8. I confirmed the last exponent by simulating the loop (440 iterations, last 7.99206). Three of four lenses reported this independently.

### [LOW] Cap-exhaustion throw is not pinned by a test

**Location:** `src/LymCsharp.Hydraulics/ColebrookFriction.cs:38`, the `throw new InvalidOperationException(...)` after the loop.
**Issue:** D3 makes the throw the contract, but no test asserts it. The record's acceptance criterion treats the sweep never throwing as coverage, which pins only the absence of the throw. Replacing the throw with `return` of the last iterate would pass every test. The rubric's test-economy bullet names a behavior the diff adds with no coverage as a finding; the record's accepted coverage route keeps this at LOW.

## Rejected

- Duplicate residual assertion in `TurbulentFactorSolvesColebrookWhite` (lens code-tests F3): tier 0 excludes the `Slow` category, so these rows are the only residual coverage in the default tier, and the record's D2 acceptance criterion needs them.
- No test row at the Re 2300 laminar/turbulent boundary (lens code-tests F4): both sibling models' tests leave the same boundary untested, and D1 asks for parity with them.
- Exit test relies on `next > 0`, so NaN Re or roughness above 3.7 throws "did not converge" instead of naming the cause (lens numerics NIT): the inputs are outside the physical range, the record trusts the roughness argument, and D3 keeps the failure loud.
- Scope: the diff touches only the two files the board entry and decision record list, so nothing in `LymCsharp.Regression`, `LymCsharp.Units` or `LymCsharp.Reporting` changed (lens code-affected-set, handler).
- D1 laminar branch and input guard, D2 iteration with the Swamee-Jain start, the 1e-12 relative tolerance and the 50-iteration cap, D3 throw, and D4 grid and 3 percent tolerance all match the diff and the sibling models (lens code-correctness, lens numerics, handler).
- No `<inheritdoc/>` or summary defects: the class summary follows the sibling one-line summaries and the implementation carries `<inheritdoc/>` (lens code-affected-set).
