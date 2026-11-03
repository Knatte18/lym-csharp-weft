# Board

Rendered 2026-11-03 18:44 UTC; every age below is as of then.
Prime owner: `neve`.

Tasks are grouped by dependency layer and notes by type.
An entry waits only on the open entries it names under After, so the entries in one layer can run in parallel.

## [P0 Root: entries under no project](project-root.md) (3)

Owner `neve`

### Tasks (3)

Concrete and claimable; only a task can run.

#### Ready (0)

Waits on nothing open; can start now.

_None._

#### Draft (3)

Not released; nothing starts it until its status is cleared.

| # | Task | Slug | Recipe | After | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- |
| T1 | `draft` **Cache friction factors in PipeFlowCalculator**<br>• Skip recomputing the friction factor for a flow already solved; results unchanged | [`cached-friction-factor`](design-cached-friction-factor.md) | darn · light |  | 0m<br>0m |  |
| T2 | `draft` **Add Quantity.Parse**<br>• Read a quantity back from the text Quantity.ToString writes | [`quantity-parse`](design-quantity-parse.md) | darn · light | T1 | 0m<br>0m |  |
| T3 | `draft` **Note transitional flow in the pipe flow report**<br>• Append a transitional-flow warning to the report line | [`transitional-report-note`](design-transitional-report-note.md) | darn · light | T2 | 0m<br>0m |  |

### Notes (0)

Not tasks: ideas and observations, merged into a task when one is promoted.
