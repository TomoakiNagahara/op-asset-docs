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
