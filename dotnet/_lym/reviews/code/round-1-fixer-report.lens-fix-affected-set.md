```yaml
lens: fix-affected-set
outcome: reported
```

Task: check that the fix for F2 (and F1) covers every member of its set.

No findings.

- F2 (doc wording under one key, "turbulent" describing the cache): the search for `turbulent (friction|factor)` and `cache` in `src` found no other doc comment that limits the cache to turbulent flow. The only other cache doc, `CachedFrictionModel.cs:10`, does not mention turbulent flow. The `PipeFlowCalculator` doc comment was the only site.
- F1 (constructors): I did not search, because the review states the parameterless constructor chains to the primary one. `PipeFlow.cs` shows `: this(new SwameeJainFriction())`, so the one new test covers both.

Tool calls:
1. `grep -rniE "turbulent (friction|factor)|cache" src`: reran F2's search for other sites that name only turbulent flow. My context did not hold the other `src` files.
