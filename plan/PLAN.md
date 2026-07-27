# PLAN: milestone index

The ordering authority. Milestones are listed here in the order they are worked;
their folders are named `NNNN-slug` (zero-padded, and the number is identity, not a
sort key; see `call/0000`). A milestone is a thin, persona-serving increment.

The order carries a judgement. The two errors that motivated this tool were both
errors of arithmetic and of carried provenance, so the milestones that address
those come before the ones that need loop contracts, and they do not wait on
them.

- [0001 register-schema-and-census](0001-register-schema-and-census/README.md),
  the declaration format and the ELF adapter that emits a skeleton of it.
- [0002 units-provenance-intervals](0002-units-provenance-intervals/README.md),
  the `Quantity` core and its three obligations (K5, K6, K7).
- [0003 composition-and-attainment](0003-composition-and-attainment/README.md),
  the composition operators and the witness (K3, K4).
- [0004 termination-and-counter-fit](0004-termination-and-counter-fit/README.md),
  well-foundedness and counter-type fit (K1, K2), which need loop contracts.
- [0005 deadlines-and-response-time](0005-deadlines-and-response-time/README.md),
  the armed-deadline comparison, and after it interrupt response time.
