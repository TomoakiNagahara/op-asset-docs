# OP()->URL()

## Overview

`OP()->URL()` is the ONEPIECE Framework entry point for converting a meta path or a local full path into a document-root-based URL path.

It is the URL-side counterpart of the path handling system.

If `OP()->Path()` is the unified entry point for path conversion, `OP()->URL()` is the unified entry point for URL conversion.

## Main Role

`OP()->URL()` exists so callers do not need to manually calculate:

- where the application is deployed under the document root
- how a meta path should appear as a web URL
- whether a trailing slash should be added for directories

This is especially useful with `app:/`.

Even if the application is deployed in different places under the document root, `OP()->URL('app:/...')` can still produce the correct URL path.

## Typical Usage

Examples:

- `OP()->URL('app:/foo/bar/')`
- `OP()->URL('/actual/full/path/to/app/foo/bar/')`
- `OP()->URL('.')`

In current behavior:

- `app:/...` is converted into a document-root-relative URL
- a full path under the application root is also converted into a document-root-relative URL
- `.` returns the current full request URL

## Important Limitation

`OP()->URL()` is not a general-purpose converter for every meta path.

It is intended for paths that can be expressed as web-visible URLs under the document root.

That is why paths such as `asset:/...` are not treated as ordinary public URL targets in the current implementation.

## Relationship to Meta Path

`OP()->URL()` is closely related to the meta path system, but it has a different goal.

- `OP()->Path()`
  path conversion
- `OP()->URL()`
  URL conversion

Together, they let the framework treat local paths and web URLs through a unified conceptual model.
