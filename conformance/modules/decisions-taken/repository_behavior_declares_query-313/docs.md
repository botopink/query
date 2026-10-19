# Decision 313 — A repository is a `behavior` annotated `#[repository]`; each method declares its query — erika's or native SQL

`Users` is a `behavior` annotated `#[repository]`, and each of its methods declares its query. `find` and `active` carry `` #[query @`…`] `` in `query`'s grammar, which is checked against the entity `User` at build and recorded with its SQL and parameters as typed meta of the method. `find`'s `limit 1` matches its `Result<?User, StoreError>` answer. `named` carries `#[nativeQuery("…")]`: the driver's SQL as a literal, whose `:name` placeholder is matched against the method's parameter `name`. From that meta, `#[repository]` generates `Users.Sql(ctx)`, the factory `Users.of(ctx)` and the registration by type. A caller depends only on the behavior. `greet` takes any `Users`, and the example hands it `Listed`, an implementation over a list built with `query`'s fluent layer. The program prints `hello Ann`, `hello stranger` and `Bob,Ann`.

A method with neither annotation is an error at the method. A placeholder no parameter answers, or a parameter no placeholder uses, is an error at the annotation. Every method answers `Result<T, StoreError>`.

Cells in this repository that prove it: none yet. The annotations are `rakun-data`'s (398, and 724's merge of `dbcontext` into it), which depends on `query`; `query`'s part is the grammar the annotation's text is written in, built in front 145.

[draft/main.bp](draft/main.bp)
