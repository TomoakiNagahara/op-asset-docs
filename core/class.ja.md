# class

Source folder: `asset/core/class/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`class` は OP が runtime で使う named core class を定義する。

この folder は `OP.class.php`、`Config.class.php`、`Env.class.php`、`Error.class.php`、`Session.class.php`、`Unit.class.php` などの class file を所有する。

object-level core behavior と runtime state management にはこの folder を使う。global helper function、bootstrap include file、interface contract、trait-only shared implementation はここに置かない。

1 つの class file に依存する behavior を documentation 化する場合、無関係な class details を shared document にまとめず、class file-specific document を作成する。
