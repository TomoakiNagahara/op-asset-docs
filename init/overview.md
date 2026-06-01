# Initialization Overview

## Entry Point

The normal ONEPIECE Framework skeleton initialization entry point is:

```text
php asset/init/submodules.php
```

The file name is plural: `submodules.php`.

## Repository And Submodule Model

Most files in the ONEPIECE Framework working tree are package repositories installed as submodules in the broad OP sense.

In the skeleton repository itself, almost no application/runtime package files are tracked directly. The main repository primarily keeps the files needed to bootstrap initialization, especially `asset/init/`, plus the configuration that tells initialization which packages to install.

Current skeleton setup has no Git-managed submodules in the skeleton root `.gitmodules`.

End users may still attach their own Git-managed submodules to the skeleton side. Initialization code therefore still contains paths for Git-managed submodule handling.

Framework package areas such as CORE, UNIT, MODULE, LAYOUT, bootstrap, and template are submodule packages installed by initialization. In the skeleton root, these are configured through `asset/config/submodule/**` rather than through root `.gitmodules`.

Some installed package repositories may themselves contain Git-managed nested submodules. `asset/core/` is an intentional example: the core package uses Git-managed nested submodules for parts such as class, interface, function, trait, include, ci, testcase, tutorial, and docs.

`asset/init/update.php` is the main initializer/updater for the configured package repositories under `asset/config/submodule/**`.

## Flow

`asset/init/submodules.php` is the first initializer for the main repository, the OP Framework skeleton.

It prepares the skeleton repository, handles any Git-managed submodule pass that may exist on the skeleton side, includes `asset/init/update.php`, and applies hooks after `update.php` returns.

For the current file-level As-Is flow, see `submodules.md`.

`asset/init/update.php` then handles configured package repository cloning and update work, including OP-managed packages and any nested Git-managed submodules inside those packages.

For the current file-level As-Is flow, see `update.md`.

For configured package repositories, `update.php` reads the configuration files under:

```text
asset/config/submodule/*/*.php
```

If `update.php` is executed directly, it defines the Git root itself and runs `git submodule foreach git fetch --all` followed by `git submodule foreach git pull` before processing the configured package repositories. In the default skeleton root this Git-managed pass is usually empty, but it can matter if an end user added root Git submodules.

For each enabled config file, `update.php` calls:

```php
Init($type, $name, $config)
Update($type, $name, $config, $init)
```

Config files with a truthy `skip` value are ignored.

## Responsibility Split

`submodules.php` is the top-level initializer for the main skeleton repository.

It starts the skeleton initialization sequence and calls `update.php` internally so submodule initialization can continue there.

For `submodules.php` call order and branch behavior, see `submodules.md`.

`update.php` is the initializer/updater for configured package repositories described by `asset/config/submodule/**`, and it also updates nested Git-managed submodules inside those repositories when present.

`update.php` loads `asset/init/function/Init.php` and `asset/init/function/Update.php`, then calls `Init()` and `Update()` for each enabled configuration.

For `update.php` call order and branch behavior, see `update.md`.

`Init()` creates or clones a configured repository when it is missing. It uses the configured `url`, `path`, and `branch`, may rewrite the GitHub owner or clone URL scheme through request values, may add remotes, may retain the original remote under another name, may create a local remote, sets hooks, and initializes nested Git submodules inside the cloned repository. If the target directory already exists, `Init()` does not run the init path.

`Update()` updates repositories after the init step. When `Init()` just cloned the repository, `Update()` intentionally does not run the fetch/pull path. For existing repositories, it fetches `Request('remote', '--all')`, rebases through `git pull --rebase` unless `pull=0`, and also updates nested Git submodules when `.gitmodules` exists.

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

This gap exists because package update responsibility is concentrated in `asset/init/update.php`, while skeleton repository update remains outside that command. As a result, `update.php` became the daily update command for framework packages, but not yet for the main skeleton repository.

[DOC-FUTURE] The desired direction is that `asset/init/update.php` should be enough for daily updates of the whole working tree, including the skeleton repository itself. In that model, users would not need to remember a separate skeleton-level `git pull` before or after running `update.php`.

## Operational Meaning

For normal initialization, users should run:

```text
php asset/init/submodules.php
```

Running `update.php` directly is useful for daily package updates after initialization. In the current implementation, it refreshes existing Git-managed submodules reachable from the current skeleton root before it processes configured package repositories, but the skeleton repository itself must still be updated separately.
