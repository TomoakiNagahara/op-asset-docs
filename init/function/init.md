# `Init()` As-Is

## Overview

`asset/init/function/Init.php` initializes OP-managed repositories from the entries under `asset/config/submodule/**`.

It is called from `asset/init/update.php` for each submodule configuration file.

## Clone URL Source

`Init()` reads the clone URL from:

- `$config['url']`

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

