# Support log — friction-colebrook

## Interview

A darn run holds no interview.
The board entry is the whole statement of the change, and no board note or peer run contradicts it.

## Rejected alternatives

- Newton-Raphson on 1/sqrt(f): converges faster per step, but fixed-point iteration from the Swamee-Jain start already converges in a few steps and is simpler to read.
- Starting the iteration from a constant guess such as f = 0.02: works, but costs more iterations at high roughness than the Swamee-Jain start.
- Returning the last iterate when the cap is reached: hides a defect; rejected for D3's throw.
- Public tolerance and cap parameters: no caller needs them; they stay private constants.
- Lambert-W closed form: exact for smooth pipes only in its simple form, and the entry asks for an iterative solver.

## Review rounds

_No rounds yet._

## Question ledger

- Tolerance and iteration cap values — self-picked: relative 1e-12 and 50 iterations (D2).
- Behavior at the iteration cap — self-picked: throw `InvalidOperationException` (D3).
- Comparison tolerance — self-picked: 3 percent, matching the existing Haaland-versus-Swamee-Jain test (D4).
- Sweep grid — self-picked: Re 4000 to 1e8, relative roughness 0 to 0.05, residual below 1e-9 (D5).
