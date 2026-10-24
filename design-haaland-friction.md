# Add a Haaland friction model

`haaland-friction` · Task · enhancement · running · Describe

Add `HaalandFriction` in `src/LyxCsharp.Hydraulics`, a sealed class implementing `IFrictionModel`.
It returns 64/Re below Re 2300 and the Haaland formula above: 1/sqrt(f) = -1.8 log10((relativeRoughness/3.7)^1.11 + 6.9/Re).
A non-positive Reynolds number throws ArgumentOutOfRangeException, as `SwameeJainFriction` does.
Add `tests/LyxCsharp.Hydraulics.Tests/HaalandFrictionTests.cs` covering the laminar branch, a smooth turbulent value at Re 100000, roughness raising the factor, the throw, and agreement with `SwameeJainFriction` within 3 percent over a few turbulent cases.