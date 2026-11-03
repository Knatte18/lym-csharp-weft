# Pull the Reynolds number computation out of PipeFlowCalculator.Calculate

`pipeflow-reynolds-helper` · Task · enhancement · draft

`PipeFlowCalculator.Calculate` (`src/LymCsharp.Hydraulics/PipeFlow.cs`) computes the Reynolds number inline.

## Expected

- A public static `PipeFlowCalculator.Reynolds(Pipe, Fluid, Quantity flow)` computes it, and `Calculate` calls it.
- Every result `Calculate` returns is unchanged to the last digit, so the regression harness reports no drift.
- A test pins `Reynolds` for one laminar and one turbulent flow.

## Why

A behavior-preserving refactor: this run proves that Drift captures a baseline and compares clean on a hub whose anchor is `dotnet/`.
