# Support log — regression-harness

## Interview

A darn run holds no interview; the scope comes from the board entry and a reading of the code and the drift config template.

## Rejected alternatives

- **Host the cases as NUnit tests driven by `dotnet test` run settings.**
  Drift's command must hold `{baseline_dir}`; passing it as `TestRunParameters` means nested quoting through YAML and a shell, and the gate's tier filters would pick the cases up unless every tier excluded them.
- **Compare numbers with a relative tolerance.**
  Two stability rules (scrub precision and tolerance) would overlap; fixing precision in the scrubber keeps the baseline text the single source of truth.
- **Stop compare at the first drifted case.**
  Drift-Verify judges every drifted test, and a partial list would cost it a compare run per drifted case.
- **Write the hub's drift `capture`/`compare` config in this run.**
  The board puts turning drift on out of scope, and the config is hub-wide, committed in the board dir rather than on this branch.

## Review rounds

_No rounds yet._

## Question ledger

- Does "NUnit-hosted" require the cases to run as NUnit tests? Self-pick: no; the harness is NUnit-tested library code behind a console entry point (see Decisions).
- Should compare stop at the first deviation overall or per case? Self-pick: per case, every case checked.
- What precision fixes the number formatting? Self-pick: 10 significant digits, invariant culture.
- What does an unknown skip name do? Self-pick: exit 2, naming it.
