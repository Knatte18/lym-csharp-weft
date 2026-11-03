```yaml
lens: code-correctness
outcome: reported
```

Task: the code-correctness lens, covering each function and branch the diff adds or changes and the case I ran by hand on it.

**Per-branch list**

- **`ChurchillFriction.FrictionFactor`, the formula.** Correct against Churchill 1977.
  - The code has f = 8·[(8/Re)^12 + (A+B)^-1.5]^(1/12), with A = [2.457·ln(1/((7/Re)^0.9 + 0.27·ε/D))]^16 and B = (37530/Re)^16.
  - The exponents and constants all match.
  - Re=100, ε/D=0.001 gives laminarTerm ≈ 6.9e-14 and B ≈ 1e41, so (A+B)^-1.5 ≈ 1e-61 is negligible. f ≈ 8·0.08 = 0.64, which equals 64/Re. Correct.
  - Re=1000 gives a relative deviation from 64/Re of about 3e-14, inside the test's 1e-9 % (1e-11 relative) tolerance. Correct.
- **Re=3000, ε/D≈2.95e-5, the regression case (2-inch PVC, 7.2 L/min water).**
  - I did not read `Fluid.Water`. The case reuses the existing `water-pvc-2in-transitional` inputs, so its Re is the same as that case's.
  - Hand arithmetic gives A ≈ 1e18, B ≈ 3.6e17 and laminarTerm ≈ 1e-31.
  - The transition term dominates, and f ≈ 0.046, finite and between 64/Re = 0.0213 and the turbulent value of about 0.043. Correct.
- **Smooth turbulent case, Re=1e4, ε/D=0.** Hand arithmetic gives f ≈ 0.0311 against a Colebrook value of about 0.0309, so the test's 1 % tolerance holds with margin. Correct. I did not run the test.
- **Branch `reynolds <= 0`.**
  - Re=0 and Re=-5 both throw `ArgumentOutOfRangeException`. Correct; this matches `ColebrookFriction` and the other models.
  - Re=NaN: the comparison is false, so the guard is skipped, every term is NaN, and the method returns NaN silently. `ColebrookFriction` throws `InvalidOperationException` for the same input; the `NonConvergingInputThrows` test pins this.
  - Re=+∞: laminarTerm=0, A=∞ and B=0, so f = 8·0 = 0. A friction factor of 0 is wrong; the physical limit at infinite Re is the rough-pipe value. It is a boundary-only input.
- **Extreme small Re.**
  - Re=1e-3 and 1e-10 stay finite. Correct.
  - At Re ≈ 1e-20 and below, `Math.Pow` overflows, and the result diverges from 64/Re. At 1e-30 it returns ∞.
  - It is non-physical and unreachable from `PipeFlowCalculator`, so I do not report it.
- **`relativeRoughness` unvalidated.**
  - A negative value large enough that `(7/Re)^0.9 + 0.27·ε/D ≤ 0` gives `Math.Log` of a negative number, which is NaN, or of 0, which gives ∞ and f = 0. The method returns NaN or 0 silently.
  - NaN roughness returns NaN silently.
  - `ColebrookFriction` fails loudly on the same inputs (NaN from `Math.Log10`, then the non-convergence throw).
  - `PipeFlowCalculator` passes `pipe.RoughnessMetres / pipe.DiameterMetres`, and I did not check whether `Pipe.From` rejects negative roughness, so reachability is unconfirmed.
- **`RegressionCases` entry.** The new line is appended at the end of `All`, and the earlier entries are untouched. The `Pvc2Inch` and `Fluid.Water` arguments are valid. Correct.

**Findings**

1. **LOW, class `design`** — `src/LymCsharp.Hydraulics/ChurchillFriction.cs:16`: neither input is checked against NaN, and `relativeRoughness` is not checked at all.
   - NaN reynolds, NaN roughness, or a negative roughness that makes the `Math.Log` argument non-positive each return NaN or 0 silently.
   - A NaN friction factor then flows into `PipeFlowCalculator.Calculate`'s pressure drop.
   - `ColebrookFriction` throws in the NaN cases.
   - The focus asked whether roughness is validated like Reynolds. It is not, and the model does not use the same fail-loud pattern as `ColebrookFriction`.
   - Suggested fix: change the guard to `if (!(reynolds > 0))`, which also rejects NaN. Add `if (!(relativeRoughness >= 0))` throwing `ArgumentOutOfRangeException(nameof(relativeRoughness), ...)`. Add test rows for both.
   - Whether that guard is wanted depends on `Pipe.From` already rejecting negative roughness, which I did not read. If it does, only the NaN/zero guard on reynolds applies.

No other defects found.

**Tool calls:** none. My context already held every file I needed.
