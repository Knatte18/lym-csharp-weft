```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of `ColebrookFriction.cs` and `ColebrookFrictionTests.cs`.

**Checked, no finding**
- **No `==` on floats:** every comparison is a tolerance or a `<=` bound.
- **No casts:** no integer casts, so no overflow or precision loss from them.
- **Convergence rate:** the map's derivative magnitude is about 0.9/x, roughly 0.08–0.17 over Re 4000–1e8 with x about 5–11. Starting from the Swamee-Jain estimate, 1e-12 relative is reached in about 10–14 iterations, well inside the 50 cap.
- **Tolerance reachable in double:** rounding noise is about 1e-15 relative, so 1e-12 is reachable.
- **Residual test vs exit test:** the residual of the returned `next` is about 0.17 × the last step, so at most about 2e-12. That is below the 1e-9 assertion, and the `1/(next*next)` then `1/sqrt` round trip adds only about 1e-15.
- **Agreement grid:** hand-evaluating the corners (Re 1e4 smooth; Re 1e7 with ε/D 0.01) puts Haaland and Swamee-Jain within about 1% of Colebrook, inside the 3% tolerance.
- **Laminar branch:** `64.0 / reynolds` is correct for Re at or below 2300.
- **Re 2300 step:** the jump from 64/Re to the turbulent value at Re 2300 is the same in both sibling models, so it is not a defect of this diff.

**Findings**

1. **LOW / design** — `ColebrookFrictionTests.cs`, the `ConvergesAcrossReynoldsAndRoughnessGrid` loop (`for (var exponent = Math.Log10(4000); exponent <= 8.0; exponent += 0.01)`).
   - The floating-point loop counter drifts, and the span from log10(4000) ≈ 3.60206 to 8.0 is 439.79 steps of 0.01. The last iterate is therefore about 7.992, so the largest Re swept is about 9.8e7 and never 1e8.
   - Decision D5 and the summary both state the upper bound as Re 1e8, so the sweep stops short of the range D5 states.
   - Fix: drive the loop with an integer index and clamp or append the endpoint. For example, compute `exponent = Math.Log10(4000) + i * (8.0 - Math.Log10(4000)) / steps` for `i` in `0..steps` inclusive.

2. **NIT / design** — `ColebrookFriction.cs`, the exit test `Math.Abs(next - inverseSqrtFactor) <= RelativeTolerance * next`.
   - The test relies on `next > 0`. If relative roughness exceeds about 3.7, or Re is NaN, `next` is non-positive or NaN. The test is then never true, and the call throws "did not converge" after 50 iterations instead of rejecting the input.
   - Both inputs are outside the physical range and the record trusts the roughness argument (Auto-mode assumptions). The sibling models return a silently wrong or NaN value in the same cases.
   - Raise only if the author wants the exception to name the cause. Otherwise nothing is needed: D3 keeps it loud.

**Tool calls:** none. The diff, sibling models and decision record were already in context.
