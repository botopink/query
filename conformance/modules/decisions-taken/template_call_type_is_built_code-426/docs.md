# Decision 426 — A template call's type is the type of the code it built for that call, computed at build; the template's signature is a bound, and the call unifies with the type expected where it stands.

Each `` query @`…` `` in the example has the type of the code built for that call, and the program states the type it expects. `names` returns one column as `string[]`, and `all` infers the same type with no annotation. `limit 1` builds an optional row, so `biggest` is `?string`. Two columns build a tuple per row, so `rows` is `#(string, i32)[]`. `select *` builds the rows themselves, so `whole` is `City[]`. The template's signature is only a bound. Each call unifies with the type expected where it stands, as any expression does. The program prints `Lyon,Paris`, `Paris`, `2`, `3` and `Lyon,Nice,Paris`.

`` val n: i32 = query @`select name from cities` `` is the ordinary mismatch at the call, `expected i32, got string[]`. On a database context the template builds `runOne` under `limit 1` and `runMany` otherwise. So `` return self.db.query @`select * from User`; `` in a method declared `-> Result<?User, E>` is `expected Result<?User, E>, got Result<User[], E>`, with the note `query answers a list — add limit 1 for ?User`.

Cells in this repository that prove it: `modules/generic_loader_binding` (an inferred `string[]`). The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
