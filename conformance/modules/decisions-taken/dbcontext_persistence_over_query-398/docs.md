# Decision 398 — The persistence layer is a library of its own over erika — `dbcontext` (`botopink/dbcontext` at `repository/dbcontext`, 326's rule), botopink's JPA.

The example shows which layer owns what. `#[entity("users")]` and `#[column("user_name")]` come from the persistence library, `rakun-data` (724's merge of `dbcontext`), and what they record is `query`'s own `QueryTable`. `main` reads that record at build with `compiler.typeInfo(User).meta(QueryTable)` and prints `users`, then `id->id,name->user_name,active->active`. `Directory` holds a `DbContext`, `rakun-data`'s implementation of `query`'s `QueryContext`, and writes its query in `query`'s grammar on it. `query` is the query language in memory and in SQL. The persistence library depends on it and on std and holds the entities, the context over its `Driver`, `#[repository]` (313), `` #[query @`…`] ``, `#[nativeQuery(…)]` and the row decoding. Neither names a framework.

Cells in this repository that prove it: `modules/query_context_declared` (`query`'s half: the records a persistence library fills). The persistence half is `rakun-data`'s, built in front 150; `query`'s database-target steps are front 145's.

[draft/main.bp](draft/main.bp)
