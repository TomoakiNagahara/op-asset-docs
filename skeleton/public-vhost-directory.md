# Public Virtual Host Directory

## Overview

The app-root `public/` directory is used when one skeleton contains multiple virtual hosts.

The usual single-site public document root is `public_html/` when that layer is enabled. In a multi-host setup, `public/` becomes a container for virtual-host app roots.

## Meaning of `public/`

Do not assume `public/` is only a flat static public directory.

When multiple virtual hosts are configured, a child directory under `public/` can be the app root for one virtual host. That child may contain the entry files, web-server-facing files, local public assets, and configuration needed for that host.

For example:

- `public/<subdomain>/`
- `public/<site-name>/`

Each of these can be mapped by the web server as the app root for a different virtual host.

## Relationship With `public_html/`

`public_html/` remains the normal public document-root layer for a single public site when that submodule is enabled.

`public/` is used when the skeleton needs to hold more than one virtual-host app root.

## Editing Rule

Before editing anything under `public/`, first identify which level owns the change:

- `public/` itself: virtual-host container structure
- `public/<host>/`: one virtual host's app root
- `public/<host>/css`, `public/<host>/img`, or similar: public assets for that specific virtual host

Do not move behavior between virtual hosts unless the task explicitly changes shared hosting structure.

## Runtime Meaning

The virtual-host web-server configuration decides which `public/<host>/` directory becomes the active app root for a request.

Once a request reaches that virtual host app root, the normal skeleton startup flow still applies: the host entry loads `app.php`, `APP_ROOT` is set from that app root, bootstrap runs, and the application lifecycle continues.
