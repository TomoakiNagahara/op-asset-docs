# interface

Source folder: `asset/core/interface/`

For overall OP-CORE context, see `overview.md`.

`interface` defines core contracts for OP units, modules, framework services, and external integrations.

This folder owns `IF_*.php` interface files such as `IF_UNIT.php`, `IF_ROUTER.php`, `IF_LAYOUT.php`, `IF_HTML.php`, `IF_DATABASE.php`, `IF_SQL.php`, `IF_CI.php`, `IF_SELFTEST.php`, and `IF_WEBPACK.php`.

Use this folder when the public method contract for a framework component must be defined or changed. Do not place implementation, runtime state, test cases, or tutorial examples here.

Interface changes are high-impact because they affect implementers across units and modules; keep each interface documented and committed by file.
