# Board

Rendered 2026-10-24 06:36 UTC; every age below is as of then.
Prime owner: `neve`.

Tasks are grouped by dependency layer and notes by type.
An entry waits only on the open entries it names under After, so the entries in one layer can run in parallel.

## Tasks

Concrete and claimable; only a task can run.

### Running

Held by a run; its scope is locked until the run ends.

| # | Task | Slug | Recipe | At | Owner | Age<br>Revised | Labels |
| --- | --- | --- | --- | --- | --- | --- | --- |
| T1 | **Add a Haaland friction model**<br>• Add HaalandFriction, an IFrictionModel using the Haaland explicit formula for turbulent flow, with NUnit tests. | [`haaland-friction`](design-haaland-friction.md) | darn | Code-Bouncer | neve | 1m<br>1m |  |

### Ready

Waits on nothing open; can start now.

_None._

## Notes

Not tasks: ideas and observations, merged into a task when one is promoted.
