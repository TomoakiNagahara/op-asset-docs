# WebPack

## Overview

This document describes WebPack in the ONEPIECE Framework.

WebPack in the ONEPIECE Framework is the framework's own system for grouping multiple files by extension and delivering each extension group with a single request.

For example, multiple CSS files are grouped into one CSS request, and multiple JavaScript files are grouped into one JavaScript request.

It is not Node.js webpack and should not be confused with the Node.js build tool named `webpack`.

In ONEPIECE Framework documents, `WebPack` refers to this framework-native grouping and delivery system unless explicitly stated otherwise.

It is not a mechanism that automatically collects every file just because it exists in a directory.

The default model is explicit registration through `OP()->Unit()->WebPack()->Auto()`.

## Packed Content Hash

WebPack packs content by extension. Each extension group is built from the registered files for that extension.

WebPack generates a hash value for each packed extension group.

The ONEPIECE Framework concept does not force what the hash must be generated from. A WebPack unit may derive it from the packed binary output, the content values, the registered file list, or another unit-owned cache identity.

The responsibility for choosing and implementing the hash source belongs to `op-unit-webpack`.

The generated hash is included in the grouped asset URL. When the delivery request contains that hash, WebPack can output the cached packed content for the corresponding extension group.

This hash is also useful during debugging. It gives a visible clue about which packed content version the page requested, and whether the browser, server-side cache, or WebPack unit state is still pointing at an older packed result.

## File Authoring Rules

Because WebPack groups multiple files of the same extension into one request, frontend files must follow the shared framework asset authoring rules.

See `op/frontend-asset-authoring.md` before adding or changing JavaScript or CSS.

## NewWorld Rendering Flow

WebPack registration must be understood together with the ONEPIECE Framework NewWorld rendering flow.

For normal HTML output, the application endpoint is executed before the layout is rendered:

1. the Router unit resolves the endpoint
2. the App unit gets the Router-resolved endpoint from the Router unit
3. the App unit executes the endpoint through `OP()->Template()`
4. endpoint output is stored in the App unit buffer
5. the Layout unit is executed
6. the layout calls shared templates such as `layout/head.phtml`
7. the layout outputs the buffered endpoint content through `OP()->Content()`

This means WebPack registrations made inside the endpoint content are completed before `layout/head.phtml` emits the WebPack `<link>` or `<script>` tags.

Do not diagnose endpoint-owned WebPack registration as "too late for the head" without first checking this flow.

When a WebPack hash or grouped asset URL looks wrong, inspect:

- which files were registered while the endpoint was executed
- how the active WebPack unit generated the hash during layout rendering
- how the separate `/webpack/css/...` or `/webpack/js/...` delivery request reconstructs or reads the registered asset list
- whether the delivery request is registering the intended layout-owned and app-owned asset directories

The grouped asset delivery request is a separate request from the HTML page request. Its responsibility is not the same as endpoint rendering, even though both are coordinated through the WebPack unit.

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
