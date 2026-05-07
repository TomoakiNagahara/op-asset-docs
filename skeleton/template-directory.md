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

## [DOC-PRIORITY1] Current Lookup Priority

The framework specification is:

1. current directory
2. `asset/layout/<layout-name>/template/`
3. `asset/template/`
4. unit template directory

However, the current implementation does not follow that specification.

In current implementation, `OP()->Template()` checks template directories in this order:

1. current directory
2. unit template directory
3. `asset/layout/<layout-name>/template/`
4. `asset/template/`

This is a highest-priority specification gap because the intended order is already clear.

That means a file placed under:

- `asset/layout/<layout-name>/template/`

is judged before the same-named file under:

- `asset/template/`

This makes layout-specific shared templates the better default location for site-level customization.

## Practical Advice

When editing or adding templates, remember that `asset/template/` is closer to framework-default material than to purely user-owned application content.

That should be considered before mixing local application templates into the same area.
