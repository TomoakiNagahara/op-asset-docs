# docs

Source folder: `asset/core/docs/`

For overall OP-CORE context, see `overview.md`.

`docs` contains package-owned documentation for op-core itself.

This folder is the documentation submodule that travels with `asset/core/`. It describes core concepts and behavior from the core package side, such as OP overview, request handling, URL handling, MetaPath, the Unit system, and the `D()` function.

Use this folder when changing documentation that belongs to the op-core package. Use `asset/docs/core/` in the skeleton when the document is an agent-facing or skeleton-facing reference about core.

Keep package-owned core docs separate from skeleton docs so submodule updates and skeleton documentation commits remain clear.
