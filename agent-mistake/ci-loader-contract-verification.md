# Agent Mistake: CI Loader Contract Verification

## Summary

An AI agent changed a CI loader to match a private verification harness instead of the ONEPIECE Framework CI contract.

The mistake happened while adding CI support for `asset/module/counter/InitGuidance.class.php`.

The agent tried to verify the new CI files with an isolated `php -r` script that manually bootstrapped the framework and directly included CI files. That was not the same execution path as the framework CI runner.

Based on that flawed verification, the agent changed the CI loader from the established split CI layout:

```php
$name = basename(__FILE__);
$name = explode('.', $name)[0];
foreach( glob(__DIR__."/{$name}/*.php") as $path ){
	require_once($path);
}
```

to a `getcwd()`-based lookup.

That was wrong. The loader must follow the framework CI contract, not a local test harness.

## Root Cause

The agent treated a self-made verification environment as authoritative.

The framework already has a CI execution contract:

- class CI loaders follow the split CI layout
- the loader file name maps to the same-named method directory
- `asset/unit/html/ci/Html.php` is the reference pattern
- CI execution is responsible for setting the expected package context

The agent should have used that contract as the source of truth. Instead, it tried to make the implementation fit the behavior of a manual `php -r` script.

## Why This Is Dangerous

Verification code is allowed to be narrow, temporary, or incomplete.

Production and framework code is not.

If an agent changes production or package code to satisfy a private harness, it can silently break framework conventions that other packages rely on.

In this case, the risk was breaking the generic CI loader pattern used across UNIT and MODULE packages.

## Correct Verification Rule

Do not verify framework CI behavior by inventing an isolated execution path and then changing the implementation to match it.

For CI loader work:

1. Keep the loader contract aligned with `asset/unit/html/ci/Html.php`.
2. Prefer the real `./cicd` entrypoint or the framework CI runner path.
3. If a focused helper script is needed, make it reproduce the framework CI contract rather than redefine it.
4. If the focused helper disagrees with the framework runner, the helper is suspect first.
5. Never replace `__DIR__`-based package-local lookup with `getcwd()` just because a manual harness changed the current directory differently.

## Correct Pattern

Use the established split CI loader pattern:

```php
/* @var $ci \OP\UNIT\CI\CI_Config */
$ci = OP()->Unit()->CI()->Config();

$name = basename(__FILE__);
$name = explode('.', $name)[0];
foreach( glob(__DIR__."/{$name}/*.php") as $path ){
	require_once($path);
}

return $ci->Get();
```

The loader's job is to load method CI files located next to the loader under the same class name.

## Prevention

Before changing CI loader code:

1. Read `asset/docs/cicd/ci-file-layout.md`.
2. Compare against `asset/unit/html/ci/Html.php`.
3. Confirm whether the problem is in the class, namespace, `OP_CI`, CI config file name, or method config before changing the loader pattern.
4. Treat isolated verification scripts as diagnostic aids only.
5. Do not let a temporary verification script become the design authority.

