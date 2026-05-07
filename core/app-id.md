# App ID

## Overview

In the ONEPIECE Framework, `_APP_ID_` is defined in:

`asset/config/app_id.php`

This value has two important roles:

- it is used as part of the encryption basis
- it is used as part of the session namespace structure

## Definition

The App ID is generated from the seed value defined in:

`asset/config/app_id_seed.php`

Then it is defined as:

```php
define('_APP_ID_', $app_id);
```

## Use in Encryption

The App ID is used by the framework encryption layer.

In the current implementation, `Encrypt.class.php` uses `OP::AppID()` as the source material for:

- the initialization vector source
- the password source

unless those values are explicitly overridden by server variables.

This means the App ID is part of the encryption basis used by the framework.

## Use in Session Namespace

The App ID is also used in the framework session storage structure.

The current session reference is built in this shape:

```php
$_SESSION[_OP_NAME_SPACE_][unit-group][unit-name][_APP_ID_]
```

More precisely, the framework stores session data under a multi-level key that includes:

- framework namespace
- unit group
- unit name
- App ID

## Why This Matters

PHP session storage can be shared within the same session context, especially when applications are running under the same domain and session environment.

Because of that, simply using `$_SESSION['key']` directly is not enough to keep application data clearly separated.

The ONEPIECE Framework uses `_APP_ID_` as part of the session namespace so that application data is separated per app identity inside the framework structure.

## Important Precision

This design does **not** mean that another application can never read the raw PHP session storage.

The more precise meaning is:

- the framework's standard session access is namespaced by App ID
- framework-managed session values are less likely to collide across different applications
- application separation is enforced inside the framework session structure

So the purpose is separation and collision avoidance, not absolute physical invisibility of session data.

## Purpose

The design intention is:

- avoid session key collisions between different applications
- keep framework-managed session data separated by application identity
- use application identity as part of the encryption basis

## Summary

`_APP_ID_` is an application identity value used by the ONEPIECE Framework for:

- encryption basis
- session namespacing

It helps separate framework-managed application state, especially when multiple applications may share the same broader session environment.
