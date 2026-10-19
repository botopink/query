# Decision 312 — erika queries a database too, as LINQ does: one grammar, two targets, picked by the source.

The example writes one grammar against two sources. `cities` is an array, so `` query @`select name from cities where pop >= 5 order by name asc` `` runs in memory as the fluent pipeline and prints `Lyon,Paris`. The number of rows is written in the query: `limit 1` makes the answer `?string`, which is `Paris`. In `Atlas.big`, `` self.db.query @`select * from City where pop >= ${min} order by name asc` `` runs on a database context instead. `City` names a type imported in the module, its table and columns are the `QueryTable` meta its `#[entity]` recorded, and the template builds SQL text plus the hole `${min}` as a bound parameter, never as text. `Console` is the example's own `QueryContext<string>`: it prints the statement it is handed and answers no rows, so the last line is `0`.

`draft/comptime/entity.bp` is the program's build layer: its `#[entity]` records `query`'s `QueryTable` on a type, as a persistence library does, so the example names no library. A `from` naming a type with no import is an error at the token, and a field `City` lacks is an error at the field. A `?T` answer without `limit 1`, or a list answer with it, is a compile error at the call (426).

Cells in this repository that prove it: `modules/generic_loader_binding` (the in-memory form). The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
[draft/comptime/entity.bp](draft/comptime/entity.bp)
