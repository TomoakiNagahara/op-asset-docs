# Rolling Update

## Overview

The ONEPIECE Framework uses a rolling update model.

In this model, the main branch changes when the year changes.

Examples:

- `2025` -> `2026`
- `2030` -> `2031`

This is a normal part of the framework workflow.

## Branch Rule

The main branch is year-based.

That means the branch name itself represents the active application generation or yearly line.

When the year changes, the framework moves to the next branch name instead of continuing indefinitely on the previous year branch.

## Configuration Source

The active application branch is configured in:

`asset/config/op.php`

This file defines:

```php
define('_OP_APP_BRANCH_', 2030);
```

The `_OP_APP_BRANCH_` constant is the branch identifier used by the framework workflow.

## How to Move to the Next Year

When moving from one yearly line to the next, both of the following must be updated together:

1. change `_OP_APP_BRANCH_`
2. change the Git branch name

For example:

- change `_OP_APP_BRANCH_` from `2030` to `2031`
- rename or switch the branch name from `2030` to `2031`

These two values are expected to stay aligned.

## Operational Meaning

This rolling update model keeps the branch structure explicit.

It means:

- the current main line is visible from the branch name
- yearly transitions are handled by moving to the next branch
- framework configuration and Git branch naming must match

It also has an important operational advantage:

- updates can be delayed intentionally

If a project does not want to move immediately to the next yearly line, it can remain on the current year branch until the timing is appropriate.

## Why the Branch Name Is a Year

The year-based branch model is used not only for naming clarity, but also for compatibility control.

The same year-based branch rule is applied across related repositories such as:

- CORE
- UNIT
- application repositories

Because of that, repositories can stay aligned on the same yearly specification line.

This helps prevent specification mismatches between repositories when behavior changes over time.

In practice, this means:

- a project can stay on one yearly line intentionally
- CORE and UNIT can stay on the matching yearly branches
- incompatible specification changes are less likely to be mixed accidentally
- CI can keep passing within the same aligned yearly line

## Summary

The rolling update rule of the ONEPIECE Framework is:

- the main branch is year-based
- when the year changes, the main branch also changes
- `_OP_APP_BRANCH_` in `asset/config/op.php` must match that branch name
- the year-based branch model allows updates to be delayed intentionally
- using the same year branch across CORE and UNIT helps avoid specification mismatches and helps keep CI passing

For example, if `_OP_APP_BRANCH_` changes from `2030` to `2031`, the branch name must also change to `2031`.
