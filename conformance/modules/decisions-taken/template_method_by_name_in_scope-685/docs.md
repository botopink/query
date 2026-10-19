# Decision 685 — A template method is found by a name in the call site's scope: `` value.method @`…` `` resolves `method` as a template whose `syntax self: R` receiver takes the receiver (577 (3), 578 (1)); a receiver's type never imports code by itself.

`Shelf.recent` writes `` self.db.query @`…` ``, and `query` there is found the way any name is: by the import at the top, `import {query, …} from "query";`. The compiler resolves `query` in the call site's scope as a template whose first `syntax` parameter is the receiver, so `self.db` is handed to it. The receiver's type, `Console` (a `QueryContext<string>`), brings no code with it. Without `query` in the import the call is an unresolved name, even though the receiver implements `QueryContext`. The program prints the statement, then `0`. `draft/comptime/entity.bp` is the program's build layer: its `#[entity]` records `query`'s `QueryTable` on a type, as a persistence library does, so the example names no library.

Cells in this repository that prove it: none yet. The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
[draft/comptime/entity.bp](draft/comptime/entity.bp)
