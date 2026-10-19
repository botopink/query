# Decision 427 — A query's bound parameters are typed values — erika's `QueryParam` —, each built by the template from the type of the column its hole meets, checked at build.

`Users` binds holes of every column type. In `find`, `${id}` meets `User.id: i32` and is built as `QueryParam.Int(id)`. In `matching`, `${name}` becomes `Text`, `${active}` `Bool`, `${score}` `Float`, and `${nickname}`, over the `?string` column, becomes `Null` when the value is `null` and `Text` otherwise. The hole after `limit` is `Int`. `Console` is the example's own `QueryContext<string>`. Its two runners take `QueryParam[]` and a `decode` function, and it prints each parameter's variant with its value. `main` therefore prints `params: int 7`, `nobody`, `params: text Ann, bool true, float 2.5, null, int 10` and `0`. Each value reaches the driver with its type: a real NULL, a real boolean, bytes undecoded.

A hole of another type than its column is an error at the hole, at build. In `fn porId(self: Self, id: string)` with `where id = ${id}`, the error is `expected i64 (User.id: i32), got string`. `draft/comptime/entity.bp` is the program's build layer: its `#[entity]` records `query`'s `QueryTable` on a type, as a persistence library does, so the example names no library.

Cells in this repository that prove it: none yet. The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
[draft/comptime/entity.bp](draft/comptime/entity.bp)
