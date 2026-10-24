# Add HaalandFriction, an explicit turbulent friction model

`LyxCsharp.Hydraulics` gains `HaalandFriction`, a sealed `IFrictionModel` that offers the Haaland explicit formula as a second turbulent correlation beside `SwameeJainFriction`.
Callers that want Haaland pass it where they pass any `IFrictionModel`; nothing existing changes.

The model returns `64 / Re` below Re 2300.
At or above 2300 it returns `f = 1 / (-1.8 log10((relativeRoughness / 3.7)^1.11 + 6.9 / Re))^2`.
A non-positive Reynolds number throws `ArgumentOutOfRangeException` for `reynolds`, with the same message as `SwameeJainFriction`.

No issue is resolved by this change, so there is no `Closes` line.

## Design decisions

- The 2300 laminar cut-off and the guard match `SwameeJainFriction`, so the two models are interchangeable.
- `SwameeJainFriction`, `IFrictionModel` and `PipeFlow`'s default model are untouched.
- The tests live in their own `HaalandFrictionTests` fixture rather than in `FrictionTests`.

## Tests

`HaalandFrictionTests` covers:

- the laminar branch;
- a smooth turbulent factor at Re 100000, in the range 0.017 to 0.020;
- roughness raising the factor;
- the throw for zero and negative Reynolds numbers;
- agreement with `SwameeJainFriction` within 3 percent relative over four cases spanning Re 10^4 to 10^7 and smooth to rough pipes.

The agreement bound is not loosened to fit: Haaland and Swamee-Jain can differ by more than 3 percent at some roughness and Reynolds combinations, so the cases were chosen where both correlations are valid and agree.

## To check by hand

`dotnet test` for the hydraulics test project was not run as part of writing this description.
