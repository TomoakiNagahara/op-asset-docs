# Important Gaps

## Overview

This document is a human-friendly index of important documented gaps.

Its purpose is to collect high-priority or practically important mismatches in one place, so they do not remain scattered across many documents.

This file is not a replacement for local `[DOC-GAP]`, `[DOC-FUTURE]`, or `[DOC-PRIORITY1]` notes.

It is a curated summary for human review.

## Highest Priority

### [DOC-PRIORITY1] `OP()->Template()` Lookup Priority

Current status:

- The intended specification order is:
  1. current directory
  2. `asset/layout/<layout-name>/template/`
  3. `asset/template/`
  4. unit template directory
- The current implementation order is:
  1. current directory
  2. unit template directory
  3. `asset/layout/<layout-name>/template/`
  4. `asset/template/`

Why it matters:

- This changes which template wins when the same file name exists in multiple places.
- The intended framework operating pattern for shared site templates depends on the specified order.

Primary references:

- `asset/docs/skeleton/template-directory.md`
- `asset/docs/skeleton/template-directory.ja.md`
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
