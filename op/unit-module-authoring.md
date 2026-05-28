# UNIT/MODULE Authoring

## Purpose

This document records the authoring rules for creating or restructuring ONEPIECE Framework UNIT and MODULE packages.

It is intentionally focused on implementation discipline.
For deciding whether behavior belongs in a UNIT or MODULE, see `asset/docs/op/unit-module-boundary.md`.
For framework-wide coding rules such as public-name spelling checks, see `asset/docs/op/coding-rules.md`.

## Required Reading

Before coding a UNIT or MODULE, read these documents:

- `asset/docs/op/unit-module-authoring.md`
- `asset/docs/op/coding-rules.md`
- `asset/docs/cicd/ci-file-layout.md`
- `asset/docs/op/common-recipes.md`
- `asset/docs/op/design-philosophy.md`

Also read the responsible dictation files before working.
User dictation is split by speaker language, such as `asset/docs/dictation.ja.md` or `asset/docs/dictation.en.md`, and agent-facing guidance is summarized in `asset/docs/dictation.md`.

## Full Inspection Principle

UNIT and MODULE package code should be designed for full inspection by CI.

When adding reusable behavior, do not hide it from CI by placing it in a directory or filename pattern that the framework CI collector does not inspect.

In principle:

- reusable behavior belongs in class files that can participate in class-based CI
- visible `*.class.php` files in a UNIT or MODULE package must follow the class CI contract
- each CI-target class should use `OP_CI`
- deterministic behavior should be listed in `CI_AllMethods()`
- inspected methods should have method-level CI config under `ci/<ClassName>/<MethodName>.php`

Memory-conscious design and full inspection are not opposites.

Do not avoid CI by hiding optional code in an unscanned directory.
Keep optional code out of normal request memory by lazy-loading it only when needed, while still keeping the code in a CI-visible class when it owns package behavior.

For example, an optional MODULE feature can be:

- stored as `OptionalFeature.class.php` in the module root so CI can inspect it
- loaded by the request entry file only when that optional feature is requested
- tested through `ci/OptionalFeature.php` and `ci/OptionalFeature/<MethodName>.php`

## CI File Layout

Use the split CI file layout described in `asset/docs/cicd/ci-file-layout.md`.

The class file and CI files should line up by name:

```text
asset/unit/<unit-name>/<ClassName>.class.php
asset/unit/<unit-name>/ci/<ClassName>.php
asset/unit/<unit-name>/ci/<ClassName>/<MethodName>.php

asset/module/<module-name>/<ClassName>.class.php
asset/module/<module-name>/ci/<ClassName>.php
asset/module/<module-name>/ci/<ClassName>/<MethodName>.php
```

Do not treat an empty `CI_AllMethods()` as the final design for behavior-owning classes.
It can be a temporary bridge, but the preferred final state is to inspect stable deterministic methods.

## Namespace Placement

Main UNIT and MODULE classes are exposed under `OP\UNIT` or `OP\MODULE`.

Helper and sub classes must be isolated under the package subnamespace so they do not collide with other packages.

Examples:

- main module class: `OP\MODULE\Counter`
- module helper class: `OP\MODULE\COUNTER\Calendar`
- main unit class: `OP\UNIT\Html`
- unit helper class: `OP\UNIT\HTML\SomeHelper`

## Runtime Loading

Keep normal successful request paths small.

Optional, diagnostic, recovery, maintenance, or error-only classes should not be loaded during unrelated successful requests.

Use lazy loading for runtime memory savings:

```php
require_once __DIR__ . '/OptionalFeature.class.php';
```

Use `__DIR__` for fixed files inside the same package.
Use public framework path APIs only when path abstraction is actually needed.

## Implementation Discipline

Keep UNIT and MODULE code small, direct, and memory-conscious.

- Do not add unnecessary classes or methods.
- Do not split a method only because splitting is possible.
- A method that is called from only one place does not need to be separated unless it removes real complexity, isolates rare-path loading, or matches an established package pattern.
- Keep entry files thin; they should initialize, load the responsible class, and call the minimum package behavior.
- Keep optional, diagnostic, recovery, maintenance, and error-only code out of normal request memory.
- Return only the minimum value the caller needs. For example, do not build and return an array when the caller only needs success or failure.
- Use framework APIs instead of raw PHP globals. For session state, use `OP()->Session()` instead of raw `$_SESSION`.

## Path APIs

Do not use framework-internal root constants such as `_ROOT_ASSET_`, `_ROOT_APP_`, or `_ROOT_CORE_` in end-user, application, UNIT, or MODULE code.

These constants are reserved for framework internals.
If end-user or package code depends on them, future core deprecation or replacement becomes much harder.

Use public meta-path APIs when path abstraction is needed:

- `OP()->Path('asset:/...')` for environment-dependent local file paths
- `OP()->URL('app:/...')` for public URLs
- `OP()->Template('asset:/...')` for template inclusion

When loading a file at a fixed location inside the same repository and same package, prefer `__DIR__ . '/file.php'` because no framework path abstraction is needed.

## Documentation

When adding a class file, add or update the same-named documentation beside the package docs when the behavior needs explanation.

Examples:

- `Calendar.class.php`
- `docs/Calendar.class.md`
- `docs/Calendar.class.ja.md`

Document why optional code is lazy-loaded, and also document that lazy loading must not remove the code from CI visibility when the class owns package behavior.

Do not pack detailed rules into `AGENTS.md`.
Keep `AGENTS.md` as a thin routing document and put detailed rules in the responsible document, such as this file, `asset/docs/op/coding-rules.md`, or `asset/docs/cicd/ci-file-layout.md`.
