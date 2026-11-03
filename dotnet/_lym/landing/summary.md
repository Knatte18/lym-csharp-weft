# Extract PipeFlowCalculator.Reynolds from Calculate

`PipeFlowCalculator.Calculate` computed the Reynolds number inline.
It now calls a new public static `PipeFlowCalculator.Reynolds(Pipe, Fluid, Quantity flow)`,
so callers can get the Reynolds number without running a full pressure-drop calculation.

## Behavior

The refactor preserves behavior.
`Reynolds` evaluates `ρ·v·D/μ` with the same operands in the same order as the old inline expression,
so every `PipeFlowResult` that `Calculate` returns is unchanged to the last bit.

## Design decisions

- The flow-to-velocity conversion and the positive-flow check moved into a private `MeanVelocityMetresPerSecond`, which `Reynolds` and `Calculate` both call.
  `Reynolds` is public, so it rejects a non-positive flow with the same `ArgumentOutOfRangeException` (parameter `flow`) that `Calculate` throws.
  It doesn't return a negative or infinite Reynolds number.
- `Calculate` converts the flow twice, once for its own velocity and once inside `Reynolds`.
  The repeated conversion and division are cheap, and they keep `Reynolds`' signature to the one the entry asks for.

## Tests

`PipeFlowTests.ReynoldsMatchesFourRhoQOverPiMuD` checks `Reynolds` against the closed form `4ρQ/(πμD)`.
It covers oil at 0.1 L/s, which is laminar, and water at 10 L/s, which is turbulent,
and it confirms that `Classify` puts each result in the expected regime.
