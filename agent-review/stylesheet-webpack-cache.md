# Stylesheet WebPack Cache Agent Review

## Scope

This note records the investigation of a stylesheet problem where CSS changes appeared not to be reflected and WebPack looked like the failing area.

The work covered:

- checking the skeleton customization boundary before editing
- reproducing WebPack CSS output through the framework request flow
- fixing the WebPack cache hash timing in `op-unit-webpack`
- documenting the `public/` virtual-host container meaning after the user clarified it

## Trouble

Stylesheets were not reflected after recent public-site changes.

The visible symptom was around WebPack CSS delivery. The page generated a stylesheet URL such as:

- `/webpack/css/index.css?layout=<layout-name>&hash=...`

The first direct PHP built-in server test returned a 500 from `asset/module/webpack/content/css/index.php`, but that was a reproduction mistake. The server had been started without a router script, so the module entry file was executed directly before framework bootstrap. With `public/app.php` or `public/<subdomain>/app.php` as the router, WebPack output worked through the normal skeleton flow.

## Root Cause

The real issue was cache invalidation.

`OP()->Unit()->WebPack()->Hash('css')` generated the hash before directory registrations were expanded into concrete files.

That meant a directory registration such as:

- `OP()->Unit()->WebPack()->Auto('css')`

could produce a hash based on the registered directory state, not the actual files inside that directory.

When files under `css/` changed, moved, or were added, the generated hash could stay stale. Because WebPack uses that hash as the APCu cache key, the browser could keep requesting a URL that still mapped to old cached CSS.

## Fix

The fix was made in `asset/unit/webpack/WEBPACK_2024.trait.php`.

`Hash()` now expands registered directories before reading the extension session and building the hash:

```php
// Include directory contents before generating the cache key.
self::_RegisterFilesFromDirectory();
```

This makes the cache key include the actual file list that will be emitted for the requested extension.

## Verification

The following checks were run:

- `php -l asset/unit/webpack/WEBPACK_2024.trait.php`
- local framework request through `php -S ... -t public public/app.php`
- local virtual-host request through `php -S ... -t public/<subdomain> public/<subdomain>/app.php`
- same-session request for the generated WebPack CSS URL

The CSS response included:

- `public/<subdomain>/css/color.css`
- `public/<subdomain>/css/welcome.css`
- `asset/layout/<layout-name>/css/flexbox.css`

The hash changed after the fix, confirming that the concrete registered CSS files affected the cache key.

## CI Result

`./cicd` was attempted.

It did not complete because of existing repository structure issues unrelated to the WebPack patch:

- `public/<subdomain>` is present as a nested repository/submodule-like path, but the root `.gitmodules` does not contain a mapping for it.
- CI also attempted to enter `asset/core//docs`, which did not exist in this checkout.

## Documentation Follow-up

The user clarified that the app-root `public/` directory is a virtual-host container.

Normally `public_html/` is the public document root. When multiple virtual hosts are present, entries under `public/` can become app roots for individual virtual hosts, such as `public/<subdomain>/`.

This was recorded in:

- `asset/docs/CUSTOMIZATION_MAP.md`
- `asset/docs/CUSTOMIZATION_MAP.ja.md`
- `asset/docs/skeleton/public-vhost-directory.md`
- `asset/docs/skeleton/public-vhost-directory.ja.md`

## Lessons

[DOC-RISK] When reproducing WebPack or module URLs locally, run through the skeleton router entry so bootstrap has happened. Direct execution of module content files can create false 500 errors.

[DOC-RISK] Cache keys for grouped assets must be based on the concrete emitted file list, not only the pre-expanded directory registration state.

[DOC-GAP] `public/` has a special virtual-host-container meaning in multi-host skeleton setups. Agents should not treat it as a single flat public web root without checking the host mapping.

[DOC-FUTURE] WebPack cache tests should include directory registration cases where a file is added, moved, or renamed under the registered directory.
