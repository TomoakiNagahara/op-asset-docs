# `Request()` の As-Is

## 概要

`asset/init/function/Request.php` は、skeleton の初期化 script 群で使われる current の request 値 loader です。

たとえば次の file から使われます。

- `asset/init/update.php`
- `asset/init/function/Init.php`
- `asset/init/function/GitSubmoduleRepository.php`

## current の読込順

current 実装では、request 値は次の順で組み立てられます。

1. `asset/config/init.php`
2. `asset/config/_init.php`
3. `key=value` 形式の CLI 引数

後段の層が前段を上書きします。

## local override の挙動

`asset/config/_init.php` が存在する場合、`asset/config/init.php` の結果に対して次で merge されます。

- `array_replace_recursive()`

つまり `_init.php` は、init-time configuration の local override layer として機能します。

## CLI override の挙動

config 読込の後、`$_SERVER['argv']` から CLI 引数が解析されます。

引数が次の形式で:

```text
key=value
```

current の validation rule に一致すれば、config 由来の値を上書きします。

したがって current の As-Is 優先順位は次です。

- base config
- local override config
- CLI override

## 運用上の意味

これにより、初期化 layer では次を両立できます。

- `init.php` に shared default を置く
- `_init.php` に local machine override を置く
- command line で一時的な one-shot override を与える

しかも、init script 自体の source code を書き換える必要はありません。
