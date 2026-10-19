# Decision 696 — A template function is an annotation only when its signature declares the annotated declaration as its second parameter (578 (1): `syntax q: string`, then `syntax decl: unknown`); called as `` f @`…` `` it is `template-annotation-only`, and a template without that parameter at `#[f @`…`]` is `template-annotation-without-decl`.

`query` takes one `syntax` parameter, the template text, so it is a template that is called: `largeOrders` writes `` query @`…` `` in a body and `main` writes another as an argument. The program prints `Lyon,Nice`, then `3`. A template is an annotation only when its signature also declares the annotated declaration as its second `syntax` parameter. `query`'s does not, so writing `` #[query @`…`] `` with `query`'s template on a declaration is refused with `template-annotation-without-decl`. The repository annotation of that spelling is a separate function, `rakun-data`'s (313, 398). The opposite case is refused too: a function that takes the declaration and is called as `` f @`…` `` is `template-annotation-only`.

Cells in this repository that prove it: `modules/generic_loader_binding` (`query` called as a template). The refusals' cells are botopink-lang's.

[draft/main.bp](draft/main.bp)
