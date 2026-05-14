# ci

Source folder: `asset/core/ci/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`ci` は core class と trait に対する CI-facing entry point を定義する。

この folder は `OP`、`Config`、`Cookie`、`Env`、`Error`、`Session`、`Unit`、`Unit_Mapper` などの core behavior に対する executable CI check target を保持する。

core class や trait に file-level CI check が必要な場合にこの folder を使う。通常の runtime implementation はここに置かない。runtime class definition は `asset/core/class/`、shared trait implementation は `asset/core/trait/`、standalone helper function は `asset/core/function/` に置く。

core feature file を変更する場合、対応する `ci` file は同じ commit unit に入りやすい companion file の一つである。
