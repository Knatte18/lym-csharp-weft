# Capture/compare regression harness

`regression-harness` · Task · enhancement · draft

## Seen (operator, 2026-10-25)

LoomYard is gaining regression rows (Drift-Await, Drift-Verify) that compare a run's numerical output against a captured baseline.
Their real target is Models on OSL-1046, whose `NORCE.Models.NumericalBaseline` captures scrubbed output and compares against it.
Proving those rows on Models alone is slow and ties them to one machine.

## Design

This repository gets a small capture/compare regression harness on the pattern of `NORCE.Models.NumericalBaseline`:

- A capture mode runs a fixed set of cases through `LyxCsharp.Hydraulics` and `LyxCsharp.Reporting` and writes their scrubbed output, one file per case, into a gitignored baseline directory.
- Scrubbing removes what changes between runs without a code change, such as timestamps and machine paths, and fixes the number formatting so a baseline is stable across machines.
- A compare mode runs the same cases and fails on the first deviation from the baseline, naming the case, the line and both values.
- Both modes run from one command the hub's regression config can name, so LoomYard's Drift rows can call capture before a change and compare after it.
- The harness is ordinary NUnit-hosted .NET code in this repository, with its own tests for capture, scrub and compare.

The run also proves the whole C# path of a loom run in this hub: Discussion, Plan, Webster, the code perch and Finalize on a .NET repository.

## Out of scope

- Turning on LoomYard's regression rows in this hub: that is a config change once LoomYard ships them.
- Any change to the existing hydraulics or units behaviour.
