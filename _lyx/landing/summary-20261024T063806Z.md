# Add a Haaland friction model

Adds `HaalandFriction`, a second `IFrictionModel` in `LyxCsharp.Hydraulics` beside `SwameeJainFriction`.
Below Re 2300 it returns the laminar 64/Re;
at or above it, it solves the Haaland explicit formula 1/√f = −1.8 log10((ε/D / 3.7)^1.11 + 6.9/Re) for the Darcy factor.
A non-positive Reynolds number throws `ArgumentOutOfRangeException`, with the same message as `SwameeJainFriction`.

## Tests

`tests/LyxCsharp.Hydraulics.Tests/HaalandFrictionTests.cs` covers the laminar branch, a smooth turbulent factor at Re 100 000 in the 0.017–0.020 range, roughness raising the factor, the throw for zero and negative Reynolds numbers, and agreement with `SwameeJainFriction` within 3 percent relative across smooth and rough pipes from Re 10^4 to 10^7.

## Design decisions

- The laminar cut-off stays at 2300, matching `SwameeJainFriction`, so the two models are interchangeable behind `IFrictionModel`.
- The laminar branch and Reynolds guard are duplicated rather than pulled into a shared base class; two short call sites don't justify the abstraction.
- "Within 3 percent" is measured relative to the Swamee-Jain value, via NUnit's `Within(3).Percent`.
