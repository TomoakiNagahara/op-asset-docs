# `submodules.php` As-Is

## Overview

`asset/init/submodules.php` is the normal first initializer after cloning the OP Framework skeleton.

It prepares the main skeleton repository, initializes Git-managed submodules reachable from the current repository, hands configured repository setup to `asset/init/update.php`, and finally applies the framework Git hook path.

For the broader initialization model, see `overview.md`.

## Execution Flow

1. Load helper functions.
2. Define `_ROOT_GIT_` from `git rev-parse --show-toplevel`.
3. Change the current directory to `_ROOT_GIT_`.
4. Include `asset/config/op.php` so branch and application constants are available.
5. Run Git-managed submodule preparation helpers.
6. Include `asset/init/update.php`.
7. Run `GitHooks()` after `update.php` returns.

The earlier `GitHooks()` call before submodule preparation is currently commented out. In the current As-Is, hook configuration is applied after `update.php` returns, and helper paths may also set hooks during recursive submodule handling.

## Hook Ordering Reason

The hook package itself is configured as a submodule at `asset/init/hooks/`.

When `GitHooks()` ran before that hooks submodule had been cloned, `core.hooksPath` pointed at a directory that did not exist yet. That caused warnings about the missing hooks directory.

For that reason, the top-level `GitHooks()` call was moved to after `asset/init/update.php`, so the hooks package can be cloned before the main hook path is applied.

## Remaining Hook Ordering Gap

[DOC-GAP] `GitSubmoduleForeach()` still sets `core.hooksPath` during recursive Git submodule handling.

If `GitSubmoduleForeach()` runs before `asset/init/hooks/` exists, it can still configure repositories to point at a missing hooks directory. The current top-level order reduces the original warning path, but hook setup is not yet fully centralized after the hooks package is guaranteed to exist.

The OP-managed `Init()` path now sets hooks for nested Git submodules after those submodules are initialized. That addresses initial clone cases such as `asset/core/class` and `asset/core/interface`. This remaining gap is specifically about the separate `GitSubmoduleForeach()` helper path.

Future cleanup should choose one clear rule:

- only set `core.hooksPath` when `asset/init/hooks/` exists
- or remove hook setup from recursive helper paths and centralize it after `update.php`

## Helper Calls

`GitSubmoduleGithub()` handles `.gitmodules` owner replacement when requested and runs the basic Git submodule sync/init/update commands.

See `function/git-submodule-github.md`.

`GitSubmoduleRepository()` adds optional local or SSH remotes when the corresponding request values are enabled.

See `function/git-submodule-repository.md`.

`GitSubmoduleForeach(_ROOT_GIT_)` walks active Git submodules, checks out the target branch, applies hooks, and recurses into nested Git submodules.

See `function/git-submodule-foreach.md`.

`asset/init/update.php` handles configured submodule repository initialization and update, including the `Init()` and `Update()` function flow.

See `overview.md`, `function/init.md`, and `function/update.md`.

`GitHooks()` sets `core.hooksPath` to `asset/init/hooks/` for the current repository and its Git submodules.

See `function/git-hooks.md`.
