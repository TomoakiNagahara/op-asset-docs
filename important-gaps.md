# Important Gaps

## Overview

This document is a human-friendly index of important documented gaps.

Its purpose is to collect high-priority or practically important mismatches in one place, so they do not remain scattered across many documents.

This file is not a replacement for local `[DOC-GAP]`, `[DOC-FUTURE]`, or `[DOC-PRIORITY1]` notes.

It is an index for human review, not the detailed owner of each gap.

Do not add full current status, reasoning, or planned direction here when another document owns the gap.
For ordinary gap entries, keep only:

- a short title
- the searchable tag or search terms
- the primary owner document references

Put detailed current status, reasoning, and planned direction in the owner document.

## Highest Priority

### [DOC-PRIORITY1] `OP()->Template()` Lookup Priority

Current status:

- The intended specification order is:
  1. current directory
  2. `asset/layout/<layout-name>/template/`
  3. `asset/template/`
  4. unit template directory
- The current `OP()->Template()` As-Is is owned by the `OP_TEMPLATE` trait documentation.
- See `asset/core/trait/docs/op-template.md` for the current As-Is lookup order and current-directory behavior.

Why it matters:

- This changes which template wins when the same file name exists in multiple places.
- The intended framework operating pattern for shared site templates depends on the specified order.
- `OP()->Template()` current-directory behavior is implementation-dependent, so documents outside the trait docs should refer to the trait As-Is instead of duplicating it.

Primary references:

- `asset/docs/skeleton/template-directory.md`
- `asset/docs/skeleton/template-directory.ja.md`
- `asset/core/trait/docs/op-template.md`
- `asset/core/trait/docs/op-template.ja.md`
- `asset/core/trait/OP_TEMPLATE.php`

## Important Current Gaps

### `OP()->URL('.')` returns a full URL with FQDN

Current status:

- `OP()->URL('.')` currently returns `scheme://host/request_uri`

Why it matters:

- Framework philosophy prefers not to include FQDN at the URL abstraction level.

Planned direction:

- expected to be corrected later

Primary references:

- `asset/core/docs/op-url.md`
- `asset/core/docs/op-url.ja.md`

### Pass-Through extension control is hard-coded

Current status:

- pass-through target extensions are hard-coded in the current router implementation

Why it matters:

- this conflicts with the broader framework idea that such policy should live in configuration

Planned direction:

- move pass-through extension control into config

Primary references:

- `asset/docs/new-world/html-pass-through.md`
- `asset/docs/new-world/html-pass-through.ja.md`
- `asset/unit/router/docs/calc-route-2018.md`
- `asset/unit/router/docs/calc-route-2018.ja.md`

### Empty `ci.sh` can bypass the CI gate

Current status:

- current hook resolution prefers `ci.sh` before `.ci.sh`
- an empty `ci.sh` therefore lets the CI gate succeed without real inspection

Why it matters:

- this is an implementation side effect, not an intended stable contract

Planned direction:

- treat it as changeable As-Is behavior, not as a reliable specification

Primary references:

- `asset/unit/ci/docs/ci-spec.md`
- `asset/unit/ci/docs/ci-spec.ja.md`
- `asset/unit/ci/docs/ci-flow.md`
- `asset/unit/ci/docs/ci-flow.ja.md`
- `asset/docs/cicd/hooks.md`
- `asset/docs/cicd/hooks.ja.md`

### Pre-commit non-source-document diff-context mismatch

[DOC-GAP] Search terms: `pre-commit`, `non-source document`, `documentation hook`, `diff context`, `added lines only`.

Primary reference:

- `asset/docs/cicd/git/rules.md`

### WebPack module / unit automatically registers layout asset directories

Current status:

- `asset/module/webpack/content/js/index.php` and `asset/module/webpack/content/css/index.php` register `asset:/layout/<layout>/<extension>/` through `WebPack()->Auto()`
- `op-unit-webpack` also has a path that reads the `layout` request value and registers the layout asset directory

Why it matters:

- `js` / `css` directories under a layout should not be packed automatically
- In ONEPIECE Framework philosophy, each layout should explicitly register its own assets
- When the packing unit or an intermediate module automatically registers layout assets, responsibility boundaries become unclear and can cause unintended asset inclusion or debugging difficulty

Intended direction:

- layout-specific WebPack registration should be performed explicitly by each layout in its own initialization or template flow
- the WebPack unit should focus on registered asset state, output, cache, and minify behavior
- the WebPack module should focus on the delivery-side request entry and should not own layout asset policy

Primary references:

- `asset/docs/module/webpack.md`
- `asset/docs/module/webpack.ja.md`
- `asset/module/webpack/docs/as-is.md`
- `asset/module/webpack/docs/as-is.ja.md`
- `asset/module/webpack/content/js/index.php`
- `asset/module/webpack/content/css/index.php`
- `asset/unit/webpack/WEBPACK_2024.trait.php`

## Lower-Priority Planned Adjustments

### `isAdmin()` localhost auto-admin remains configurable only by code today

Current status:

- localhost is automatically treated as admin in current behavior

Planned direction:

- make this configurable later

Primary references:

- `asset/docs/core/is-admin.md`
- `asset/docs/core/is-admin.ja.md`
- `asset/core/docs/is-admin.md`
- `asset/core/docs/is-admin.ja.md`

## Maintenance Rule

When a new gap is important enough that a human should be able to find it quickly without reading many separate documents, add it here as well as keeping the local tag in the detailed document.
