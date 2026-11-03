# Add Quantity.Parse

`quantity-parse` · Task · enhancement

## Goal

Let callers read a quantity back from the text `Quantity.ToString` writes, such as `7.2 l/min` or `1.5 bar`, so a flow or pressure can come from a config file or a command line.

## Change

- Add `public static Quantity Parse(string text)` to `Quantity` in `dotnet/src/LymCsharp.Units/Quantity.cs`.
  - It reads a number, one space and a unit symbol, the form `Quantity.ToString` writes.
  - It looks the symbol up through `UnitRegistry.Find`, so an unknown symbol throws `KeyNotFoundException` as `Find` does.
  - Text with no space in it throws `FormatException`.
- Nothing else changes: no caller uses `Parse` yet, and no calculation or report output moves.

## Tests

Add to `dotnet/tests/LymCsharp.Units.Tests/QuantityTests.cs`:

- `Parse("1.5 bar")` gives the value 1.5 in `UnitCatalog.Bar`;
- `Parse("7.2 l/min")` gives the value 7.2 in `UnitCatalog.LitrePerMinute`;
- `Parse(q.ToString())` gives back `q` for a quantity such as 3.25 psi;
- an unknown symbol throws `KeyNotFoundException`;
- text with no space throws `FormatException`.
