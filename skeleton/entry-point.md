# Application Entry Point

## Overview

In the ONEPIECE Framework, `app.php` is always the application entry point.

The application is executed through `app.php`, not by running individual page files directly as the main application flow.

## Role of `app.php`

`app.php` is responsible for:

- setting application startup constants
- setting `APP_ROOT`
- loading the bootstrap process
- initializing the framework
- handing control to the app unit

The bootstrap layer under `asset/bootstrap/` should also be understood carefully.

It exists in the stage before OP-CORE is fully available.

That means bootstrap-side files are expected to be self-contained startup code rather than ordinary reusable framework/application logic.

After bootstrap, the application proceeds through:

```php
OP()->Unit()->App()->Auto();
```

In normal startup terms, this is the point where the startup process ends and application-side control is handed over to the App unit.

In that sense, the purpose of `app.php` can be described very narrowly:

- initialize the framework
- make the framework ready to use
- delegate all further handling to the app unit

## Role of the App Unit

After `app.php` finishes framework initialization, the app unit takes over the rest of the handling.

That means the app unit is responsible for the full application-side flow after initialization, including overall request handling inside the framework lifecycle.

## Web Server Responsibility

Execution of `app.php` is controlled by web server configuration.

The web server must be configured so that requests are dispatched to `app.php`.

Examples include:

- rewrite rules
- document routing rules
- built-in server startup settings

## Meaning of This Design

This means the framework expects a single application entry flow.

The request should first enter `app.php`, and the framework should then handle:

- bootstrap
- routing
- template dispatch
- application lifecycle control

More precisely:

- `app.php` performs initialization
- the app unit handles the rest of the application flow

If bootstrap files are missing because submodules are not initialized, `app.php` falls back to guidance output instead of entering the normal framework runtime.

## Bootstrap Boundary

`asset/bootstrap/` is a startup-side boundary.

Two practical rules follow from that:

1. bootstrap templates and bootstrap-side files are not intended as reusable templates for general application use
2. bootstrap code should not assume normal OP-CORE feature availability unless that availability has already been established inside bootstrap itself

This is because bootstrap belongs to the execution stage before the framework is fully up.

## Why Initialization and Execution Are Separated

Separating framework initialization from framework execution makes repository responsibilities clearer.

In practice, this separation helps clarify the role of:

- the main repository (`Skeleton`)
- submodule repositories

This also has an important Git workflow benefit.

By separating initialization responsibility from application handling responsibility, the project can reduce:

- unnecessary conflicts
- mixed-purpose commits
- noisy history across the main repository and submodules

As a result, commit history becomes easier to keep concise and easier to understand.

## Summary

The rule is simple:

- `app.php` is always the entry point of the application
- `app.php` mainly exists to initialize the framework
- after initialization, control is delegated to the app unit
- the web server configuration is responsible for executing `app.php`
- this separation also helps keep repository responsibilities and Git history clean
