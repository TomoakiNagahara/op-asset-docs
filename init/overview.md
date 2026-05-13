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

## Optional Local And SSH Remotes

`GitSubmoduleRepository()` can add optional remotes when the request enables `local=1` or `ssh=1`.

For `local=1`, the `dir` value is a local filesystem path. Git does not expand `~` for that local remote path, so `GitSubmoduleRepository()` expands a leading `~` to the local user's home directory before passing the path to `GitInitLocal()`.

For `ssh=1`, the `dir` value is part of an SSH remote path such as `host:~/repo/...`. The local PHP process cannot know the remote host's home directory. The leading `~` must therefore be preserved, and Git/SSH resolves it on the remote host.

## Current Update Gap

[DOC-GAP] The current `update.php` implementation is useful for daily updates after initialization, but it does not update the skeleton repository itself.

When `asset/init/update.php` is executed directly, it updates:

- existing Git-managed Git submodules through `git submodule foreach`
- repositories described by `asset/config/submodule/*/*.php`
- nested Git submodules inside those repositories when `.gitmodules` exists

It does not run `git fetch` or `git pull` in the skeleton Git root itself.

This gap exists because the skeleton originally used only Git-managed Git submodules. The non-Git-managed submodule layer was added later, and the update responsibility for that later layer was placed in `asset/init/update.php`. As a result, `update.php` became the daily update command for framework packages, but not yet for the main skeleton repository.

[DOC-FUTURE] The desired direction is that `asset/init/update.php` should be enough for daily updates of the whole working tree, including the skeleton repository itself. In that model, users would not need to remember a separate skeleton-level `git pull` before or after running `update.php`.

## Operational Meaning

For normal initialization, users should run:

```text
php asset/init/submodules.php
```

Running `update.php` directly is useful for daily package updates after initialization. In the current implementation, it refreshes existing Git-managed submodules before it processes the non-Git-managed configs, but the skeleton repository itself must still be updated separately.
