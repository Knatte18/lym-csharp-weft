```yaml
lens: fix-proposal
outcome: reported
```

Task: challenge the F1 proposal, a Re 3000 row asserting the factor is above 64/Re and within 5% of Colebrook. I check it against the review's stated aim that changing 37530 or dropping `transitionTerm` fails the row.

**MEDIUM: the 5% bound against Colebrook does not catch dropping `transitionTerm`**
- Changed text: `TransitionalFactorLiesBetweenLaminarAndTurbulentValues`, `Within(5).Percent` at Re 3000, relative roughness 1e-4, in `ChurchillFrictionTests.cs`.
- Evidence: these are hand calculations, not a run.
  - Colebrook at that point is about 0.0436.
  - Churchill as written: A = 2.457·ln(1/((7/3000)^0.9 + 2.7e-5)) ≈ 13.39, so A^16 ≈ 1.0e18 and transitionTerm = 12.51^16 ≈ 3.6e17. That gives f ≈ 0.0429, about 1.7% from Colebrook.
  - With `transitionTerm` removed, B ≈ 1.0e18 and f ≈ 8·exp(−62.2/12) ≈ 0.0449, about 3% from Colebrook.
  - So the mutant passes the row, and the review's goal "dropping transitionTerm fails the row" is not met.
  - Moving the constant 37530 to 40000 gives f ≈ 0.0411, which is −5.7% and fails the row.
  - Moving 37530 down to about 30000 should still pass, since it leaves f between those two values.
- Fix direction: tighten the bound to about 2%, or assert against a precomputed reference value with a small tolerance, which the review also allowed. Either must still pass the correct code. A 2% bound is tight against my 1.7% figure, so use the reference value if a run shows it is too close.

**LOW: `Is.GreaterThan(64.0 / 3000.0)` adds nothing**
- Changed text: the first assertion of the same test.
- Evidence: the factor is about 0.043 against 64/Re ≈ 0.0213, a gap of about 2×. Any plausible mutant, including dropping `transitionTerm` (≈0.0449), passes it.
- It is redundant next to the Colebrook assertion and can go, or be kept as documentation only.

Further tool calls: none. The context already held the code, the test and the review, and the arithmetic needs no reads.
