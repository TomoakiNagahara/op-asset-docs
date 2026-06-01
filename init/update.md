# `update.php` As-Is

## Overview

`asset/init/update.php` initializes and updates configured submodule repositories.

It can run in two modes:

- included from `asset/init/submodules.php`
- executed directly as `php asset/init/update.php`

When included from `submodules.php`, `_ROOT_GIT_` and `_OP_APP_BRANCH_` are already expected to be available.

When executed directly, `update.php` defines `_ROOT_GIT_`, refreshes existing Git-managed submodules with `git submodule foreach`, and loads `asset/config/op.php` if `_OP_APP_BRANCH_` is not yet defined.

## Execution Flow

1. Ensure `_ROOT_GIT_` exists.
2. If running directly, refresh existing Git-managed submodules.
3. Ensure `_OP_APP_BRANCH_` exists by including `asset/config/op.php` when needed.
4. Load init helper functions.
5. Read `indicator` through `Request('indicator', '1')`.
6. Iterate over `asset/config/submodule/*/*.php`.
7. For each config file, derive `$type` and `$name` from the file path.
8. Include the config file.
9. Skip entries with a truthy `skip` value.
10. Call `Init($type, $name, $config)`.
11. Call `Update($type, $name, $config, $init)`.
12. Print progress indicators when enabled.

## Direct Execution Branch

When `_ROOT_GIT_` is not defined, `update.php` assumes it was executed directly.

In that branch, it:

- defines `_ROOT_GIT_` from `git rev-parse --show-toplevel`
- runs `git submodule foreach git fetch --all`
- runs `git submodule foreach git pull`

This refreshes existing Git-managed submodules before configured repositories are processed.

## Config Loop

The config loop scans:

```text
asset/config/submodule/*/*.php
```

The parent directory name becomes `$type`, and the file basename without `.php` becomes `$name`.

Each config file returns the repository settings consumed by `Init()` and `Update()`.

## Called Functions

`Request()` reads initialization options from config and CLI arguments.

See `function/request.md`.

`Init()` creates or clones a missing configured repository, sets hooks and remotes after clone, and returns whether it performed initialization.

See `function/init.md`.

`Update()` updates an existing configured repository after `Init()` has run.

See `function/update.md`.

`Dir()` is defined inside `update.php`. It maps config type to target directory:

- `public_html` maps to `_ROOT_GIT_`
- `asset` maps to `_ROOT_GIT_/asset/`
- all other types map to `_ROOT_GIT_/asset/<type>`

## Scope

`update.php` does not fetch or pull the skeleton repository itself.

For the current update gap, see `overview.md`.
