# Decision record — Add a Haaland friction model

## Goal

Offer a second explicit turbulent friction correlation beside Swamee-Jain:
`HaalandFriction`, an `IFrictionModel` using the Haaland formula.

## Scope

- New sealed class `HaalandFriction` in `src/LyxCsharp.Hydraulics/HaalandFriction.cs`, implementing `IFrictionModel`.
- New NUnit fixture `tests/LyxCsharp.Hydraulics.Tests/HaalandFrictionTests.cs`.
- Out of scope: changing `SwameeJainFriction`, `PipeFlow`'s default model, or `IFrictionModel`.

## Decisions

### Laminar and turbulent branches mirror SwameeJainFriction

Decision: below Re 2300 return 64/Re;
at or above it return f = 1 / (-1.8 log10((relativeRoughness / 3.7)^1.11 + 6.9 / Re))^2.
Rationale: the board entry fixes both branches, and the 2300 cut-off matches the existing model so the two are interchangeable.

### Non-positive Reynolds throws ArgumentOutOfRangeException

Decision: a Reynolds number at or below zero throws `ArgumentOutOfRangeException` naming `reynolds`, with the same message as `SwameeJainFriction`.
Rationale: the board entry asks for parity with the existing model's guard.

### A separate test fixture per model

Decision: Haaland tests live in their own `HaalandFrictionTests` fixture, leaving `FrictionTests` untouched.
Rationale: the board entry names the file, and one fixture per class keeps each model's behavior in one place.

## Constraints

- Target framework and language settings come from `Directory.Build.props`; no new package references.
- Tests use NUnit's constraint model, as the existing fixtures do.

## Auto-mode assumptions

- "Agreement within 3 percent" means relative difference |haaland - swameeJain| / swameeJain ≤ 0.03.
- The agreement cases span smooth and rough pipes across Re 10^4 to 10^7, where both correlations are valid.

## Open risks

- Haaland and Swamee-Jain differ by up to a few percent at some roughness and Reynolds combinations; a case at the edge could exceed 3 percent and needs a different pick rather than a looser bound.

## Acceptance criteria

- Laminar branch returns 64/Re for Reynolds below 2300.
- Smooth turbulent factor at Re 100000 lies in the expected range near 0.018.
- A rougher pipe yields a higher factor at the same Reynolds number.
- Zero and negative Reynolds numbers throw `ArgumentOutOfRangeException`.
- Over several turbulent cases, the factor agrees with `SwameeJainFriction` within 3 percent relative.
- `dotnet test` passes for the hydraulics test project.
