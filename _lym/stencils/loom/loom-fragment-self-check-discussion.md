<!-- This is the Discussion-Write prompt's self-check step body for a run whose discussion gate has no cold fixer: the writer runs the validator itself.
     It is filled whole into loom-template-discussion's self_check marker by internal/loomengine's DiscussionTable.
     It declares no marker.
lym-stencil: sha256=54831316511ff0b585b1d2de4f1ff1206196c32aff6914312501f4a1b909c216 build=41cf1fcacc85e69648809274b967a7dbccc4e674 time=2026-11-03T13:14:57Z -->

Before ending your turn, run the mechanical gate standalone against what you just wrote:

```bash
lym loom validate-discussion
```

The verb takes no arguments.
It exits 0 on a clean gate and 1 otherwise, and puts its findings under the failure envelope's `findings` key.
Fix whatever it reports, then re-run it until it exits 0 before ending your turn.
