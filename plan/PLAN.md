# PLAN: milestone index

The ordering authority. Milestones are listed here in the order they are worked;
their folders are named `NNNN-slug` (zero-padded, and the number is identity, not a
sort key; see `call/0000`). A milestone is a thin, persona-serving increment.

The order carries a judgement. The two errors that motivated this tool were both
errors of arithmetic and of carried provenance, so the milestones that address
those come before the ones that need loop contracts, and they do not wait on
them.

The response-time work sits ahead of the termination work for the same reason,
settled in [call/0008](../call/0008-interrupts-are-declared-and-answerability-is-a-verdict.md).
It depends on composition rather than on loop contracts, and it is the question
the whole enquiry began with. Numbers are identity rather than sort keys, so the
folders keep the names they were given and only this index moves.

- [0002 units-provenance-intervals](0002-units-provenance-intervals/README.md),
  the `Quantity` core and its three obligations over units, provenance and
  intervals. **Built.**
- [0005 deadlines-and-response-time](0005-deadlines-and-response-time/README.md),
  the interrupt verdicts and the schedulability analysis. **Built**, apart from
  the armed-interval comparison.
- [0003 composition-and-attainment](0003-composition-and-attainment/README.md),
  the composition operators, accumulator fit, and the witness. **Built.** It
  moved ahead of the register schema because the schema has to serialise the
  expression tree, so designing the format first would have been guesswork.
- [0001 register-schema-and-census](0001-register-schema-and-census/README.md),
  the declaration format and the ELF adapter that emits a skeleton of it.
- [0004 termination-and-counter-fit](0004-termination-and-counter-fit/README.md),
  well-foundedness and counter-type fit, which need loop contracts.
