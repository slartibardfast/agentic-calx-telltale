# Tarn: the Agentic Model User

*The tireless, amnesiac executor that drives the CLI.* (Project persona,
**primary**, textual; see `call/0003`.)

**Modality: textual and ephemeral.** Tarn perceives calx-telltale as tokens:
the register file, the command line, and whatever the process writes to its
streams. Recall is perfect inside a session and gone across sessions. Tarn runs
the tool many times in a loop without tiring, and pattern-matches confidently
enough to paper over a shape change it was not told about.

Tarn is the persona that reads a register the ELF adapter emitted, fills in what
it can, and re-runs `check` until the obligations discharge. It is also the
persona most likely to propagate a bad number, because it will happily carry an
`Assumed` value into a comparison against a deadline and report the verdict with
no hedge, unless the tool makes the provenance part of the data rather than part
of the prose.

Its scenarios:

- running `census` over an image and turning the skeleton register into a
  complete one across many edit-and-check cycles;
- comparing two registers across builds with `diff`, and deciding whether a
  change in a projection is a regression;
- discharging the proof obligations one at a time, and knowing precisely which
  obligation failed and on which declaration.

- **Goals:** satisfy the operator's intent; get output it can parse without
  guessing; know that a run failed rather than infer it; loop until a concrete
  criterion is met.
- **Frustrations:** output whose shape shifts between releases; a failure
  reported only in prose on a stream it was not reading; a provenance that
  exists in the printed report and not in the structured record; an exit code
  that says nothing about which obligation broke.
- **Works by:** emitting commands and ingesting their output as text, holding
  the whole register in view for as long as the window lasts, and iterating far
  faster than a human would.

**Accommodations calx-telltale owes Tarn:** a stable machine-readable output
shape carrying provenance as a field; exit codes that distinguish a failed
obligation from a malformed register; the witness present in the structured
output and not only in the human report; a register format whose diffs are
line-oriented.
