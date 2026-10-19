# Decision 311 — A method with no body in a `type` is only a host binding; a declared-only method is a `behavior`'s; a template can be an annotation.

The example has both kinds of method without a body. `Source` is a behavior, and its `readings` is declared with no body, so each implementor writes it: `Fixed` gives the body that turns its numbers into `Reading`s. `Host` is a type, and its `sensorCount` has no body because it is a host binding. It carries its cells (`.JavaScript(…)`, `.Erlang(…)`) under `#[compiler.external(…)]` and has no other meaning. `report` takes any `Source` and runs `query`'s pipeline over what it reads. The program prints `fixed=2,fixed=3`, then `3`.

`query` itself relies on the same split. `QueryContext<E>` is a behavior whose runners have no body, and a persistence library implements them (397). `query` is a template, and on a context it is a template method, `` self.db.query @`…` ``, whose first parameter is the `syntax` receiver (397, 685). The annotation form `` #[f @`…`] `` belongs to a function that also takes the annotated declaration (696). `query`'s template does not, so writing it as an annotation is refused.

Cells in this repository that prove it: none yet. `modules/query_context_declared` builds the records that go with the behavior but does not implement it; the cells come with front 145's database-target steps.

[draft/main.bp](draft/main.bp)
