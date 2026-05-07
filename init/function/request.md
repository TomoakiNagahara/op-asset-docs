# `Request()` As-Is

## Overview

`asset/init/function/Request.php` provides the current request-value loader used by the skeleton initialization scripts.

It is used by files such as:

- `asset/init/update.php`
- `asset/init/function/Init.php`
- `asset/init/function/GitSubmoduleRepository.php`

## Current Load Order

The current implementation builds request values in this order:

1. `asset/config/init.php`
2. `asset/config/_init.php`
3. CLI arguments such as `key=value`

Later layers overwrite earlier layers.

## Local Override Behavior

If `asset/config/_init.php` exists, it is merged into the base result from `asset/config/init.php` using:

- `array_replace_recursive()`

This means `_init.php` works as a local override layer for init-time configuration.

## CLI Override Behavior

After config loading, CLI arguments are parsed from `$_SERVER['argv']`.

If a CLI argument is in the form:

```text
key=value
```

and it matches the current validation rules, it overwrites the config-derived value.

So the current As-Is precedence is:

- base config
- local override config
- CLI override

## Operational Meaning

This allows the initialization layer to keep:

- a shared default in `init.php`
- a local machine override in `_init.php`
- an immediate one-shot override on the command line

without changing the source code of the init scripts themselves.
