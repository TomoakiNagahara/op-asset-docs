# testcase

Source folder: `asset/core/testcase/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`testcase` は runnable core test case を定義する。

この folder は `AppID.php`、`Cookie.php`、`EMail.php`、`Encrypt.php`、`Error.php`、`Request.php`、`RootPath.php`、`Session.php`、`Time.php`、`Timestamp.php` など、core behavior の test file を所有する。

core file に対する executable または inspectable test coverage にはこの folder を使う。runtime implementation、tutorial page、package documentation はここに置かない。

core class、function、include、trait を変更する場合、対応する testcase は同じ file-level commit unit に入りやすい companion file である。
