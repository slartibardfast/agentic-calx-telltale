# Cast: the project's Who

Personas are hypothetical archetypal actors that keep the work grounded in the
people it serves. The workshop process is in
[applying-personas.md](applying-personas.md); its story steps become `plan/`
milestones, and a scenario a persona hits becomes acceptance criteria under a
milestone's spec. (Powell, Keenan & McDaid 2007, after Cooper & Reimann,
Interaction Design.)

calx-telltale is consumed in two modalities that need different accommodations,
so the cast splits first on modality rather than on domain. It splits again on
role, because two people can share a modality and still want opposite things
from the same output. The domain scenarios are use-cases every persona engages:

- declaring a register of waits and windows for an image, and getting a
  worst-case cost for each composition out of it;
- finding where a maximum is attained, when the attaining input sits inside the
  budget instead of at its boundary;
- telling a derived number from an assumed one at the point of reading a result.

What differs is how each persona can engage those, and therefore what
calx-telltale owes each.

## This project's cast

- [Ines](ines.md), the human operator. **Primary** (embodied, intermittent):
  declares the register by hand, curates what the adapter emits, and reads
  results by eye against their own model of the hardware.
- [Tarn](tarn.md), the agentic model user. **Primary** (textual, ephemeral):
  drives the CLI programmatically, and needs deterministic structured output and
  explicit failure.
- [Marek](marek.md), the firmware engineer. **Primary** (embodied, pressed):
  owns the code the register describes, and reads a verdict to find out what to
  change and whether it matters before the release.

All three are primary because an interface designed for one cannot satisfy the
others (Cooper 2004). A human-facing surface of prose, examples and readable
output does not serve an agent, and a stable structured surface does not serve a
human reading by eye. calx-telltale therefore owes each its own accommodation,
set as a rule in [call/0003](../call/0003-cast-modalities.md).

Ines and Marek share a modality and still pull apart, which is why role is a
second axis rather than a footnote. Ines reads a projection and asks whether to
trust it. Marek reads the same projection and asks what to change. A verdict
that satisfies the first can be useless to the second: trustworthy, and
attached to nothing anyone can open.

## The shared substrate

Both personas operate the same model. These are the facts every persona and
every spec must respect:

- Every number is a `Quantity`: an interval, a unit, and a provenance. The model
  holds no bare integers.
- A result carries the weakest provenance of anything in its derivation. One
  `Assumed` input makes the whole answer `Assumed`, and it is labelled that way
  wherever it is printed.
- `Iterations` and `BusReads` have no conversion to time. Supplying one requires
  a declared `Conversion` that carries its own provenance.
- Evaluation returns a cost together with the witness, which is the input at
  which that cost is attained.
- The arithmetic is Kani-verified. The register it computes over is a human's
  declaration, so a verified result is a claim about the model and never about
  the firmware.
- Several failure classes sit outside the model by construction, including
  execute-in-place instruction fetch, hangs that are not loops, and windows that
  cross a function boundary. The tool states its exclusions on every run, so an
  exclusion is never read as coverage.
