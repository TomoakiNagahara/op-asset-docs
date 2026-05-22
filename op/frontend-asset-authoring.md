# Frontend Asset Authoring

## Purpose

This document defines ONEPIECE Framework rules and cautions for writing JavaScript and CSS files.

These rules apply when adding or changing frontend assets in applications, layouts, units, modules, or framework-provided asset packages.

## WebPack Grouping Model

ONEPIECE Framework WebPack is the framework-native system that groups multiple files by extension and delivers each extension group with a single request.

It is not Node.js webpack.

Because files of the same extension may be concatenated into one response, each frontend asset file must be written so it can safely share one output stream with other files.

WebPack also generates a hash for each packed extension group and includes that hash in the grouped asset URL.

The ONEPIECE Framework concept does not force whether the hash is derived from packed bytes, content values, registered file lists, or another cache identity. That choice belongs to `op-unit-webpack`.

When debugging frontend assets, use the URL hash as a clue for which packed content version is being requested and whether cached packed output is still being served.

## JavaScript Rules

JavaScript files are sensitive to grouped delivery because top-level declarations can collide when multiple files are combined.

Each WebPack-managed JavaScript file should wrap its code in a closure:

```javascript
(function(){
	'use strict';

	// File-local code goes here.
})();
```

Use this closure style for:

- page JavaScript
- layout JavaScript
- unit or module JavaScript
- framework-provided standard JavaScript assets

Only expose names on `window` or another shared object when the file intentionally provides a public API for other scripts.

When a file must expose a public API, keep the exported surface small and make the ownership obvious from the name.

## CSS Rules

CSS is usually less sensitive than JavaScript to grouped delivery, but the cascade still applies across the grouped response.

When writing CSS:

- use selectors that match the ownership boundary of the file
- avoid broad selectors when a layout, page, unit, or module scope is available
- assume the file may be delivered with other CSS files of the same extension group
- avoid relying on incidental file order unless that order is intentionally controlled by WebPack registration

## Registration Awareness

Do not assume a file is packed just because it exists in a `js` or `css` directory.

Files are included when they are explicitly registered through the WebPack flow used by the application, layout, unit, or module.

When a frontend asset does not appear in the browser, check:

- whether the file was registered
- whether the correct extension group was requested
- whether the grouped delivery request is using the intended app-owned, layout-owned, unit-owned, or module-owned directory
- whether cache or hash state is hiding the latest registration result

## Related Documents

- `../webpack.md`
- `../module/webpack.md`
- `responsibility-boundaries.md`
