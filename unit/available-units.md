# Available Units

## Purpose

This document gives AI agents a compact list of ONEPIECE Framework UNIT packages that may be available through the skeleton dependency map.

When work needs database access, form handling, SQL, ORM, validation, or model-style persistence, use the responsible UNIT and its public API or configured mapping instead of hand-rolling a parallel application layer.

## Agent Rule

For DATABASE, FORM, ORM, SQL, validation, and model-style work:

1. Check whether a responsible UNIT already exists.
2. Use the unit public API or the configured unit mapping.
3. Do not implement a separate application-local layer for the same responsibility while an appropriate unit exists.
4. If the installed unit does not satisfy the need, decide whether to use application configuration, `asset/config/unit.php` mapping, a project-owned UNIT, a MODULE that composes units, or a change to the responsible framework unit package.

## Unit Mapping

`asset/config/unit.php` is the application-side mapping point for selecting compatible replacement implementations.

The skeleton dependency map for unit repositories lives under `asset/config/submodule/unit/`.

## Skeleton Unit Repository Map

| Unit | Dependency map | Typical responsibility |
| --- | --- | --- |
| API | `asset/config/submodule/unit/api.php` | API-related unit behavior. |
| App | `asset/config/submodule/unit/app.php` | Application flow after bootstrap. |
| Bitcoin RPC | `asset/config/submodule/unit/bitcoin-rpc.php` | Bitcoin RPC integration. |
| CD | `asset/config/submodule/unit/cd.php` | Continuous delivery orchestration. |
| CI | `asset/config/submodule/unit/ci.php` | Continuous integration checks. |
| Database | `asset/config/submodule/unit/database.php` | Database access. |
| Dump | `asset/config/submodule/unit/dump.php` | Debug and structured dump output. |
| Form | `asset/config/submodule/unit/form.php` | Form configuration, rendering helpers, submitted values, and form state. Use `asset/unit/form/docs/usage.md` first; read `current-spec.md` only for source-level troubleshooting or refactoring. |
| Git | `asset/config/submodule/unit/git.php` | Git-related operations. |
| Html | `asset/config/submodule/unit/html.php` | HTML-related output helpers. |
| Layout | `asset/config/submodule/unit/layout.php` | Layout rendering control. |
| Login | `asset/config/submodule/unit/login.php` | Login-related behavior. |
| Model | `asset/config/submodule/unit/model.php` | Model-style application data behavior. |
| Notice | `asset/config/submodule/unit/notice.php` | Notice and error notification behavior. |
| ORM | `asset/config/submodule/unit/orm.php` | ORM behavior and persistence abstraction. |
| QQL | `asset/config/submodule/unit/qql.php` | QQL query-related behavior. |
| Router | `asset/config/submodule/unit/router.php` | Route resolution. |
| SQL | `asset/config/submodule/unit/sql.php` | SQL query handling. |
| Validate | `asset/config/submodule/unit/validate.php` | Validation behavior, including validation used around forms and submitted data. |
| WebPack | `asset/config/submodule/unit/webpack.php` | Asset registration, grouping, and output generation. |

## Form And Validation

Use the Form unit through `OP()->Unit()->Form()`.

The Form unit source is `asset/unit/form`.

The Form unit owns form configuration, rendering helpers, submitted values, and form state. Validation is not owned by the Form unit; validation belongs to the Validate unit.

For ordinary Form usage, read `asset/unit/form/docs/usage.md`. For source-code troubleshooting, behavior investigation, or refactoring, read `asset/unit/form/docs/current-spec.md`.

## Related Documents

- `unit-replacement.md`
- `app-unit.md`
- `../../unit/form/docs/usage.md`
- `../../unit/form/docs/current-spec.md`
- `../op/unit-module-boundary.md`
- `../op/unit-module-authoring.md`
