```yaml
lens: code-correctness
outcome: reported
```

Task: lens code-correctness, covering each branch of `ColebrookFriction.FrictionFactor` and the new tests.

**List (branch, case run, verdict)**
- Guard `reynolds <= 0`: Re 0 and Re -5 throw `ArgumentOutOfRangeException`. Correct.
- Laminar branch: Re 2299 gives 64/2299, and Re 2300 falls through to the turbulent branch. Correct, and it matches the sibling models.
- Seed (Swamee-Jain): Re 2300, ε 0 gives 1/√f of about 4.53 (Re^0.9 is about 1060, 5.74/1060 is 5.4e-3, log10 is -2.267). Finite and positive. Correct.
- Fixed-point iteration, Re 2300, ε 0: the first step gives about 4.61. The map's derivative is about 0.19 there. At Re 1e8, ε 0.05 it is far smaller. Convergence to 1e-12 relative takes about 15 steps or fewer, well under the 50 cap. Correct.
- Exit test `Abs(next - x) <= 1e-12 * next`: `next` is positive for any physical roughness (the log argument is below 1). The tolerance sits well above the double rounding floor, so it is reachable. Correct.
- Return `1/(next*next)`: the residual of `next` against the equation is about the contraction factor times the last step, around 1e-13, below the 1e-9 bound. Correct.
- Cap exhaustion: an iteration that cannot converge throws `InvalidOperationException` with a message naming Re and roughness. No state is left behind, the loop is bounded, and no unconverged value is returned. Correct (D3).
- Out-of-contract inputs (NaN Re, infinite Re, roughness of 3.7 or more): all throw `InvalidOperationException` after 50 steps instead of returning garbage. That is loud, not silent, and these inputs are untrusted-caller misuse, so no finding.
- Test `LaminarFactorIs64OverRe` and test `NonPositiveReynoldsThrows`: correct.
- Test `TurbulentFactorSolvesColebrookWhite`: I ran Re 4000 to 1e7, ε 0 to 0.05. The residual helper uses the same equation as the model, with 1/√f recomputed from the returned factor. Correct.
- Test `TurbulentFactorAgreesWithExplicitModelsWithinThreePercent`: I computed Re 1e4/ε 0, 1e5/1e-4 (not computed, checked from the published 2% accuracy), 1e6/1e-3 (same), 1e7/0.01. The Colebrook, Haaland and Swamee-Jain factors agree to about 0.1 to 1% at the cases I computed. Correct.
- Test `ConvergesAcrossReynoldsAndRoughnessGrid` (`ColebrookFrictionTests.cs`, the `for` loop): the loop does not reach the stated upper end.
  - The exponent starts at log10(4000), about 3.602, and steps by 0.01.
  - The last step that satisfies `<= 8.0` is about 7.992 (Re about 9.8e7).
  - The next step is about 8.002, which fails the bound. The point Re 1e8 is never tested, so the grid does not cover the D5 range "4000 to 1e8".
  - Float accumulation over about 440 additions adds only about 1e-14 of drift. The gap is the step not dividing the span, so the shortfall is structural.

**Findings**
1. LOW, class `design`, `tests/LymCsharp.Hydraulics.Tests/ColebrookFrictionTests.cs` (the `for (var exponent = Math.Log10(4000); exponent <= 8.0; exponent += 0.01)` line in `ConvergesAcrossReynoldsAndRoughnessGrid`). The sweep ends near Re 9.8e7 and never evaluates Re 1e8, so it does not cover the grid D5 states. The tests lens may report the same defect. Evidence: that `for` line, and the arithmetic (8.0 − 3.602) / 0.01 = 439.8 steps, so step 440 lands at 8.002 and is cut off. Fix: drive the loop by an integer index over a fixed point count, e.g. `for (var step = 0; step <= N; step++) exponent = lo + (hi - lo) * step / N`, so that the last point is exactly Re 1e8.

No production-code defect found in `ColebrookFriction.cs`.

**Tool calls:** none. My context already held the diff and the sibling sources.
