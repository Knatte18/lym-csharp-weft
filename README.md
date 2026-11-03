# Board

Rendered 2026-11-03 19:15 UTC; every age below is as of then.
Prime owner: `neve`.

Tasks are grouped by dependency layer and notes by type.
An entry waits only on the open entries it names under After, so the entries in one layer can run in parallel.

## [P0 Root: entries under no project](project-root.md) (2)

Owner `neve`

### Tasks (2)

Concrete and claimable; only a task can run.

#### Ready (0)

Waits on nothing open; can start now.

_None._

#### Draft (2)

Not released; nothing starts it until its status is cleared.

| # | Task | Slug | Recipe | After | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- |
| T2 | `draft` **Add Quantity.Parse**<br>• Read a quantity back from the text Quantity.ToString writes | [`quantity-parse`](design-quantity-parse.md) | darn · light |  | 30m<br>30m |  |
| T3 | `draft` **Note transitional flow in the pipe flow report**<br>• Append a transitional-flow warning to the report line | [`transitional-report-note`](design-transitional-report-note.md) | darn · light | T2 | 30m<br>30m |  |

### Notes (0)

Not tasks: ideas and observations, merged into a task when one is promoted.
