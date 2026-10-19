# Decision 397 — A database query in a method body is written on the database context — `self.db.query "select * from User where active = true"` —, and `from User` names an entity whose table and columns erika reads from the type at build.

`Directory` holds a database context and writes each query in a method body, on that context: `` self.db.query @`select * from User where active = true` ``. `from User` names the entity, and at build the template reads `User`'s `QueryTable` (the table `users`, `name` stored as `user_name`) to build the SQL. `named` binds `${name}` as a parameter and, under `limit 1`, answers `Result<?User, string>`, while `active` answers `Result<User[], string>`. `Console` implements `query`'s `QueryContext<string>` with its two runners. `string` is its own error, so `query` names no library's. It prints each statement and answers no rows, so `main` prints the `active` statement, `0`, the `named` statement and `nobody`.

`draft/comptime/entity.bp` is the program's build layer: its `#[entity]` records `query`'s `QueryTable` on a type, as a persistence library does, so the example names no library. A type with no `QueryTable` is an error at the query, "`User` is not an entity — annotate it with #[entity(…)]", and a field the type lacks is an error at the field.

Cells in this repository that prove it: `modules/query_context_declared` (the declared `QueryTable` and `QueryColumn`). The database form waits on front 145's database-target steps (397, 415, 427): `QueryContext<E>` still declares a single `run<T>(self, sql, params: Array<string>)`, `QueryParam` and the runners `runMany` / `runOne` are not declared, and the template method on a context is not built.

[draft/main.bp](draft/main.bp)
[draft/comptime/entity.bp](draft/comptime/entity.bp)
