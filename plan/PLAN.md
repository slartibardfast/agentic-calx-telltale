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
  the interrupt verdicts and the schedulability analysis. **Built**, the
  armed-interval comparison among them. Priority entered through
  [call/0010](../call/0010-response-time-over-a-priority-ordered-set.md), which
  supersedes the exclusion the earlier decision recorded.
- [0003 composition-and-attainment](0003-composition-and-attainment/README.md),
  the composition operators, accumulator fit, and the witness. **Built.** It
  moved ahead of the register schema because the schema has to serialise the
  expression tree, so designing the format first would have been guesswork.
- [0001 register-schema-and-census](0001-register-schema-and-census/README.md),
  the declaration format and the ELF adapter that emits a skeleton of it. **The
  format and the command line are built**, and the adapter this one left
  outstanding was built under 0008.
- [0004 termination-and-counter-fit](0004-termination-and-counter-fit/README.md),
  well-foundedness and counter-type fit. **Built**, and without the loop
  contracts this entry expected: declaring how the counter moves turned the
  question into a decision about the declaration rather than a search over the
  loop.
- [0006 declarations-cite-their-source](0006-declarations-cite-their-source/README.md),
  a declaration names the file and symbol it came from, so a verdict points at
  code. **Built**, which meets the accommodation Marek's persona records.
- [0007 the-promised-verbs](0007-the-promised-verbs/README.md), the five
  subcommands the documentation described and the binary did not answer to.
  **Built**, and it sat behind 0006 so that a verdict carried its citation from
  the first version.
- [0008 census-adapter](0008-census-adapter/README.md), the skeleton register
  drafted from the image. **Built**, as a workspace member beside the core.
- [0011 branch-decoding](0011-branch-decoding/README.md), the loops inside a
  function, which is what makes the census a census. **Built**, by delegating
  instruction boundaries to a toolchain listing rather than decoding them.
  [call/0012](../call/0012-boundaries-are-delegated-to-the-listing.md) settles
  that on the measured case rather than on the arithmetic of effort.

The two that remain are ordered by what the cast asks for rather than by what is
interesting to build. Tarn's loop is broken by a message that names no line, so
that goes ahead of the other and is small. Marek pays twice for hand-declared
arrival rates, and the machinery to derive them already exists.

- [0009 units-refused-at-the-line](0009-units-refused-at-the-line/README.md), a
  composition mixing units named at the line that wrote it rather than at the
  operation that failed.
- [0010 arrivals-from-the-clock-tree](0010-arrivals-from-the-clock-tree/README.md),
  a periodic interrupt deriving its period from the clock that drives it, so one
  fewer number is typed and the clock's provenance reaches the verdict.
