```yaml
lens: fix-affected-set
outcome: reported
```

**Fix-affected-set lens: no findings.**

I reran F1's search: which public entry points reach the flow guard `MeanVelocityMetresPerSecond` (`src/LymCsharp.Hydraulics/PipeFlow.cs:48-53`).

Two public methods call it, and the changed test file now covers both:
- `Calculate` (`PipeFlow.cs:39`) is pinned by `NonPositiveFlowThrows`.
- `Reynolds` (`PipeFlow.cs:33`) is pinned by the new `ReynoldsRejectsNonPositiveFlow`, with rows 0.0 and -1.0.

The other `ArgumentOutOfRangeException` guards sit in the friction models (`ColebrookFriction.cs:17`, `SwameeJainFriction.cs:10`, `HaalandFriction.cs:10`). They check `reynolds`, not flow, so F1's premise doesn't reach them.

Tool calls:
1. grep of `src/LymCsharp.Hydraulics` for guards, public methods and callers of `MeanVelocityMetresPerSecond`. The context lacked `PipeFlow.cs` and the friction files.
2. A first grep with a wrong path (`src/LymCsharp.Hydraulics/PipeFlowCalculator.cs`) returned no match. I reran it against the project directory.
