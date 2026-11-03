```yaml
lens: code-tests
outcome: reported
```

Task: the code-tests lens. For each behaviour the diff adds, I name the test row that would fail if it broke.

**Per behaviour**
- Laminar factor equals 64/Re: pinned by `ChurchillFrictionTests.LaminarFactorIs64OverRe` at Re 100, 500 and 1000. By hand, the deviation at Re 1000 is about 1e-12 %, inside the `Within(1e-9).Percent` tolerance, so the test should pass. A wrong `(8/Re)^12` term or the leading `8.0` would fail it.
- Non-positive Reynolds throws `ArgumentOutOfRangeException`: pinned by `NonPositiveReynoldsThrows` at 0 and -5.
- Turbulent factor within 1 % of Colebrook: pinned by `TurbulentFactorAgreesWithColebrookWithinOnePercent`, five rows from Re 1e4 to 1e7 and roughness 0 to 0.01. This pins the `A` term, including the `2.457`, `0.9`, `0.27` and `7` constants, the `^16`, the `-1.5` exponent and the roughness dependence. I did not run the test, so the 1 % margin is unconfirmed; that is the verify gate's call.
- Transitional-regime behaviour (Re 2300–4000, the `(37530/Re)^16` term): no test asserts a value or tolerance. See F1.
- New regression case `water-pvc-2in-transitional-churchill`: the case runs through `BaselineHarnessTests` (it iterates `RegressionCases.All`). That test only captures and compares against its own freshly captured baseline, so it fails if the case throws or renders unstably. It does not assert the case's values. That fits the Goal, which wants Drift to flag a case missing from the baseline. No finding.
- Test economy: the laminar and throws tests repeat the pattern of the Colebrook and SwameeJain tests, but each is for a different class, so no duplicate exists. No finding.

**Findings**

F1: LOW, class `design`, location `tests/LymCsharp.Hydraulics.Tests/ChurchillFrictionTests.cs:7-34`
- Summary: the `37530.0 / reynolds` transition term, and so Churchill's transitional-range behaviour, has no pinning test row.
- Evidence: the laminar rows (Re ≤ 1000) and turbulent rows (Re ≥ 1e4) both sit outside the range where that term matters. At Re 1e4 the `B` term is about 1.4e9 against `A` of about 2e19, so it is negligible. At Re 1000, `B` is about 1.5e25 and the laminar term is about 7e-26, so `B` only shrinks a term that is already negligible. Changing 37530 to any other value, or dropping `transitionTerm`, leaves every row green. The Goal calls the model "valid for ... transitional" but only expects laminar and turbulent checks, so I rate this LOW.
- Fix: add a row for Re 3000, Re 3500 or a similar point in 2300–4000. Assert the factor falls between the laminar `64/Re` and the turbulent Colebrook value, or within a stated tolerance of Colebrook, rather than leaving transitional unasserted. I did not compute a tight tolerance, so the fixer must choose one.

**Tool calls:** none. The diff, the existing friction tests and `RegressionCases.cs` were already in context.
