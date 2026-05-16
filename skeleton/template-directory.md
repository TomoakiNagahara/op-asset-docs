# Template Directory

## Overview

In this skeleton, `asset/template/` mainly stores default templates used by the framework itself.

It is not primarily intended as the general storage place for every developer-facing or end-user-facing application template.

## Operational Meaning

A developer or end user may still choose to store their own templates there.

However, doing so can make commits and template ownership harder to manage.

In other words:

- it is technically possible
- but it can create operational friction in version control

If the user can manage that tradeoff intentionally, it is still allowed.

## Recommended Operating Pattern

The normal framework operating pattern is to create a new site-specific layout under:

- `asset/layout/<layout-name>/`

When shared templates are needed, such as:

- header
- footer
- menu

they should usually be placed in:

- `asset/layout/<layout-name>/template/`

rather than in `asset/template/`.

## Intended Lookup Priority

The framework specification is:

1. current directory
2. `asset/layout/<layout-name>/template/`
3. `asset/template/`
4. unit template directory

[DOC-PRIORITY1] The current `OP()->Template()` As-Is does not follow that specification.

The current As-Is is owned by the `OP_TEMPLATE` trait documentation. See `asset/core/trait/docs/op-template.md`.

That means a file placed under:

- `asset/layout/<layout-name>/template/`

is judged before the same-named file under:

- `asset/template/`

This makes layout-specific shared templates the better default location for site-level customization.

## Current Directory Behavior

`OP()->Template()` current-directory behavior depends on the `OP_TEMPLATE` trait implementation.

For the current As-Is, see `asset/core/trait/docs/op-template.md`.

## Practical Advice

When editing or adding templates, remember that `asset/template/` is closer to framework-default material than to purely user-owned application content.

That should be considered before mixing local application templates into the same area.
