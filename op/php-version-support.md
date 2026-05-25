# PHP Version Support

## Purpose

This document records the PHP version support policy for the ONEPIECE Framework 2030 skeleton.

It is intended as the normal documentation reference for AI and contributors.

## Current Support

The ONEPIECE Framework 2030 line supports PHP 8.0 or higher.

In practical terms:

- PHP 8.0 and later are within the supported runtime line
- PHP 7.x is not supported by the 2030 runtime
- PHP 5.x and older are not supported

## Runtime Enforcement

The runtime requirement is enforced by `asset/core/Bootstrap.php`.

For the bootstrap responsibility boundary, see:

- `asset/docs/core/bootstrap.md`

The current bootstrap rule rejects PHP versions older than 8.0.0:

```php
if( version_compare(PHP_VERSION, '8.0.0') < 0 ){
	exit('...');
}
```

This bootstrap check is the most important current implementation signal.

If this check changes, this document should be updated at the same time.

## README Signal

The repository README currently presents the framework as a PHP 8.x project.

That matches the runtime rule:

```text
PHP 8.0 or higher
```

## CI Matrix Note

CI configuration may contain historical, experimental, or transition entries.

Do not treat CI matrix entries as the primary public support statement when they conflict with the runtime bootstrap requirement.

For normal documentation and agent decisions, use this document and the bootstrap check as the support baseline.

## Guidance For AI And Contributors

When adding new code to the 2030 skeleton, assume PHP 8.0 or higher.

Do not add compatibility workarounds for PHP 7.x unless the task explicitly asks for historical analysis or migration documentation.

Do not describe the 2030 runtime as supporting PHP 7.x.

When documenting compatibility, use:

```text
PHP 8.0 or higher
```

or:

```text
PHP 8.x
```

Use `PHP 8.0 or higher` when the minimum version matters.

Use `PHP 8.x` only as a short marketing-style summary.
