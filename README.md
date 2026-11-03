# Board

Rendered 2026-11-03 15:07 UTC; every age below is as of then.
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
| T2 | `draft` **Add ChurchillFriction, one friction model valid across every flow regime** | [`churchill-friction`](design-churchill-friction.md) | darn · light |  | 50m<br>50m |  |
| T3 | `draft` **The pipe flow report prints its values to 4 significant digits** | [`report-significant-digits`](design-report-significant-digits.md) | darn · light | T2 | 50m<br>50m |  |

### Notes (0)

Not tasks: ideas and observations, merged into a task when one is promoted.

### Done (1)

Finished, awaiting `lym board prune --apply`.

| # | Entry | Slug | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- |
| T1 | **Pull the Reynolds number computation out of PipeFlowCalculator.Calculate** | [`pipeflow-reynolds-helper`](design-pipeflow-reynolds-helper.md) | 50m<br>50m |  |
