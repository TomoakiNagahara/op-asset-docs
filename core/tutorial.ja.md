# tutorial

Source folder: `asset/core/tutorial/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`tutorial` は core behavior を学ぶための runnable または renderable example を定義する。

この folder は `OP.phtml`、`Config.phtml`、`Request.phtml`、`Template.phtml`、`Unit.phtml`、`Module.phtml`、`Error.phtml`、`Env.phtml`、`D.phtml`、`Timestamp.phtml` などの tutorial page を所有する。

core feature の使い方を示す example にはこの folder を使う。runtime implementation、CI check、interface contract、reference documentation はここに置かない。

tutorial change は説明対象の feature file に追随するべきだが、tutorial page は normative API definition ではなく example として扱う。
