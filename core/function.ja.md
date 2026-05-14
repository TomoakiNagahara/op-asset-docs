# function

Source folder: `asset/core/function/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`function` は OP の standalone global helper function を定義する。

この folder は `OP.php`、`D.php`、`RootPath.php`、`Layout.php`、`Mail.php`、`Time.php`、`Timestamp.php`、path conversion helper、encoding helper、MIME helper などの function file を所有する。

class instance なしで呼び出すことを意図した small function-level behavior にはこの folder を使う。class state、trait composition、request include file、interface contract はここに置かない。

1 つの helper function を documentation 化する場合、function、test、documentation が 1 つの commit unit として動けるように、その function file name と対応させて管理する。
