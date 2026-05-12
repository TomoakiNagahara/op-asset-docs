# WebPack

## Overview

This document describes WebPack in the ONEPIECE Framework.

WebPack is the framework-level mechanism for delivering JavaScript / CSS assets together when needed.

It is not a mechanism that automatically collects every file just because it exists in a directory.

The default model is explicit registration through `OP()->Unit()->WebPack()->Auto()`.

## Components

The main packages related to WebPack are:

- `op-unit-webpack`
- `op-module-webpack`
- `op-webpack-js`
- `op-webpack-css`

## `op-unit-webpack`

`op-unit-webpack` is the unit side of WebPack.

Its main responsibilities are:

- managing asset registration state
- producing grouped output from registered assets
- handling cache, minify, and output behavior

`op-unit-webpack` should not automatically decide which layout assets should be used.

### File Registration

Use `OP()->Unit()->WebPack()->Auto()` to register files with WebPack.

`Auto()` accepts targets in the following forms:

- multiple files at once
- directory paths
- wildcard paths

### Path Rules

File and directory paths passed to WebPack follow these rules:

- meta paths can be used
- full paths cannot be specified
- upper directories cannot be specified
- current path references can be used

The full path and upper directory restrictions are security measures that prevent unintended access to server-side files.

## `op-module-webpack`

`op-module-webpack` is the delivery-side module for grouped asset requests.

Its main responsibilities are request entry and output delivery.

`op-module-webpack` is not the whole WebPack concept.

It should also not own layout-specific asset policy.

## `op-webpack-js` / `op-webpack-css`

`op-webpack-js` and `op-webpack-css` are packages for framework-provided standard JavaScript / CSS assets.

They are referenced as `asset:/webpack/js/` and `asset:/webpack/css/`.

These files are not packed automatically.

Applications and layouts may refer to them, use them, or ignore them.

The recommended usage is to create symbolic links from the layout's `js` or `css` directory to the required standard assets.

Then each layout explicitly registers those assets through `OP()->Unit()->WebPack()->Auto()` as its own layout-owned assets.

This keeps the choice of standard assets clear at the layout level.

## Relationship With Layouts

In ONEPIECE Framework philosophy, each layout owns selection and registration of its own layout-specific assets.

`asset/layout/<layout-name>/js/` or `asset/layout/<layout-name>/css/` should not be packed automatically just because the directory exists.

A layout explicitly registers the assets it needs with WebPack.

## Related Documents

- `module/webpack.md`
- `op/responsibility-boundaries.md`
- `important-gaps.md`
- `to-be.md`
