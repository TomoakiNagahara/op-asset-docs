# To-Be

## Overview

This document is a human-friendly and AI-friendly collection of planned future directions.

It is not a replacement for local `[DOC-FUTURE]` notes.

Instead, it collects important To-Be items in one place so that future design direction does not remain scattered across many technical documents.

## Why This File Exists

As-Is and To-Be should be distinguishable.

The framework documentation uses this practical split:

- local technical documents
  primarily describe current As-Is behavior
- local documents
  may contain short `[DOC-FUTURE]` notes
- this file
  collects larger or cross-cutting To-Be directions

That helps both humans and AI avoid confusing:

- current behavior
- current bugs or gaps
- future goals
- intended redesign directions

## Current To-Be Directions

### CI and CD Responsibility Concentration

The ideal long-term structure is:

- CI-related responsibilities should be concentrated in `op-unit-ci`
- CD-related responsibilities should be concentrated in `op-unit-cd`

This is the preferred direction even though current implementation details are still historically scattered.

Primary references:

- `asset/docs/cicd/hooks.md`
- `asset/unit/ci/docs/ci-spec.md`
- `asset/unit/cd/docs/isCanPushToGithub.md`

### Prefix-Based Push Blocking Should Move into `op-unit-cd`

The current implementation still spreads prefix-based push blocking across hook-level files.

The preferred future design is to concentrate that push-policy responsibility into `op-unit-cd`.

Primary references:

- `asset/unit/cd/docs/isCanPushToGithub.md`

### Pass-Through Extension Control Should Move to Config

The current router hard-codes pass-through target extensions.

The preferred future direction is to move that policy into configuration.

Primary references:

- `asset/docs/new-world/html-pass-through.md`
- `asset/unit/router/docs/calc-route-2018.md`

### Each Layout Should Own WebPack Registration for Its Own Assets

The current implementation has paths in the WebPack module and WebPack unit that automatically register a layout's `js` / `css` directories.

In the ONEPIECE Framework To-Be, each layout owns selection and registration of its own layout-specific assets.

The preferred future direction is:

- `asset/layout/<layout-name>/js/` or `asset/layout/<layout-name>/css/` should not be packed merely because the directory exists
- each layout should explicitly register the JavaScript / CSS it needs through `OP()->Unit()->WebPack()->Auto()` in its own initialization or template flow
- the WebPack unit should focus on registered asset state, grouped output, cache, and minify behavior
- the WebPack module should focus on the delivery-side entry for grouped asset requests
- the WebPack unit/module should not automatically decide layout-specific asset policy

This keeps layout asset inclusion intentional and makes responsibility boundaries and debugging points clear.

Primary references:

- `asset/docs/important-gaps.md`
- `asset/docs/important-gaps.ja.md`
- `asset/docs/op/responsibility-boundaries.md`
- `asset/docs/op/responsibility-boundaries.ja.md`
- `asset/docs/webpack.md`
- `asset/docs/webpack.ja.md`
- `asset/docs/module/webpack.md`
- `asset/docs/module/webpack.ja.md`
- `asset/module/webpack/docs/as-is.md`
- `asset/module/webpack/docs/as-is.ja.md`

### `OP()->URL('.')` Should Not Return FQDN by Default

Current behavior returns a full URL with scheme and host.

The preferred future direction is to align this with framework philosophy and avoid returning FQDN at that abstraction level by default.

Primary references:

- `asset/core/docs/op-url.md`

### `isAdmin()` Localhost Auto-Admin Should Become Configurable

Current behavior treats localhost as admin automatically.

The preferred future direction is to make this configurable.

Primary references:

- `asset/docs/core/is-admin.md`
- `asset/core/docs/is-admin.md`

### Explicit API Is Preferred if Request Merge Is Ever Added

The current `OP()->Request()` behavior does not silently merge GET and POST.

If merged request access is ever added, the preferred future direction is an explicit API or explicit mode, not a silent change to the default behavior.

Primary references:

- `asset/docs/core/op-request.md`
- `asset/core/docs/op-request.md`

## Maintenance Rule

When a future design direction is important enough that humans should be able to review it in one place, add it here in addition to keeping local `[DOC-FUTURE]` notes in detailed documents.
