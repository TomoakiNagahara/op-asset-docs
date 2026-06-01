# `Update()` As-Is

## Overview

`asset/init/function/Update.php` updates configured repositories after `Init()` has run.

It is loaded from `asset/init/update.php`, which calls:

```php
Update($type, $name, $config, $init)
```

for each enabled submodule configuration.

## Existing Repository Path

`Update()` resolves the repository directory from:

- `Dir($type)`
- `$config['path'] ?? $name`

If the directory does not exist, `Update()` returns without doing update work.

## Init Result

The `$init` argument is the result returned by `Init()`.

When `$init` is truthy, `Update()` does not run the fetch/pull path. This avoids immediately updating a repository that was just cloned by `Init()`.

When `$init` is false, `Update()` treats the repository as existing and runs the update path.

## Fetch And Rebase

For existing repositories, `Update()` fetches:

```text
git fetch <target>
```

The target comes from:

```php
Request('remote', '--all')
```

If `pull` is enabled, `Update()` then runs:

```text
git pull --rebase <remote> <branch>
```

The remote and branch come from the submodule config, defaulting to `origin` and `_OP_APP_BRANCH_`.

## Nested Git Submodules

If the repository has a `.gitmodules` file, `Update()` reads it through `GitSubmoduleConfig()` and updates the nested submodules it describes.

For each nested submodule path, it fetches the requested target and then pulls the configured remote and branch.
