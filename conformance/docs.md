# query — conformance reference

> Path: `repository/query/conformance/`
> Sibling (AGENTS): [`./AGENTS.md`](AGENTS.md) · Repository guide: [`../AGENTS.md`](../AGENTS.md)
> Examples: [`../examples.md`](../examples.md) · Decisions: [`../decisions-taken.md`](../decisions-taken.md)

`query` is botopink's answer to C#'s LINQ: a fluent query vocabulary over `array<T>`, and a
SQL-subset `` query @`…` `` template that expands at build into the same pipeline. It is a
repository of its own (`botopink/query`, decisions 312 and 724, erika's rename), depends on nothing of
rakun, names no persistence library (113), and is pure botopink: no decorator, no host binding, no
compiler surface. It is opt-in, reached by `from "query"` and never loaded into the prelude.

This reference describes the library as it is built and as the rules in force
([`../decisions-taken.md`](../decisions-taken.md)) fix it. Every example is a conformance cell
(decisions 457, 461, 462): a line `<!-- example: modules/<cell> -->` names `modules/<cell>/`, whose
`src/` is the program and whose `snapshot/` is its answer, checked by `botopink cell check conformance/`.
A table, a grammar or a manifest is not a program and is fenced as `text`.

| Section | What |
|---|---|
| *Loading* | the dependency, how `from "query"` resolves, the three ways a consumer reaches the library |
| *The `query` module* | `Query<T>` and its operators, `Grouping<K, V>`, the constructors |
| *The query string* | `` query @`…` ``: the grammar, the answer's shape, holes, aggregates, joins, the front-end, refusals |
| *The database target* | `QueryContext<E>`, `QueryTable`, `QueryColumn`, and what a persistence library supplies |
| *The `query-test` package* | the library's testing member |
| *Departures* | where the source still writes a form the rules in force have replaced |

## Loading

`query` is a botopink workspace whose core is the member `modules/query/`; `from "query"` resolves
to that member, never to the umbrella. A program declares it in `dependencies`, the object form with
one source per entry; a sibling member of the workspace declares `{ "workspace": true }`:

```text
{ "dependencies": { "query": { "git": "https://github.com/botopink/query.git", "branch": "feat" } } }
{ "dependencies": { "query": { "workspace": true } } }
```

The package is its tree: every `.bp` under `src/` is a module, its path its name (592 (1)), and
`src/internal/` is private (703). The loader finds a library at the nearest ancestor workspace,
`repository/botopink-lang/libs`, `repository/`, then `.botopinkbuild/deps/`; there is no embed and no
per-library registry, and the compiler core never names `query`. The cell imports `Query` and `of`
and counts the even numbers of a list:

<!-- example: modules/loading_imports -->

Three forms reach the library once it is imported: a bare function (`of(xs)`), the bare template
`` query @`…` ``, and the namespace member `query.of(…)`. The package handle `query` is the
module's `pub default fn`, so the same name is the template and the namespace:

<!-- example: modules/generic_loader_binding -->

## The `query` module

The module `query` (`modules/query/src/query.bp`) holds the whole library. Its root declares it the
package's public and default module, so `import {…} from "query"` reaches every item below and
the package handle `query` is bound by importing it.

### `Query<T>`

`Query<T>` wraps an `array<T>` (its field `items`). It is eager and immutable: every operator returns a
fresh `Query` over a freshly built array, and a terminal returns a scalar, a `?T` or an array. It is
built on the array interface alone, with no host backing. There is no arity-based overloading, so a
predicate variant has a name of its own (`count` / `countWhere`, `first` / `firstWhere`, `any` /
`anyWhere`); methods are camelCase; `cast` and `ofType` are absent for want of run-time type tags.

| Group | Members |
|---|---|
| Materialize and size | `toArray()`, `toList()` (both answer the items), `count()`, `isEmpty()` |
| Restriction and projection | `where(pred)`, `select(fn)`, `selectMany(fn -> array)` (flattens the per-item arrays into one `Query<U>`) |
| Partition and order | `take(n)`, `skip(n)`, `takeWhile(pred)`, `skipWhile(pred)`, `reverse()`, `orderBy(keyFn)`, `orderByDescending(keyFn)` (both stable, by a comparable key) |
| Set, group and join | `distinct()`, `distinctBy(keyFn)`, `concat(other)`, `union(other)` (concat then distinct), `intersect(other)`, `except(other)` (both distinct), `groupBy(keyFn)` answering `Query<Grouping<K, T>>` in first-seen key order, `zip(other, combine)` (pairs by index, stops at the shorter) |
| Aggregate (terminal) | `countWhere(pred)`, `sum(selector)` over `i32`, `average(selector)` over `f64` (`0.0` for no items), `min(keyFn)` and `max(keyFn)` answering `?K` (absent for no items), `aggregate(seed, step)` |
| Element (terminal) | `first()`, `firstWhere(pred)`, `last()`, `single()` (present only when there is exactly one item), `elementAt(i)`, each `?T` |
| Quantifier (terminal) | `any()`, `anyWhere(pred)`, `all(pred)`, `contains(x)` |

Nothing here fails: an element terminal answers `null` where there is no element, an aggregate over
no items answers its neutral value or `null`, and an index out of range is `null`. The library has no error enum
of its own; the failures belong to the query string (below), which are build errors.

### `Grouping<K, V>`

The bucket `groupBy` produces: `key: K` and `items: array<V>`, with `count()` (the number of members) and
`toArray()` (the members).

### Constructors

`of(xs)` wraps an array. `from` is the `import` keyword and cannot name a function, so the wrapper
constructor is `of`. `empty()` is the empty query, `range(start, stop)` the `i32` run from `start` up to
`stop` (exclusive), `repeat(value, times)` a query of `times` copies (none for a count at or below zero).

## The query string

`query` is also a template function: `` query @`…` `` hands its raw text to the template, which parses a SQL
subset at build and answers the code of the fluent pipeline, so the call's type is the type of the code
built for it (252). The text is raw (577 (5)); a `${…}` hole is the template's own syntax (577 (1)).
Keywords are lowercase. The grammar:

```text
select <* | item[, item…]>
from <Name>
[join <Name> on <a.f> = <b.g>]
[where <cond>]
[group by <field>]
[order by <field> [asc|desc]]
[limit <n | ${hole}>]

item := field | count(*) | sum(field) | avg(field) | min(field) | max(field)
```

The collection `<Name>` is resolved against the caller's top-level scope, and only a `val` there: the
template reads the scope's immutable bindings, so a `var` is queried through the fluent `of(xs)` form. The
expansion is unqualified fluent source (`of(Name).where(…).orderBy(…).select(…).toArray()`), which
resolves wherever `of` and the collection are in scope. A single-line and a triple-quoted multi-line query
parse identically: newlines and tabs are ordinary token boundaries.

- `select *` answers the whole rows; `select field` that column; `select a, b` a tuple `#(a, b)` per row,
  labeled by the column names, read with `val #(a, b) = row`.
- `where <cond>` compares a field with a literal, another field or a hole (`==`, `!=`, `<`, `<=`, `>`, `>=`;
  `=` reads as `==`, `<>` as `!=`; `and`, `or`, with `or` binding looser than `and`). String literals use
  single quotes; bare digits are numbers.
- `order by <field> [asc|desc]` is a stable sort by the projected key.
- `limit <n>` is written in the query (312). `limit 1` answers one row, an optional `?T`; any other count or a
  hole keeps an array and cuts it; no `limit` answers the array. The query decides its answer's shape, and the
  call unifies with the expected type like any expression: a declared `i32`, or a `?T` over a query without
  `limit 1`, is the ordinary type mismatch at the call (252, 426).
- The aggregates keep the strict reading (428): `count(*)`; `min(f)` and `max(f)` answer `?F`; `sum(f)` answers
  `i64` over an integer field and `f64` over an `f64`, `0` for no rows; `avg(f)` answers `?f64`. Without
  `group by` an aggregate answers one value (a tuple for several) and takes neither `order by` nor `limit`;
  a field beside an aggregate needs `group by` on it; `count(f)` on a `?T` field counts the non-null rows and
  is refused on a non-optional field.
- `group by <field>` answers one row per key in first-seen order of the ordered rows: the selected items are
  the key and aggregates, a tuple per group (a bare value for one item); `order by` names the group field.
- `join <Name> on <a.f> = <b.g>` is the inner join of two collections. After a join every field is
  qualified by its collection (`orders.city`), and a row is the pair the condition matches: `select *`
  answers `#(left, right)`, picked fields a tuple.
- A hole `${expr}` is a bound value, never text: the expression is evaluated once, before the pipeline runs, and
  may be any expression. Its type is the type of the field it meets (a mismatch is a type error at the hole);
  it stands only as an operand of a comparison or as the number of rows.

### The front-end

The template runs a real three-stage front-end over the text, with no string splitting:

1. a lexer scans it character by character into tokens, each carrying a span of byte offsets;
2. a parser buckets the tokens into a select statement; the `where` clause becomes `or`-groups of `and`-groups
   of comparisons, so the precedence is structural;
3. the one parse is lowered twice: into the executable fluent pipeline, and into a generic custom node tree for
   the language server, returned together through `compiler.custom(ast)` (576 (4)). The source node carries
   its `compiler.lookup` binding so hover and go-to-definition reach the collection's declaration.

Code built by an expansion is located by its expansion, and an error in it by the word it copies from the
template text (429, 691).

### Refusals

A malformed query is a located build error at the offending token, not at the whole template
(`compiler.fail`, 576 (2)). The refusals the repository keeps as projects are under
[`../refusals/`](../refusals/): an aggregate beside a field with no `group by`, an aggregate with
`order by`, `count` of a non-optional field, an unknown aggregate, `select *` with `group by`, a join whose
condition is malformed, joins both sides of one collection, an unknown or unqualified join field, a
`limit` that is not a number, has two values or has a hole of the wrong type, a condition with an extra
token, and a hole where the grammar takes none (a field, a table, a condition, inside a string literal), an
unbound hole and a hole of the wrong type.

## The database target

`query` names no persistence library (113). It declares what a database target needs, and a persistence
library implements and records it (`rakun-data`, decisions 397, 398 and 724); the cell shows the two records
built and read:

<!-- example: modules/query_context_declared -->

- `QueryColumn(field, column)` is one column of an entity: the field it maps from and the column it names.
- `QueryTable(name, columns)` is the typed meta a persistence library records on an entity: the table it maps to
  and its `QueryColumn`s, read at build.
- `QueryContext<E>` is the behavior of where a statement runs, `E` being the implementor's own error so that
  `query` names no library's. It answers `runMany<T>(self, sql, params, decode) -> Result<T[], E>` and
  `runOne<T>(…) -> Result<?T, E>` (397 (1), 427 (1)). Each bound parameter is a typed `QueryParam`
  (`Int(i64)`, `Float(f64)`, `Text(string)`, `Bool(bool)`, `Bytes(bytes)`, `Null`), built by the template
  from the type of the column its hole meets, and a hole of another type is an error at the hole (427).

A database query is written on the context in a method body, `` self.db.query @`select * from User where active = true` ``,
and `from User` names an entity: a type imported in the module and resolved at the call site
(`compiler.lookup`), its table and columns its `QueryTable` meta. The body is assembled wholly at build, the SQL
a constant of the program with only the holes computed; a type with no `QueryTable` is an error at the query,
a field it lacks an error at the field; the call answers `Result<?User, E>` under `limit 1` and
`Result<User[], E>` otherwise (397, 415, 426). `#[repository]`, `#[entity]`, `#[query @`…`]` and
`#[nativeQuery(…)]` belong to `rakun-data`, which depends on `query`.

## The `query-test` package

`modules/query-test/` is the library's `<lib>-test` member (front 95): the home of `assert<Subject>` helpers,
fixtures and builders. Its public surface is empty today; its one inline `test` proves that the core resolves
from a member.

## Departures

Where the source has not yet caught up with the rules in force, this reference follows the rules:

- `Array<T>` is written where the rules spell `array<T>` / `T[]` (641, 617 (1)), and the module's comments name
  `@Expr`, `q.build`, `q.custom` and `q.failAt` for `syntax`, `compiler.ast`, `compiler.custom` and `compiler.fail`
  (576, 578; `retired.md`); the codemod is pending.
- `modules/query/botopink.json` still declares `entry` and `files`, and `root.bp` still declares `pub default mod query;`
  (733, 703, 592 (1)).
- `QueryContext<E>` declares a single `run<T>(self, sql, params: Array<string>) -> Result<Array<T>, E>`, and `QueryParam`
  is not declared; `runMany`, `runOne` and the typed parameters of 427 are the rules in force.
- The database target's `from User`, `self.db.query` and the template method wait on the build of 397, 415 and 427;
  the library today ships the in-memory expansion and the declared `QueryContext`, `QueryTable` and `QueryColumn`.
- The expansion's aggregates follow the fluent layer (`sum` over `i32`, `average` over `f64`), not yet 428's strict reading.
