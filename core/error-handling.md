# Error Handling

ONEPIECE Framework is designed to collect every error that can be collected after the framework becomes active.

More precisely, it collects all errors that are reachable by the framework, excluding errors that are inherently impossible to capture by any method in normal PHP application design.

## Application-Level Preference

At the framework philosophy level, ONEPIECE Framework does not prefer exception throwing as the default way to model every application error.

Its preference is to communicate failure carefully back to the caller whenever practical.

That is more labor-intensive than throwing immediately.

However, it is considered more helpful for the side that uses the code.

This is a design preference, not a claim that exceptions never exist.

The framework still captures uncaught exceptions at the runtime level.

## What Is Collected

- Standard PHP errors through `set_error_handler()`
- Uncaught exceptions and other uncaught `Throwable` objects through `set_exception_handler()`
- Shutdown-time fatal errors through `register_shutdown_function()` and `error_get_last()`

## PHP Runtime Policy

`asset/config/php.php` currently sets:

- `display_errors = Off`
- `log_errors = Off`

This means the framework does not rely on PHP's default direct screen output or default PHP error log for normal application-facing error handling.

Instead, it routes collected errors into the framework's own storage and notice flow.

## Related Framework Documents

- `../op/invariants.md`
- `../op/responsibility-boundaries.md`
- `../op/common-recipes.md`

## What Is Excluded

The framework does not claim to capture errors that are inherently outside its reach.

Examples include:

- failures before PHP application execution becomes available
- failures before the framework bootstrap and handler registration complete
- exceptions that have already been handled by user code with `try/catch`

These are not considered gaps in the framework design. They are outside the range that the framework can practically observe.

## Storage

Captured errors are stored in the session.

The storage namespace is:

- `$_SESSION[_OP_NAME_SPACE_][_APP_ID_]['OP_ERROR']`

This keeps collected errors inside the current application scope.

## Output and Notification

When the application finishes, the stored errors are processed.

- If `OP()->isAdmin()` is `true`, the errors are displayed on the screen
- If `OP()->isAdmin()` is `false`, the errors are sent to the administrator by email

This design allows developers to inspect errors directly while keeping production users from seeing internal error details.

## Related Technical Documents

Technical details are documented separately.

- `asset/core/include/docs/error-handler.md`
- `asset/core/include/docs/error-handler.ja.md`
- `asset/unit/notice/docs/error-notice.md`
- `asset/unit/notice/docs/error-notice.ja.md`
