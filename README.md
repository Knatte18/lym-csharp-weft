# Board

Rendered 2026-11-03 18:52 UTC; every age below is as of then.
Prime owner: `neve`.

Tasks are grouped by dependency layer and notes by type.
An entry waits only on the open entries it names under After, so the entries in one layer can run in parallel.

## [P0 Root: entries under no project](project-root.md) (3)

Owner `neve`

### Tasks (3)

Concrete and claimable; only a task can run.

#### Running (1)

Held by a run; its scope is locked until the run ends.

| # | Task | Slug | Recipe | At | Owner | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- | --- |
| T1 | **Cache friction factors in PipeFlowCalculator**<br>• Skip recomputing the friction factor for a flow already solved; results unchanged | [`cached-friction-factor`](design-cached-friction-factor.md) | darn · light | Drift-Recheck | neve | 7m<br>7m |  |

#### Ready (0)

Waits on nothing open; can start now.

_None._

#### Draft (2)

Not released; nothing starts it until its status is cleared.

| # | Task | Slug | Recipe | After | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- |
| T2 | `draft` **Add Quantity.Parse**<br>• Read a quantity back from the text Quantity.ToString writes | [`quantity-parse`](design-quantity-parse.md) | darn · light | T1 | 7m<br>7m |  |
| T3 | `draft` **Note transitional flow in the pipe flow report**<br>• Append a transitional-flow warning to the report line | [`transitional-report-note`](design-transitional-report-note.md) | darn · light | T2 | 7m<br>7m |  |

### Notes (0)

Not tasks: ideas and observations, merged into a task when one is promoted.
