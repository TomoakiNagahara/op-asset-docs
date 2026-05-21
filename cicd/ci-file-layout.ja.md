# CI File Layout

この document は、ONEPIECE Framework の UNIT / MODULE package に共通する CI file layout を説明します。

この rule は意図的に generic です。framework CI が検査するすべての UNIT / MODULE class に適用してください。

## Class Target

class-based CI は class file から始まります。

```text
asset/unit/<unit-name>/<ClassName>.class.php
asset/module/<module-name>/<ClassName>.class.php
```

framework CI の検査対象にする class は `OP_CI` を使います。

framework CI coverage が必要な再利用 behavior は、standalone `function.php` ではなく class file に実装してください。

## Loader File

CI loader file は class name と同じ名前にします。

```text
asset/unit/<unit-name>/ci/<ClassName>.php
asset/module/<module-name>/ci/<ClassName>.php
```

loader は CI config object を作成し、method-level CI file を include し、最後に `$ci->Get()` を返します。

loader は次の pattern を使ってください。

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

すべての method case を loader file に直接書かないでください。
loader は、同名 directory への dispatcher として保ってください。

## Method Files

検査する method ごとに、class name と同じ directory の下へ CI config file を置きます。

```text
asset/unit/<unit-name>/ci/<ClassName>/<MethodName>.php
asset/module/<module-name>/ci/<ClassName>/<MethodName>.php
```

各 method file は、自分自身の filename から method name を取得し、共有されている `$ci` object に case を登録します。

```php
$method = basename(__FILE__);
$method = explode('.', $method)[0];

/* @var $ci \OP\UNIT\CI\CI_Config */

$args   = [1];
$result = true;
$ci->Set($method, $result, $args);
```

これにより、CI case が test 対象 method の近くに保たれ、OP の file-by-file commit style とも合います。

## Existing Pattern

この layout の reference implementation として `asset/unit/html/ci/Html.php` を参照してください。

この file は class-level loader で、`asset/unit/html/ci/Html/<MethodName>.php` が method-level case を持ちます。
