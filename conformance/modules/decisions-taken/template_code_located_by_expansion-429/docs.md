# Decision 429 — The compiler locates template-built code by its expansion — every loc-keyed plan keyed by (expansion, offset); this fix lands first and pauses the fronts that would add a padded expansion.

The example writes the same query text twice in one module, in `big` and `alsoBig`, and the same text with a hole twice, in `atLeast` and `atLeastAgain`. Each expansion builds identical code, with its own lambdas and its own hole binding. The compiler keys every built node by its expansion and its offset in the built string, so the two never collide on any target and no padding tells them apart. The program prints `Lyon,Paris` twice, then `Paris` and `Lyon,Nice,Paris`. An error in built code is reported at the word of the template text it was copied from.

Cells in this repository that prove it: `modules/generic_loader_binding` (one expansion in a module). The twin expansions come with front 145's Conformance step.

[draft/main.bp](draft/main.bp)
