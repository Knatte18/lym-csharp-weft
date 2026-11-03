# The pipe flow report prints its values to 4 significant digits

`report-significant-digits` · Task · enhancement · running · Code-Burler-Review

`ResultFormatter` (`src/LymCsharp.Reporting/ResultFormatter.cs`) formats the pipe flow report's numbers.

## Expected

- The report prints every numeric value to 4 significant digits.
- The formatter's tests follow the new format.
- The regression cases that render the formatted report now drift on purpose, and the run records that drift as intended.

## Why

This run proves that Drift reports an intended output change and that the run's Drift verify accepts it with a stated reason, on a hub whose anchor is `dotnet/`.
