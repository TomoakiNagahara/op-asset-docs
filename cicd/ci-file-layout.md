# CI File Layout

This document describes the reusable CI file layout for ONEPIECE Framework UNIT and MODULE packages.

The rule is intentionally generic. Apply it to every UNIT or MODULE class that is inspected by the framework CI.

## Class Target

Class-based CI starts from a class file:

```text
asset/unit/<unit-name>/<ClassName>.class.php
asset/module/<module-name>/<ClassName>.class.php
```

The class should use `OP_CI` when it is intended to be inspected by the framework CI.

Reusable behavior that needs framework CI coverage should be implemented in a class file, not in a standalone `function.php` file.

## Loader File

The CI loader file uses the same class name:

```text
asset/unit/<unit-name>/ci/<ClassName>.php
asset/module/<module-name>/ci/<ClassName>.php
```

The loader is responsible for creating the CI config object, including method-level CI files, and returning `$ci->Get()`.

Use this loader pattern:

```php
/* @var $ci \OP\UNIT\CI\CI_Config */
$ci = OP()->Unit()->CI()->Config();

$name = basename(__FILE__);
$name = explode('.', $name)[0];
foreach( glob(__DIR__."/{$name}/*.php") as $path ){
	require_once($path);
}

return $ci->Get();
```

Do not put all method cases directly in the loader file.
Keep the loader as a dispatcher to the same-name directory.

## Method Files

Each inspected method has its own CI config file under a directory named after the class:

```text
asset/unit/<unit-name>/ci/<ClassName>/<MethodName>.php
asset/module/<module-name>/ci/<ClassName>/<MethodName>.php
```

Each method file derives the method name from its own filename and registers cases on the shared `$ci` object:

```php
$method = basename(__FILE__);
$method = explode('.', $method)[0];

/* @var $ci \OP\UNIT\CI\CI_Config */

$args   = [1];
$result = true;
$ci->Set($method, $result, $args);
```

This keeps CI cases close to the method they test and preserves OP's file-by-file commit style.

## Existing Pattern

Use `asset/unit/html/ci/Html.php` as the reference implementation for this layout.

That file is the class-level loader, and `asset/unit/html/ci/Html/<MethodName>.php` files contain the method-level cases.
