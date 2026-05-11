# Initialization Overview

## Entry Point

The normal ONEPIECE Framework skeleton initialization entry point is:

```text
php asset/init/submodules.php
```

The file name is plural: `submodules.php`.

## Two Kinds Of Submodules

The initialization workflow handles two different kinds of repository-managed parts.

1. Git-managed Git submodules
2. Non-Git-managed submodules described by `asset/config/submodule/**`

Historically, the ONEPIECE Framework skeleton used only Git-managed Git submodules.
Later, the skeleton also gained support for submodules that are not managed by Git's `.gitmodules` mechanism.

`asset/init/update.php` exists for that second category.

## Flow

`asset/init/submodules.php` first initializes the Git-managed Git submodules.

It performs work such as:

- setting Git hooks
- applying GitHub owner replacement to `.gitmodules`
- adding optional remotes
- running `GitSubmoduleForeach()` for recursive Git submodule update, branch checkout, hook setup, and nested submodule handling

After that, `asset/init/submodules.php` includes:

```php
asset/init/update.php
```

`asset/init/update.php` then handles the non-Git-managed submodule configuration files under:

```text
asset/config/submodule/*/*.php
```

If `update.php` is executed directly, it defines the Git root itself and runs `git submodule foreach git fetch --all` followed by `git submodule foreach git pull` before processing the non-Git-managed submodule configs.

For each enabled config file, `update.php` calls:

```php
Init($type, $name, $config)
Update($type, $name, $config, $init)
```

Config files with a truthy `skip` value are ignored.

## Responsibility Split

`submodules.php` is the top-level initializer.

It is responsible for the full initialization sequence and calls `update.php` internally.

`update.php` is the initializer/updater for non-Git-managed submodules.

`Init()` creates or clones a configured repository when it is missing. It uses the configured `url`, `path`, and `branch`, may rewrite the GitHub owner or clone URL scheme through request values, sets hooks, and initializes nested Git submodules inside the cloned repository.

`Update()` updates repositories after the init step. When `Init()` just cloned the repository, `Update()` intentionally does not run the fetch/pull path. For existing repositories, it fetches `Request('remote', '--all')`, pulls from the configured remote and branch unless `pull=0`, and also updates nested Git submodules when `.gitmodules` exists.

`Dir()` maps config types to target directories. `public_html` maps to the Git root, `asset` maps to `asset/`, and other types map to `asset/<type>`.

## Operational Meaning

For normal initialization, users should run:

```text
php asset/init/submodules.php
```

Running `update.php` directly is useful when the Git-managed submodules are already present and the user mainly needs to initialize or update the non-Git-managed submodule layer. In that mode, `update.php` still refreshes existing Git-managed submodules before it processes the non-Git-managed configs.
