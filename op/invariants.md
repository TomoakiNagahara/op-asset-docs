# Invariants

## Purpose

This document defines the current framework-level invariants that should not be changed casually.

These are not implementation details.

They are stable contracts that AI and contributors should preserve unless the task explicitly requires a contract-level change.

## Related Documents

- `for-tomoaki-nagahara.md`
- `responsibility-boundaries.md`
- `unit-module-boundary.md`
- `php-version-support.md`
- `common-recipes.md`

## Entry Point and Startup

- `app.php` is the application entry point.
- The web server is expected to route application execution to `app.php`.
- `app.php` initializes the framework and then hands control to the app unit.
- Normal request flow should continue through `OP()->Unit()->App()->Auto()`.

## Startup Responsibility Split

- `app.php` is for framework startup, not for application-specific page logic.
- Request handling after startup belongs to the app unit and downstream units.
- The split between startup and application execution should remain clear.

## Request Lifecycle

- The Router -> App -> Layout flow of NEW WORLD is a core execution model.
- Routing, template execution, buffered content handling, and layout output should remain separated by responsibility.
- Layout is not the first execution stage; it is a later rendering stage.

## Layout and Non-Layout Behavior

- Layout execution must remain dynamically controllable.
- Non-HTML responses must be able to avoid layout rendering.
- This is part of the framework design for JS, CSS, JSON, image-like, and other dynamic response handling.

## Documentation Placement Contract

- Web-server-related documents belong in `asset/docs/httpd/`.
- Framework-level CI/CD philosophy, background, and history belong in `asset/docs/cicd/`.
- Unit-system philosophy, background, and history belong in `asset/docs/unit/`.
- Module-system philosophy, background, and history belong in `asset/docs/module/`.
- NEW WORLD philosophy, background, and history belong in `asset/docs/new-world/`.
- op-core philosophy, background, and high-level core feature documents belong in `asset/docs/core/`.
- Framework-wide philosophy, design intent, and background belong in `asset/docs/op/`.
- Skeleton-specific framework documents belong in `asset/docs/skeleton/`.
- If a framework-level document does not fit any of the categories above, it may remain directly under `asset/docs/`.
- Japanese translations of framework-level documents should live next to the English file and use the suffix `.ja.md`.
- As-Is, technical implementation details, unit-internal flows, and encapsulated subsystem behavior belong in each subsystem's own `docs/`.
- A document's content must match the responsibility scope implied by its path.
- `asset/docs/module/<name>.md` should explain the purpose, responsibility boundary, and framework-level position of that module-system package.
- Current behavior, entry points, internal call flow, and implementation details of a specific module belong in that module package's own `docs/`.
- Do not force a framework-wide concept into a document for one specific unit, module, or package.
- Concepts that span multiple packages belong in a higher-level framework document, with individual package documents linking to it.

## CI/CD Integration

- Git hooks are part of the framework workflow, not optional decoration.
- `git push` should normally be checked against CI state before shared publication.
- CI marker files are part of the current push control model.
- The `local` remote exception is an operational exception, not a removal of all push rules.

## PHP Runtime Support

- The 2030 runtime support baseline is PHP 8.0 or higher.
- PHP 7.x is not part of the supported 2030 runtime line.
- `asset/core/Bootstrap.php` is the implementation-level runtime gate for the minimum PHP version.

## Rolling Update Contract

- The yearly branch model is part of the framework operating model.
- `_OP_APP_BRANCH_` and branch naming are expected to stay aligned.
- Rolling update compatibility is a framework responsibility.

## Unit Access and Replacement

- `OP()->Unit('Name')` remains the generic access path.
- `OP()->Unit()->App()`-style typed access exists only for mapper-supported units.
- Interface-based contracts and mapping-based replacement are part of the framework design.

## Namespace Contract

- ONEPIECE Framework namespace rules are not derived from file paths.
- Framework functions and classes live under the `OP` namespace, except for the global `OP()` and `D()` functions.
- Core features use the direct `OP` namespace.
- Unit classes live under `OP\UNIT`.
- Module classes live under `OP\MODULE`.
- Do not infer namespace placement from the directory structure alone.

## Configuration Override

- Shared configuration should live in `name.php`.
- Local-only override should live in `_name.php`.
- The layered config loading order is part of the expected operating model.

## Error Handling Model

- The framework should collect all practically reachable errors after bootstrap.
- Stored errors are later handled through the framework notice path.
- Admin-only screen display versus non-admin mail notification is part of the current operating model.

## Admin Decision Role

- `OP()->isAdmin()` is a major environment decision point.
- It affects debug visibility, notice rendering, and developer-facing behavior.
- The current localhost shortcut is part of the present design, even if it may become configurable later.
