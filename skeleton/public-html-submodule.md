# Public HTML Submodule

## Overview

One canonical way to use the ONEPIECE Framework skeleton is through the `public_html` submodule configuration.

The relevant config is:

- `asset/config/submodule/public_html/www.php`

## Current Default

The current file returns:

- `skip => 1`

That means the `public_html` repository is skipped by default.

## Enabling Clone

If the developer changes:

- `skip => 1`

to:

- `skip => 0`

then the `public_html` repository becomes eligible to be cloned.

In other words, changing `skip` from `1` to `0` is the switch that enables the `public_html` layer.

Even if this config change is made later, running:

```sh
php asset/init/update.php
```

causes the skeleton to scan `asset/config/submodule/*/*.php` again. If `public_html` is no longer skipped, it becomes a clone target at that time.

## Using Your Own Repository

If the `url` is changed to the developer's own repository, then the developer can:

- clone `public_html`
- edit files under `public_html`
- push those changes freely to their own repository

This means the developer can fully own and manage everything under `public_html`.

## Operational Meaning

This is considered a canonical and orthodox way to use the ONEPIECE Framework skeleton.

In practical terms:

- the framework/skeleton side remains reusable
- the public HTML side can be owned and developed by the application developer
- the developer can manage their own public-facing files in their own repository
- the `public_html` layer can be edited and pushed independently from the reusable skeleton side

## Why `public_html` Is Useful

If `public_html` is used as the document root, then `asset/` can stay outside the document root.

That means access to `asset/` can be blocked in a simple structural way, rather than only by runtime behavior or web server rules.

## Why It Is Not Forced

The skeleton does not force `public_html` to be the standard document root.

The reasons are:

- the application should still be installable under a subdirectory, not only at the document root
- some hosting or deployment environments do not allow the document root to be chosen freely
- the skeleton keeps this flexibility on purpose

## Summary

The basic pattern is:

1. change `skip` from `1` to `0`
2. change `url` to your own repository if needed
3. run `php asset/init/update.php`
4. clone and manage `public_html` as your own editable and pushable layer

This is a canonical skeleton usage pattern.
