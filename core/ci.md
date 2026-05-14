# ci

Source folder: `asset/core/ci/`

For overall OP-CORE context, see `overview.md`.

`ci` defines the CI-facing entry points for core classes and traits.

This folder keeps executable CI check targets for core behavior such as `OP`, `Config`, `Cookie`, `Env`, `Error`, `Session`, `Unit`, and `Unit_Mapper`.

Use this folder when a core class or trait needs a matching file-level CI check. Do not place normal runtime implementation here; runtime class definitions belong in `asset/core/class/`, shared trait implementation belongs in `asset/core/trait/`, and standalone helper functions belong in `asset/core/function/`.

When a core feature file changes, the matching `ci` file is one of the expected companion files for the same commit unit.
