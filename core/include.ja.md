# include

Source folder: `asset/core/include/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`include` は startup や request setup で直接 load される core include file を定義する。

この folder は `Autoloader.php`、`Define.php`、`Error.php`、`Request.php`、`RequestWeb.php`、`RequestShell.php`、`isAdmin.php`、`isLocalhost.php` などの low-level include target を所有する。

bootstrap support、environment/request setup、通常の class usage の前後で利用可能である必要がある procedural include file にはこの folder を使う。通常の class definition、reusable trait、general helper-function library はここに置かない。

ここへの変更は startup と request behavior に影響し得るため、編集時は bootstrap と request flow を確認する。
