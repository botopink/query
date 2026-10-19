# Decision 425 — A template call expands wherever an expression may stand.

The example puts `` query @`…` `` everywhere an expression may stand. Each call expands where it is, the same way as in a `fn` body:

- a field default (`Report`'s `cities`);
- a type's method body (`Report.biggest`, under `limit 1`, read with `??`);
- a destructuring initializer (`` val #(n, total) = query @`select count(*), sum(total) from orders` ``);
- an argument (`` count(query @`…`) ``);
- a lambda whose hole is the lambda's own parameter (`` { min -> query @`… >= ${min}` } ``);
- the arms of a `case`.

The program prints `3 orders, 20 in all`, `2`, `Lyon,Lyon`, `Lyon,Lyon`, `Lyon` and `Paris`. A template call left unexpanded anywhere is a compiler error, never a run-time `undefined`.

Cells in this repository that prove it: `modules/generic_loader_binding` (an expansion in a `fn` body). The other positions come with front 145's Conformance step.

[draft/main.bp](draft/main.bp)
