# Agent Mistake: `OP()` Function Resolution

## Summary

An AI agent introduced an incorrect function import while trying to call `OP()` from the `OP\MODULE\COUNTER` namespace.

The incorrect change was:

```php
use function OP\OP;
```

This made unqualified `OP()` calls resolve to `OP\OP()`.
However, ONEPIECE Framework defines `OP()` as a global function, not as `OP\OP()`.

The resulting runtime error was:

```text
Call to undefined function OP\OP()
```

The screenshot `op-function-undefined-function-error.png` captured this failure.

![Runtime error screenshot](op-function-undefined-function-error.png)

## Root Cause

The agent misunderstood the `OP()` function contract.

`asset/docs/core/op-function.md` explains that `OP()` is the unified framework entry point.
At implementation level, the global `OP()` function returns the reusable singleton instance of `\OP\OP`.
`asset/core/function/OP.php` confirms this directly: it has no namespace declaration and defines `function OP()`.

That means:

- `OP()` is the function to call.
- `\OP\OP` is the class returned by the global function.
- `OP\OP()` is not a framework function.
- `use function OP\OP;` is wrong for this module.

## Why The Agent Got It Wrong

The mistake came from three bad assumptions.

First, the agent treated the module namespace as a reason to import `OP()` instead of following the framework contract. In normal PHP, an unqualified function call inside a namespace may look like it needs namespace handling. But the framework intentionally provides `OP()` as a global ergonomic entry point. The correct response was to trust that contract, not to invent a namespaced import.

Second, the agent confused the function and the class. The documentation says the global `OP()` function returns the singleton instance of `\OP\OP`. The agent incorrectly converted the returned class name into a function name, producing `OP\OP()`.

Third, the verification was too weak. `php -l` cannot catch missing runtime functions. A local stub test also hid the problem because it defined a namespaced `OP\OP()` function-like path that did not match the real framework. The test should have used the real bootstrap or a stub that matches the real global `OP()` function exactly.

The key lesson is that namespace fixes are not mechanical. Before adding `\`, `use function`, or imports, confirm the symbol's real declaration and framework contract.

## Correct Pattern

Use `OP()` directly:

```php
if(!OP()->isAdmin() ){
	return true;
}

return IsOne(OP()->Request('admin'));
```

Do not add:

```php
use function OP\OP;
```

Do not write:

```php
\OP\OP()
```

## Why This Matters

This module is loaded through `OP()->Template()`.
If `save.php` includes `function.php` and `function.php` calls the wrong function name, the page fails during template execution before the counter can save or display.

The stack trace in `op-function-undefined-function-error.png` showed the failure path:

```text
asset/module/counter/function.php
asset/module/counter/save.php
asset/core/trait/OP_TEMPLATE.php
welcome.phtml
```

## Prevention

Before editing calls to ONEPIECE Framework APIs:

1. Read `asset/docs/core/op-function.md`.
2. Inspect the actual declaration when there is any namespace uncertainty. For `OP()`, check `asset/core/function/OP.php`.
3. Confirm whether the target is a global function, namespaced function, class, or method.
4. Do not add namespace imports for `OP()` unless the framework documentation explicitly says to.
5. If using a test stub, make the stub match the real symbol shape. For `OP()`, define global `function OP()`, not a namespaced function.
6. Verify with an execution path that includes the target template, not only `php -l`.

For this module, keep the rule simple:

```text
Call OP() directly. Do not import OP\OP as a function.
```
