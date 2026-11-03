# Add Quantity.Parse

`Quantity.Parse(string text)` reads a quantity back from the text `Quantity.ToString` writes, such as `7.2 l/min` or `1.5 bar`,
so a flow or pressure can come from a config file or a command line.

## Behavior

- The text splits at its first space: the part before it is the value, the part after it the unit symbol.
- The symbol is looked up through `UnitRegistry.Find`, so an unknown symbol throws `KeyNotFoundException`, and the match ignores case as `Find` does.
- Text with no space throws `FormatException`, and so does a value part that is not a number.

## Design decisions

- The value is read in the current culture, because `ToString` writes it in the current culture;
  this keeps `Parse(q.ToString())` returning `q` on any machine.
- No caller uses `Parse` yet, and no calculation or report output changes.

## Tests

`QuantityTests` covers `1.5 bar` and `7.2 l/min` parsing to their catalog units, a round trip through `ToString` for 3.25 psi, an unknown symbol, and text with no space.
