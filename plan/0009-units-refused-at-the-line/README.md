# 0009 units-refused-at-the-line

A composition mixing units is refused, correctly, by the arithmetic. The message
names the operation that failed and not the line that wrote it.

- **Who**: [Tarn](../../cast/tarn.md) is primary, with
  [Ines](../../cast/ines.md) behind him.
- **What**: the specs under `spec/`.
- **Why**: the refusal is right and unactionable, which is a diagnostics fault
  rather than a modelling one.

## What the cast says

Tarn's recorded frustration is "an exit code that says nothing about which
obligation broke", and this is the same complaint one level down. Tarn edits a
register and re-checks in a loop; a refusal that names an operation rather than
a line makes each turn of that loop a search. Tarn is primary because the loop
is the whole working mode, and because a structured error carrying a line number
serves it in a way prose cannot.

Ines has the matching entry: "a tool that invents a unit conversion it has no
evidence for". The tool does not invent one, which is the point, and it declines
in terms that send her back through the file to find out where.

Marek is barely affected. He reads verdicts rather than authoring registers, so
this milestone is not his and the ordering should not pretend otherwise.

## The increment

Unit agreement across a composition's operands is decidable when the composition
is parsed, because every wait it names has already been read and every wait
carries its unit. So the check moves to the parser, where the line number is
still in hand, and the refusal names the declaration that disagrees.

The arithmetic keeps its own refusal. The parser catching a case earlier does
not make the later check redundant, since the library is usable without the
register and the property belongs to the arithmetic.

## Acceptance

- A composition naming waits of differing units is refused at parse time, and
  the message carries the line and the operand that disagrees.
- The structured surface carries the line as a field rather than inside a
  sentence.
- The arithmetic still refuses the same case when reached directly, so the
  property holds for a caller that never parsed a register.
