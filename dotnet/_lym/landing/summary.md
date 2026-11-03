# Extract PipeFlowCalculator.Reynolds from Calculate

`PipeFlowCalculator.Calculate` computed the Reynolds number inline.
It is now a public static `PipeFlowCalculator.Reynolds(Pipe, Fluid, Quantity flow)`, and `Calculate` calls it.
This is a behavior-preserving refactor: every `Calculate` result is meant to be unchanged to the last digit.
It also exercises the regression harness's baseline capture and clean compare on a hub anchored at `dotnet/`.

## Design

- The flow validation and mean-velocity computation moved into a private static `MeanVelocityMetresPerSecond`.
  `Reynolds` and `Calculate` both use it, so `Reynolds` rejects a non-positive flow with the same `ArgumentOutOfRangeException` (`flow`, "Flow must be positive.") as `Calculate`.
- `Calculate` still needs the velocity for the pressure drop, so it computes the velocity once itself and once again inside `Reynolds`.
  The arithmetic is identical, so the values are bit-for-bit the same; the cost is one redundant division and unit conversion.
  I chose that over an overload taking a precomputed velocity to keep the public surface to the single requested method.

## Tests

- `ReynoldsMatchesFourRhoQOverPiMuD` pins `Reynolds` for one laminar flow (oil) and one turbulent flow (water) against `4ρQ/(πμD)`, and checks each classifies as expected.
- `ReynoldsRejectsNonPositiveFlow` covers zero and negative flow.

## To check by hand

- Run the regression harness compare against the baseline captured before this change, and confirm it reports no drift.
  It was not run as part of writing this description.
