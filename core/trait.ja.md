# trait

Source folder: `asset/core/trait/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`trait` は core class に mix in される shared implementation を定義する。

この folder は `OP_CORE.php`、`OP_ENV.php`、`OP_ERROR.php`、`OP_FUNCTION.php`、`OP_SESSION.php`、`OP_TEMPLATE.php`、`OP_UNIT.php`、`OP_UNIT_MAPPER.php` などの trait file を所有する。

class 間で core behavior を構成する reusable method group にはこの folder を使う。standalone global function、complete class definition、interface contract、test case はここに置かない。

trait change は複数 class に影響し得るため、trait-specific behavior は trait file name ごとに documentation 化し、その trait を consume する class を確認する。
