# Support log — Add a Haaland friction model

## Interview

A darn run holds no interview; the writer worked from the board entry and a reading of the hydraulics project alone.

## Rejected alternatives

- Adding the Haaland cases as rows in `FrictionTests`: rejected because the board entry names a separate file and `FrictionTests` is bound to `SwameeJainFriction` through its field.
- Extracting a shared base class for the laminar branch and Reynolds guard: rejected as premature; two short call sites do not justify the abstraction.

## Review rounds

_No rounds yet._

## Question ledger

- Q: How is "within 3 percent" measured? Self-pick: relative to the Swamee-Jain value. Resolved.
- Q: Which turbulent cases to compare? Self-pick: smooth and rough pipes across Re 10^4 to 10^7. Resolved.
