# Meta Path

## Overview

One of the major features of the ONEPIECE Framework is its **meta path** system.

Instead of writing every path as a raw local filesystem path, the framework can use named roots.

Typical examples are:

- `doc:/foo/bar/index.php`
- `app:/hoge/`
- `op:/asset/`

These labels let the framework describe paths in terms of role and meaning rather than only by absolute location.

The label set is not limited to built-in examples.

Developers and end users may also define their own meta path labels when needed.

## Main Meta Path Examples

### `doc:/`

`doc:/` means a path from the document root.

Example:

```text
doc:/foo/bar/index.php
```

### `app:/`

`app:/` means a path from the directory where the current application is deployed and executed.

Example:

```text
app:/hoge/
```

This is especially convenient because the application does not need to be installed at a fixed location directly under the document root.

As long as the framework knows the application root, the same meta path expression can still work even when the application is deployed in different places under the document root.

That makes application relocation and deployment structure much easier to handle.

### `op:/`

`op:/` means a path from the top directory of the ONEPIECE Framework.

Example:

```text
op:/asset/
```

This is useful when the framework needs to refer to framework-level resources, tooling, or shared assets from a stable base path.

### `git:/`

`git:/` is still available in the 2030 generation during the migration period.

It remains supported for compatibility.

## Historical Background

The current convenience method `OP()->Path()` was introduced in the 2030 generation of the framework.

It was introduced for three practical reasons:

- the older path handling was too verbose
- the framework wanted a unified interface
- path access should always be reachable consistently from `OP()`

However, the underlying meta path concept is older.

The following functions already existed in the 2020 generation:

- `RootPath()`
  registers meta path roots
- `ConvertPath()`
  converts a meta path into a full path
- `CompressPath()`
  converts a full path back into a meta path

In other words, the meta path system itself is not new in 2030.

What 2030 adds is the unified convenience entry point `OP()->Path()`.

At the application usage level, this is the public face that users are expected to rely on.

In that sense, `OP()->Path()` can be understood as a wrapper over the older meta path mechanics.

The lower-level functions still matter, and AI should understand them, but human users normally do not need to know them directly because they are internal mechanics behind the unified entry point.

## Historical Meaning of `op:/`

In the first generation of the ONEPIECE Framework, `OP:/` referred to the `op-core` directory itself.

From the 2020 generation onward, `op-core` became `core:/`.

As time passed, code that still depended on the old `OP:/` meaning disappeared.

Because of that, `op:/` is now intended to mean the top directory of the framework as a whole.

The newly added `_ROOT_OP_` constant exists as the naming replacement for the older `_ROOT_GIT_`.

In the 2030 generation, both `git:/` and `op:/` are allowed during the transition period.

This is because ONEPIECE Framework places very high importance on backward compatibility.

There is still a large amount of existing code that uses `git:/`.

The direction is to replace it with `op:/` over time, but from now on the preferred name is `op:/`.

## Why Meta Paths Matter

The meta path system helps the framework stay:

- simple
- readable
- portable
- less dependent on hard-coded local filesystem locations

It also matches the broader ONEPIECE Framework preference for explicit, understandable behavior.

A path like `app:/config/database.php` immediately tells the reader what root concept is being used.

It also gives projects a controlled way to introduce their own path vocabulary without abandoning the unified meta path model.

## Summary

Meta paths are a core convenience feature of the ONEPIECE Framework.

They allow code to refer to:

- the document root
- the application root
- the framework root

without forcing every caller to depend on raw absolute paths.
