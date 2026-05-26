# Common Recipes

## Purpose

This document gives task-oriented patterns that help AI and contributors carry out common work safely.

## Related Documents

- `for-tomoaki-nagahara.md`
- `invariants.md`
- `responsibility-boundaries.md`

## Add a Page

- start in `asset/template/`
- confirm how Router resolves the endpoint
- confirm whether Layout should wrap the output
- verify 404 behavior is unchanged for unknown routes

## Change Layout

- start in `asset/layout/`
- verify the page is HTML-rendered through Layout
- avoid moving routing logic into layout logic

## Add Local-Only Configuration

- put shared defaults in `asset/config/name.php`
- put machine-local values in `asset/config/_name.php`
- avoid hardcoding environment-specific values in templates

## Add UNIT or MODULE Default Configuration

- put package-owned default config in `asset/unit/<unit-name>/config.php` or `asset/module/<module-name>/config.php`
- put user-defined application config in `asset/config/<name>.php`
- put machine-local overrides in `asset/config/_<name>.php`
- merge config layers with `array_replace_recursive()` semantics
- keep default config values suitable for the package's normal behavior
- make default values self-explanatory in the config file; use comments to show valid values and explain each value's runtime effect so third-party users can understand the setting without reading separate docs or asking an AI assistant
- note that current `Config::Get(<name>)` automatically loads `asset/unit/<name>/config.php`, but never automatically loads `asset/module/<name>/config.php`
- treat `asset/module/<module-name>/config.php` as a template that users copy to `asset/config/<module-name>.php` when they want the MODULE config to affect runtime behavior

## Keep Normal Requests Memory-Light

- keep normal successful request paths small
- do not load code used only for initialization failures, recovery guidance, diagnostics, rare maintenance paths, or large optional helpers
- move rare-path logic into a focused file or class
- load that file or class only after the condition that needs it has actually occurred
- prefer public framework APIs such as `OP()->Path('asset:/...')` when lazy-loading framework or package files

## Replace a Unit

- identify the relevant interface contract
- confirm whether the unit has a typed mapper accessor
- use `asset/config/unit.php` mapping when replacement should be application-controlled
- keep caller-visible behavior aligned with the interface contract

## Call a Non-Typed Unit

- use `OP()->Unit('UnitName')`
- prefer this when the unit is not officially exposed through typed mapper methods

## Add CI Support to a Repository

- ensure `ci.sh` or `.ci.sh` exists
- provide CI config for inspected classes and methods
- confirm the repository can produce `.ci_commit_id_<branch>_php<version>`

## Understand Why CI Did Not Run

Check for:

- missing `ci.sh` / `.ci.sh`
- `.ci_skip`
- missing `.git`
- a fresh matching `.ci_commit_id_*` file that caused skip behavior

## Understand Why Push Was Blocked

Check for:

- CI marker file missing
- CI marker commit ID mismatch
- `pre-push-prefix.php` rejection
- GitHub-specific branch restriction in CD logic

## Use the `local` Remote Workflow

- use `local` when private, fast, or offline history storage is needed
- remember that `local` skips the CI gate only
- remember that prefix checks still continue

## Add Grouped JS/CSS Delivery

- read `frontend-asset-authoring.md` before writing JavaScript or CSS
- use `op-unit-webpack` to register files or directories
- use the `webpack` module delivery entry
- verify whether layout-specific asset directories should be included

## Investigate Error Visibility

- check `OP()->isAdmin()`
- check `asset/config/admin.php`
- check whether errors are stored through `OP_ERROR`
- check whether Notice is rendering or mailing the stored error
