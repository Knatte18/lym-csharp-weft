# Add Quantity.Parse to read back Quantity.ToString text

`Quantity.Parse(string)` reads the text `Quantity.ToString` writes, such as `7.2 l/min` or `1.5 bar`.
A flow or pressure can now come from a config file or a command line.
Nothing calls `Parse` yet, and no calculation or report output changes.

## Behavior

- The text is a number, one space and a unit symbol.
- The symbol is looked up through `UnitRegistry.Find`, so an unknown symbol throws `KeyNotFoundException` as `Find` does.
- Text with no space throws `FormatException`.
- A number part that is not a number also throws `FormatException`.

## Design decisions

- The number is read in the current culture with `NumberStyles.Float`, because `ToString` writes it in the current culture.
  A round trip therefore holds under any culture, and `1.5 bar` fails to parse under `de-DE`.
- `NumberStyles.Float` excludes thousands separators, so `1,5 bar` is rejected under the invariant culture instead of reading as 15.
- `QuantityTests` runs under `[SetCulture("")]`, the invariant culture, so the tests do not depend on the machine's locale.
  One test switches to `de-DE` to pin the culture-dependent reading.

## Tests

`QuantityTests` covers:

- the `1.5 bar` and `7.2 l/min` cases;
- the round trip through `ToString` for 3.25 psi;
- an unknown symbol;
- text with no space;
- a non-numeric value and a thousands-separator value;
- the `de-DE` decimal separator.

## To check by hand

- `Quantity.Parse`, non-numeric value: `abc bar` throws `FormatException` and the test `ParsingTextThatCannotBeSplitOrReadThrows` pins it.
- `Quantity.Parse`, value syntax: `1,5 bar` is rejected under the invariant culture and not read as 15.
