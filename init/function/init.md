# `Init()` As-Is

## Overview

`asset/init/function/Init.php` initializes OP-managed repositories from the entries under `asset/config/submodule/**`.

It is called from `asset/init/update.php` for each submodule configuration file.

## Clone URL Source

`Init()` reads the clone URL from:

- `$config['url']`

If the target directory already exists, `Init()` returns `false` and does not run the clone or init path.

The current submodule config files normally use HTTPS GitHub URLs such as:

```text
https://github.com/onepiece-framework/op-core-8.git
```

## GitHub Owner Override

Before cloning, `Init()` may replace the GitHub owner name when:

```php
Request('github')
```

is set and the URL targets `onepiece-framework`.

For example:

```text
https://github.com/onepiece-framework/op-core-8.git
```

can become:

```text
https://github.com/example-account/op-core-8.git
```

The original `onepiece-framework` remote is retained as `onepie` after clone.

## Hooks And Remotes

After a successful clone, `Init()` changes into the cloned repository and sets Git hooks through `GitHooks()`.

It also initializes nested Git submodules and applies repository helper behavior:

- `GitSubmoduleGithub()` can initialize nested Git submodules from `.gitmodules`.
- `GitSubmoduleRepository()` can add optional local or SSH remotes.
- A local bare repository can be created through `GitInitLocal()` when `local=1` is enabled.

## Nested Submodule Hooks

After nested submodules are initialized, `Init()` reads their paths with `git submodule foreach pwd`.

For each nested submodule path, it changes into that submodule directory and calls `GitHooks()`. This initializes `core.hooksPath` for nested Git-managed repositories such as `asset/core/class` and `asset/core/interface` during the initial clone path.

This hook setup is part of initialization only. If the target package directory already exists, `Init()` returns before this path and does not re-apply hooks. Existing repositories are handled by `Update()`, which intentionally performs fetch/rebase work and does not perform initialization such as hook setup.

## SSH Clone Scheme

When:

```php
Request('scheme') === 'ssh'
```

`Init()` changes HTTPS GitHub clone URLs to SSH GitHub clone URLs before running `git clone`.

Current conversion:

```text
https://github.com/owner/repository.git
```

becomes:

```text
git@github.com:owner/repository.git
```

This conversion happens after the optional `github` owner replacement and before shell escaping.

That means the two options can be combined:

```text
php asset/init/update.php github=example-account scheme=ssh
```

In that case, a config URL like:

```text
https://github.com/onepiece-framework/op-core-8.git
```

is cloned as:

```text
git@github.com:example-account/op-core-8.git
```

## Scope

The `scheme=ssh` behavior only changes clone URLs that match:

```text
https://github.com/owner/repository.git
```

Other URL formats are left unchanged.

The request value is loaded through `asset/init/function/Request.php`, so it can come from:

- `asset/config/init.php`
- `asset/config/_init.php`
- a CLI argument such as `scheme=ssh`
