# CI Purpose

This document records the intended purpose of CI in the ONEPIECE Framework.

CI is not only a syntax check. Its primary purpose is to mechanically confirm that shared behavior contracts have not been broken.

## Class Contracts

In ONEPIECE Framework, class files are especially important because CORE, UNIT, and MODULE classes become reusable boundaries for other code.

The class-based CI flow discovers `*.class.php` files, requires `OP_CI`, and uses `CI_AllMethods()` and `CI_Inspection()` to inspect behavior.

This means CI is checking the contract that a class exposes to callers:

- expected inputs
- expected outputs
- behavior that should remain stable across changes

## Responsibility Boundaries

CI also clarifies responsibility boundaries.

The current framework CI does not inspect every function or template indiscriminately. It focuses on class-based contracts so each package can say:

- this class is part of the behavior this package is responsible for
- this class must keep its inspected contract stable
- this package accepts CI responsibility for the class methods listed by `CI_AllMethods()`

`OP_CI` is therefore not just a technical requirement. It is the entry point by which a class participates in the framework CI contract.

## Mechanical Regression Detection

CI exists so regressions are detected by machinery rather than by human memory.

When a change breaks an expected class behavior, CI should point to the broken method and expected result. That makes the failure actionable and repeatable.

## Non-CI Helpers

[DOC-ISSUE] A helper class that exists only for rare recovery or error handling can still be discovered by CI if it is placed as a visible `*.class.php` file in a package root or scanned class directory.

When that happens, the question is not only how to make CI pass. The design question is:

- is this class part of the CI-inspected contract?
- or is this class intentionally outside the CI target boundary?

In ONEPIECE Framework, environment-dependent code should not be excluded from CI too quickly.

The framework already provides `OP()->isCI()` as the standard way to know whether code is running under CI inspection. If a method depends on runtime environment values, split that dependency into a small method and return a fixed deterministic value while `OP()->isCI()` is true.

Example:

```php
function GetPosixUid()
{
	return OP()->isCI() ? 1000 : posix_geteuid();
}
```

This pattern is used by existing framework units, such as CI cases that need deterministic database records. It can replace an external stub class when the framework itself can provide the CI-mode branch.

Therefore, for a visible `*.class.php` helper, the preferred order is:

1. keep the class in CI when it is part of package behavior that can be refactored or broken
2. isolate environment reads into small methods
3. use `OP()->isCI()` to return deterministic values during CI
4. inspect the stable contract through `OP_CI`, `CI_AllMethods()`, and method-level CI config

Adding `OP_CI` and returning an empty `CI_AllMethods()` is only a temporary bridge, not the preferred final design.

Choosing a placement or filename that CI does not collect is reserved for code that is intentionally not part of the package behavior contract. That decision must be explicit and documented near the package.
