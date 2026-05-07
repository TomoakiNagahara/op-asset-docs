# Apache `.htaccess`

## Overview

In the ONEPIECE Framework, Apache request handling is controlled by `.htaccess`.

This file determines whether a request is passed into the framework through `app.php`, or whether it is served directly as a static file.

## Current Behavior

The current behavior is as follows.

### 1. Requesting a non-existent file

If a request points to a path that does not physically exist, the request is passed to `app.php`.

From there, the framework continues to the endpoint flow and the corresponding `index.php` is executed.

In other words:

- non-existent file path
- `app.php`
- endpoint `index.php`
- framework-controlled response

### 2. Requesting an existing file that is not explicitly routed

If a request points to a file that physically exists and `.htaccess` does not explicitly route that extension into the framework, Apache serves it as a static file.

In that case, the ONEPIECE Framework does not participate in the response.

In other words:

- existing file
- static delivery
- no framework involvement

### 3. Requesting a file whose extension is explicitly routed by `.htaccess`

If `.htaccess` explicitly matches the file extension, the request is passed to `app.php` even when the file physically exists.

From there, the framework continues to the endpoint flow and the corresponding `index.php` is executed.

This is not limited to images.

It applies to the extensions configured in `.htaccess`.

## Meaning of This Design

This design separates static delivery from framework-controlled execution.

That means:

- static files can still be served directly when appropriate
- missing resources can be handled dynamically by the framework
- selected file types can be forced through the framework flow

## Summary

The current Apache `.htaccess` behavior can be summarized like this:

- non-existent file -> `app.php` -> endpoint `index.php`
- existing file without an explicitly routed extension -> static delivery
- file extensions explicitly routed by `.htaccess` -> `app.php` -> endpoint `index.php`
