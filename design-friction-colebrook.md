# Add a Colebrook-White friction model

`friction-colebrook` · Task · enhancement · running · Code-Burler-Fix

## Goal

Add a Colebrook-White friction model to `LymCsharp.Hydraulics`, beside the Haaland and Swamee-Jain models, as the first end-to-end darn run on a C# repo.

## Scope

- `ColebrookFriction`, an `IFrictionModel`, solving the Colebrook-White equation iteratively with a fixed tolerance and an iteration cap.
- Tests in `LymCsharp.Hydraulics.Tests` that compare it against the Haaland and Swamee-Jain models over the turbulent range, within a stated tolerance.
- One test in the `Slow` category: a convergence sweep over a wide grid of Reynolds numbers and relative roughnesses.
- Nothing in `LymCsharp.Regression`, `LymCsharp.Units` or `LymCsharp.Reporting`.

## What the run proves

The plan in C# glyphs, Webster writing C#, and the gate running `dotnet test` with and without the `Slow` category, in a hub anchored in the `dotnet` subdirectory.
