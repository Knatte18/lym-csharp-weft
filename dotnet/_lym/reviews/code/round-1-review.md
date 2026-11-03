---
verdict: APPROVED
findings:
  - id: F1
    severity: LOW
    class: design
    origin: lens:code-tests
    location: tests/LymCsharp.Hydraulics.Tests/ChurchillFrictionTests.cs:7
    summary: No test row pins the transitional range, so the 37530/Re transition term is unasserted
    fix: In ChurchillFrictionTests.cs add one [TestCase] test, for example TransitionalFactorLiesBetweenLaminarAndTurbulentValues, at Re 3000 and relative roughness 0.0001. Assert the factor is greater than 64/Re and within 5 percent of ColebrookFriction at the same point, or equal to a reference value computed independently of the code, so that changing 37530 or dropping transitionTerm fails the row. Keep the existing rows unchanged.
---

### [LOW] Transitional range is not pinned by any test

**Location:** `tests/LymCsharp.Hydraulics.Tests/ChurchillFrictionTests.cs:7`
**Issue:** The laminar rows (Re 100 to 1000) and the turbulent rows (Re 1e4 and above) both sit outside the range where the `(37530/Re)^16` term matters, so changing the constant or removing `transitionTerm` leaves every row green. The diff adds that behaviour and the Goal states the model is valid in transitional flow, so a row at Re 2300 to 4000 is the missing coverage. The regression case runs at Re of about 3000 but only captures output and asserts no value.

## Rejected

- Silent NaN from NaN `reynolds` and from invalid `relativeRoughness` (code-correctness F1, numerics F1 and F2): `reynolds` and roughness come from `PipeFlowCalculator` and `Pipe`, which are internal callers, and `code-quality` says to trust them; the Goal asks for a Reynolds guard only, which the diff matches.
- Platform-dependent last-ulp drift in the baseline (numerics F3): the existing cases share the exposure and the diff adds no new class of risk.

## Holistic checks

- The formula matches Churchill 1977: exponents 12, 16, -1.5 and 1/12, constants 8, 7, 0.9, 0.27, 2.457 and 37530, natural log.
- `RegressionCases` only appends `water-pvc-2in-transitional-churchill`; the five existing entries are unchanged.
- The new case reuses `Pvc2Inch`, water and 7.2 L/min, which gives Re of about 3000, classified Transitional by `PipeFlowCalculator.Classify`.
- Both commit messages and `_lym/landing/summary.md` claims match the diff.
- The repository has no `PATTERN.md`, `pattern/` directory or checked-in constraints document, and no `Pattern-Departure:` trailer is present.
