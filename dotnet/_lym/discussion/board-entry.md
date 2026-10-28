# Board entry `regression-harness`: Capture/compare regression harness

A NumericalBaseline-style capture/compare harness, so LoomYard Drift rows can be proved here before Models

## Seen (operator, 2026-10-25)

LoomYard has regression rows (Drift-Baseline, Drift-Await, Drift-Verify) that compare a run's numerical output against a captured baseline, off by default behind the hub-wide `drift.enabled` flag.
Their real target is Models on OSL-1046, whose `NORCE.Models.NumericalBaseline` captures scrubbed output and compares against it.
Proving those rows on Models alone is slow and ties them to one machine.

## Design

This repository gets a small capture/compare regression harness on the pattern of `NORCE.Models.NumericalBaseline`:

- A capture mode runs a fixed set of cases through `LymCsharp.Hydraulics` and `LymCsharp.Reporting` and writes their scrubbed output, one file per case, into a gitignored baseline directory.
- Scrubbing removes what changes between runs without a code change, such as timestamps and machine paths, and fixes the number formatting so a baseline is stable across machines.
- A compare mode runs the same cases and fails on the first deviation from the baseline, naming the case, the line and both values.
- Both modes run from one command the hub's regression config can name, so LoomYard's Drift rows can call capture before a change and compare after it.
- The harness is ordinary NUnit-hosted .NET code under `dotnet/`, with its own tests for capture, scrub and compare.

## What the run proves

This is a darn run, the last check of the C# path before Models (operator, 2026-10-28): a darn writer writing C#, the `dotnet test` gate, the code perch and Finalize, with the hub anchored in the subdirectory `dotnet/` (moved there on 2026-10-28), so the per-worktree anchor path is proved end to end.

## Out of scope

- Turning on `drift.enabled` in this hub: the harness must exist before a run can capture a baseline with it, so the Drift rows are proved by a later run.
- Any change to the existing hydraulics or units behaviour.
