# P0 Root: entries under no project

Rendered 2026-11-03 14:33 UTC; every age below is as of then.
Under: [Board](README.md).

Owner `neve`

Tasks are grouped by dependency layer and notes by type.
An entry waits only on the open entries it names under After, so the entries in one layer can run in parallel.

## Tasks (3)

Concrete and claimable; only a task can run.

### Running (1)

Held by a run; its scope is locked until the run ends.

| # | Task | Slug | Recipe | At | Owner | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- | --- |
| T1 | **Pull the Reynolds number computation out of PipeFlowCalculator.Calculate** | [`pipeflow-reynolds-helper`](design-pipeflow-reynolds-helper.md) | darn · light | Code-Bouncer | neve | 16m<br>16m |  |

### Ready (0)

Waits on nothing open; can start now.

_None._

### Draft (2)

Not released; nothing starts it until its status is cleared.

| # | Task | Slug | Recipe | After | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- |
| T2 | `draft` **Add ChurchillFriction, one friction model valid across every flow regime** | [`churchill-friction`](design-churchill-friction.md) | darn · light | T1 | 16m<br>16m |  |
| T3 | `draft` **The pipe flow report prints its values to 4 significant digits** | [`report-significant-digits`](design-report-significant-digits.md) | darn · light | T2 | 16m<br>16m |  |

## Notes (0)

Not tasks: ideas and observations, merged into a task when one is promoted.
