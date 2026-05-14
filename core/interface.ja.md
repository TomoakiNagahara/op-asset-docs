# interface

Source folder: `asset/core/interface/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`interface` は OP unit、module、framework service、external integration の core contract を定義する。

この folder は `IF_UNIT.php`、`IF_ROUTER.php`、`IF_LAYOUT.php`、`IF_HTML.php`、`IF_DATABASE.php`、`IF_SQL.php`、`IF_CI.php`、`IF_SELFTEST.php`、`IF_WEBPACK.php` などの `IF_*.php` interface file を所有する。

framework component の public method contract を定義または変更する必要がある場合にこの folder を使う。implementation、runtime state、test case、tutorial example はここに置かない。

interface change は unit や module の implementer に影響する high-impact change なので、interface ごとに documentation と commit を分ける。
