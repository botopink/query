# Decision 344 — A git dependency may name a package inside its repository: `"subdir"`.

The example is an ordinary consumer of `query`: it keeps the adults of a list and prints `Ann, Cy`. What the decision fixes is in the manifest that brings the library in. `query` is a workspace, and the package a program depends on is its member `modules/query`. A program outside the workspace therefore declares it with `"subdir"`: `{"git": "https://github.com/botopink/query", "ref": "<tag>", "subdir": "modules/query"}`. The checkout is cloned once at `ref` and the package is read at `subdir`. Its own `path` dependencies resolve inside the same checkout, so `query-test` and `query` come from one `ref`.

The manifest is refused, located, in these cases: `subdir` without `git`; an absolute `subdir` or one that leaves the checkout (`..`); a `subdir` with no `botopink.json`, or one whose manifest is a workspace (the repository root `botopink.json` of `query` is one); and one repository at two `ref`s.

Cells in this repository that prove it: none yet. The workspace's own cells reach the member by `path`; a `subdir` dependency waits on front 98's packaging step.

[draft/main.bp](draft/main.bp)
