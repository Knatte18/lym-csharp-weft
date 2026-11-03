```yaml
lens: code-tests
outcome: reported
```

Task: the code-tests lens. For each behaviour the diff adds or changes, I name the test that pins it, or none.

**Per behaviour: the pinning test**

1. `Reynolds` computes ρ·v·D/μ, with the flow unit converted to m³/s.
   - Pinned by `ReynoldsMatchesFourRhoQOverPiMuD` (`PipeFlowTests.cs:45`).
   - It checks oil at 0.1 L/s and water at 10 L/s against the closed form 4ρQ/(πμD), within a relative 1e-12.
   - It would fail if the formula or the L/s conversion broke.
2. `Reynolds` result lands in the expected regime, one laminar and one turbulent case.
   - Pinned by the same test, through its two `Classify` assertions (`PipeFlowTests.cs:62` and `:64`).
   - This meets the record's "one laminar and one turbulent flow" requirement.
3. `Calculate` returns `PipeFlowResult.Reynolds` from `Reynolds`, and `Classify` is applied to it.
   - Regime is asserted in `WaterAtModerateFlowIsTurbulent` (`PipeFlowTests.cs:76`) and `OilAtLowFlowIsLaminar` (`:88`).
   - The result's `Reynolds` value is captured by the regression harness at `RegressionCases.cs:61` (`reynolds = {result.Reynolds:R}`), which is the record's own bit-identity check.
   - I found no unit test that asserts `result.Reynolds` itself. The harness covers it, so this is not a finding.
4. `Calculate`'s friction factor and drop are unchanged.
   - Pinned by `LaminarDropMatchesHagenPoiseuille` (`:92`), `CustomFrictionModelIsUsed` (`:136`) and `FlowUnitDoesNotChangeResult` (`:104`).
   - Bit-identity is pinned only by the regression harness, which is outside this diff's unit tests and not a gap.
5. `Calculate` throws `ArgumentOutOfRangeException` when the flow is not positive.
   - Pinned by `NonPositiveFlowThrows` (`:125`), for 0 and -1.
   - Now that `Calculate` calls the shared `MeanVelocityMetresPerSecond` on its first line, it throws before `Reynolds` runs.
6. `Reynolds` throws `ArgumentOutOfRangeException` when the flow is not positive.
   - Pinned by none.
   - `NonPositiveFlowThrows` only calls `Calculate`.
   - If `Reynolds` stopped validating, for example by inlining a velocity computation, every test would still pass.
7. The exception's `ParamName` is `flow`: pinned by none, for either `Calculate` or `Reynolds`. This was not asserted before the diff either, so it is not new.

**Findings**

- LOW, class `design`, `PipeFlowTests.cs:125` and `PipeFlow.cs:27-31`.
  - **Summary:** The documented non-positive-flow exception of the new public `Reynolds` has no test.
  - **Failure scenario:** `NonPositiveFlowThrows` exercises only `Calculate`. A change that dropped `Reynolds`' own guard would return a negative or infinite Reynolds number for `Reynolds(pipe, fluid, new Quantity(0, ...))`, and no test would fail.
  - **Fix:** Add a `Reynolds` call to the `NonPositiveFlowThrows` rows. A new test is not needed.
  - **Evidence:** `PipeFlowTests.cs:125-133` and the `<summary>` at `PipeFlow.cs:27-30`.

Nothing in the diff is redundant:
- `ReynoldsMatchesFourRhoQOverPiMuD` pins the public `Reynolds` surface, and the existing `Calculate` tests do not assert its value.
- Its `Classify` assertions check the regime of a `Reynolds` output. They do not repeat `ClassifiesRegime`, which feeds `Classify` literal numbers.

**Tool calls**
1. `grep` for `Reynolds` and `ParamName` under `src/LymCsharp.Regression`, `tests/` and `src/LymCsharp.Reporting`. I did not have the regression and reporting sources in context, and needed to confirm whether `result.Reynolds` and the exception parameter name are asserted anywhere.
