# Core Bootstrap

## Purpose

This document records the high-level responsibility of `asset/core/Bootstrap.php`.

It exists so AI and contributors can find the related framework-level documents before changing bootstrap behavior.

## Scope

`asset/core/Bootstrap.php` is the OP-CORE startup gate.

It runs before the normal framework runtime is fully available.

Its current responsibilities include:

- checking the minimum supported PHP runtime version
- loading core definitions
- registering the autoloader
- loading the framework error handler
- loading core traits, classes, and global functions
- setting baseline request values needed after core startup

## PHP Version Gate

`asset/core/Bootstrap.php` currently enforces the minimum PHP runtime version.

The current framework support baseline is documented in:

- `asset/docs/op/php-version-support.md`

If the PHP version check in `asset/core/Bootstrap.php` changes, update `asset/docs/op/php-version-support.md` in the same change.

This is required because `php-version-support.md` is the normal public documentation reference for supported PHP versions.

## Editing Guidance

Treat `asset/core/Bootstrap.php` as a high-impact startup file.

Do not change it for normal application behavior, templates, layout, routing, or feature configuration.

When changing bootstrap behavior:

- check `app.php`
- check the bootstrap include order
- check pre-core assumptions
- check whether the change affects `asset/docs/op/php-version-support.md`
- update related documentation in the same commit when a contract changes

## Related Documents

- `asset/docs/op/php-version-support.md`
- `asset/docs/op/invariants.md`
- `asset/docs/skeleton/entry-point.md`
- `asset/docs/skeleton/runtime-lifecycle.md`
- `asset/docs/CUSTOMIZATION_MAP.md`
