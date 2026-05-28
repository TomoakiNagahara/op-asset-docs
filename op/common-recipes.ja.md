# Common Recipes

## 目的

この文書は、AI や contributor がよくある作業を安全に進めるための task-oriented pattern をまとめます。

## 関連文書

- `for-tomoaki-nagahara.md`
- `invariants.md`
- `responsibility-boundaries.md`
- `unit-module-authoring.ja.md`

## ページを追加する

- `asset/template/` から始める
- Router がどう endpoint を解決するか確認する
- Layout が output を包むべきか確認する
- unknown route の 404 挙動が変わらないか確認する

## Layout を変える

- `asset/layout/` から始める
- その page が Layout 経由の HTML rendering か確認する
- routing logic を layout logic に持ち込まない

## local 専用 config を追加する

- shared default は `asset/config/name.php` に置く
- machine-local value は `asset/config/_name.php` に置く
- environment-specific value を template に hardcode しない

## UNIT / MODULE の default config を追加する

- package-owned default config は `asset/unit/<unit-name>/config.php` または `asset/module/<module-name>/config.php` に置く
- user-defined application config は `asset/config/<name>.php` に置く
- machine-local override は `asset/config/_<name>.php` に置く
- config layer は `array_replace_recursive()` semantics で merge する
- default config value は package の通常動作に適した値にする
- default value は config file の中だけで理解できるようにする。comment で valid value を示し、それぞれの value の runtime effect を説明して、third-party user が別 document を読んだり AI assistant に尋ねたりしなくても設定を理解できるようにする
- current `Config::Get(<name>)` は `asset/unit/<name>/config.php` を自動で読み込むが、`asset/module/<name>/config.php` は自動では読み込まない
- `asset/module/<module-name>/config.php` は template として扱い、MODULE config を runtime behavior に反映したい user が `asset/config/<module-name>.php` へ copy する

## 通常 request の memory を軽く保つ

- normal successful request path は小さく保つ
- initialization failure、recovery guidance、diagnostics、rare maintenance path、大きな optional helper のためだけに使う code は通常 request で読み込まない
- rare-path logic は focused file または class に移す
- その file または class は、本当に必要になった条件が発生した後にだけ読み込む
- framework や package file を lazy-load する時は、`OP()->Path('asset:/...')` のような public framework API を優先する

## unit を差し替える

- 対応する interface contract を特定する
- その unit に typed mapper accessor があるか確認する
- application 側で制御すべき差し替えなら `asset/config/unit.php` の mapping を使う
- 呼び出し側から見える挙動は interface contract に揃える

## typed でない unit を呼ぶ

- `OP()->Unit('UnitName')` を使う
- その unit が typed mapper method として公式に露出していない場合に使う

## repository に CI 対応を追加する

- `ci.sh` または `.ci.sh` が存在することを確認する
- 対象 class と method の CI config を用意する
- `.ci_commit_id_<branch>_php<version>` を生成できることを確認する

## なぜ CI が走らなかったか調べる

次を確認する。

- `ci.sh` / `.ci.sh` が無い
- `.ci_skip`
- `.git` が無い
- fresh で一致する `.ci_commit_id_*` があり、skip された

## なぜ push がブロックされたか調べる

次を確認する。

- CI marker file が無い
- CI marker の commit ID が一致しない
- `pre-push-prefix.php` に reject された
- CD logic の GitHub-specific branch restriction に引っかかった

## `local` remote workflow を使う

- private で高速、または offline 履歴保存が必要なときに `local` を使う
- `local` が skip するのは CI gate だけだと理解する
- prefix check はその後も続くことを忘れない

## grouped JS/CSS delivery を追加する

- JavaScript または CSS を書く前に `frontend-asset-authoring.ja.md` を読む
- file または directory の register に `op-unit-webpack` を使う
- delivery entry には `webpack` module を使う
- layout-specific asset directory を含めるべきか確認する

## error visibility を調べる

- `OP()->isAdmin()` を確認する
- `asset/config/admin.php` を確認する
- error が `OP_ERROR` で保存されているか確認する
- Notice が保存済み error を render しているか mail しているか確認する
