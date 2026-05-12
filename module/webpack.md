# WebPack Module

## Overview

This document explains what `op-module-webpack` is for in the ONEPIECE Framework.

It does not describe the whole WebPack mechanism.

For the framework-level WebPack overview, see `../webpack.md`.

For current implementation details of `op-module-webpack`, see `asset/module/webpack/docs/as-is.md` in the `op-module-webpack` package.

## Related Framework Documents

- `../webpack.md`
- `../op/invariants.md`
- `../op/responsibility-boundaries.md`
- `../op/common-recipes.md`

## Purpose

`op-module-webpack` is the delivery-side module for grouped asset requests.

Its purpose is to receive WebPack request paths and hand them off to `op-unit-webpack` for actual grouped output generation.

In other words, it is the request-facing adapter between public WebPack URLs and the WebPack unit.

## Responsibility Boundary

`op-module-webpack` should own:

- delivery-side request entry for grouped assets
- request-to-asset-type routing
- delegating output work to `op-unit-webpack`

`op-module-webpack` should not own:

- asset registration state
- cache / minify / output internals
- standard JavaScript / CSS asset definitions
- layout-specific asset policy

## Related Packages

`op-module-webpack` works with, but is separate from:

- `op-unit-webpack`
- `op-webpack-js`
- `op-webpack-css`

`op-unit-webpack` owns registration state and grouped output behavior.

`op-webpack-js` and `op-webpack-css` provide standard framework JavaScript / CSS assets.

## Documentation Placement

Framework-level WebPack concepts belong in:

- `asset/docs/webpack.md`

Current `op-module-webpack` behavior belongs in:

- `asset/module/webpack/docs/as-is.md`

This file exists only to explain the module's purpose and responsibility boundary from the framework documentation tree.
