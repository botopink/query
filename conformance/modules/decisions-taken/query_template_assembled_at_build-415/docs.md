# Decision 415 — `self.db.query "…"` is assembled wholly at build through `@Decl`: the template method's receiver and literal are both `comptime` expressions, and `q.lookup(name)` answers the call site's declaration as a read-only `?@Decl` whose data meta is read at build.

`Club`'s two methods each write a query on `self.db`, and the whole statement is assembled at build. The template method takes the receiver `self.db` as the call site's code and the template text as its second `syntax` parameter. It resolves `Member` with `compiler.lookup` where the call stands, as a read-only declaration handle. It reads `compiler.meta<QueryTable>` on it, which gives the table `members` with `name` as `full_name` and `since` as `joined_year`. It checks each field of the text against those columns and builds the call to the context's `runMany` or `runOne`. The program then holds the SQL as a constant and computes only the hole `${before}`. `Console` prints the statement it receives, so `main` prints the `veterans` statement, `0`, the `founder` statement and `nobody`.

When `compiler.lookup` finds no `Member` in scope, the error is at the token. A type with no `QueryTable` is "`Member` is not an entity — annotate it with #[entity(…)]", and a field it lacks is "`Member` has no field `…`" at the field. Writing to the handle the lookup answers is refused. `draft/comptime/entity.bp` is the program's build layer: its `#[entity]` records `query`'s `QueryTable` on a type, as a persistence library does, so the example names no library.

Cells in this repository that prove it: none yet. The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
[draft/comptime/entity.bp](draft/comptime/entity.bp)
