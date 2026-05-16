# Invariants

## 目的

この文書は、現行 framework-level の不変条件を定義します。これらは不用意に変えてはいけません。

ここに書かれるのは単なる implementation detail ではありません。

task が明示的に contract-level change を要求していない限り、AI や contributor が保持すべき安定契約です。

## 関連文書

- `for-tomoaki-nagahara.md`
- `responsibility-boundaries.md`
- `unit-module-boundary.md`
- `common-recipes.md`

## エントリーポイントと起動

- `app.php` は application entry point である
- Web サーバーは application 実行を `app.php` に渡す前提である
- `app.php` は framework を初期化し、その後 app unit に制御を渡す
- 通常の request flow は `OP()->Unit()->App()->Auto()` を通って進む

## 起動責務の分離

- `app.php` は framework startup のためのものであり、application 固有の page logic を持つ場所ではない
- startup 後の request handling は app unit とその下流 unit に属する
- startup と application execution の責務分離は明確なまま維持する

## request lifecycle

- Router -> App -> Layout という NEW WORLD の流れは core execution model である
- routing、template execution、buffered content handling、layout output は責務ごとに分かれているべきである
- Layout は最初の実行段階ではなく、後段の rendering stage である

## layout と non-layout の挙動

- layout 実行は動的に制御可能でなければならない
- non-HTML response は layout rendering を回避できなければならない
- これは JS、CSS、JSON、image-like response などを動的に扱うための framework design の一部である

## ドキュメント配置の契約

- Web server 関連文書は `asset/docs/httpd/` に置く
- framework-level な CI/CD の思想、背景、歴史は `asset/docs/cicd/` に置く
- UNIT system 自体の思想、背景、歴史は `asset/docs/unit/` に置く
- Module system 自体の思想、背景、歴史は `asset/docs/module/` に置く
- NEW WORLD の思想、背景、歴史は `asset/docs/new-world/` に置く
- op-core の思想、背景、および高レベルな core feature 文書は `asset/docs/core/` に置く
- framework 全体の思想、設計意図、背景は `asset/docs/op/` に置く
- skeleton 固有の framework 文書は `asset/docs/skeleton/` に置く
- 上記のどれにも当てはまらない framework-level 文書は `asset/docs/` 直下に置いてよい
- framework-level 文書の日本語訳は、英語版と同じ directory に置き、末尾を `.ja.md` にする
- As-Is、技術実装、unit 内部フロー、カプセル化された subsystem の挙動は各 subsystem 自身の `docs/` に置く
- 文書の内容は、path から推測される責務範囲と一致していなければならない
- `asset/docs/module/<name>.md` は、その module system 上の package の目的、責務境界、framework-level な位置づけを説明する
- 特定 module の current behavior、entry point、内部 call flow、現行 implementation detail は、その module package 自身の `docs/` に置く
- framework 全体の概念を、特定 unit/module/package の文書に押し込んではならない
- 複数 package にまたがる概念は、より上位の framework-level 文書に置き、個別 package 文書から参照する

## CI/CD 統合

- Git hook は framework workflow の一部であり、optional な飾りではない
- `git push` は通常、共有公開前に CI 状態と照合されるべきである
- CI marker file は現行 push control model の一部である
- `local` remote 例外は運用上の例外であり、すべての push rule を除去するものではない

## rolling update の契約

- yearly branch model は framework operating model の一部である
- `_OP_APP_BRANCH_` と branch naming は揃っていることが期待される
- rolling update compatibility は framework の責任である

## unit access と差し替え

- `OP()->Unit('Name')` は generic access path として残る
- `OP()->Unit()->App()` 形式の typed access は mapper 対応 unit に限られる
- interface ベースの contract と mapping ベースの差し替えは framework design の一部である

## namespace contract

- ONEPIECE Framework の namespace rule は file path から決まるものではない
- framework の functions と classes は、global function の `OP()` と `D()` を除き、すべて `OP` namespace の下に置く
- Core 機能は `OP` 直下の namespace を使う
- Unit classes は `OP\UNIT` の下に置く
- Module classes は `OP\MODULE` の下に置く
- directory structure だけから namespace placement を推測してはいけない

## config override

- shared configuration は `name.php` に置く
- local-only override は `_name.php` に置く
- layered config の読込順は expected operating model の一部である

## error handling model

- framework は bootstrap 後に現実的に到達可能なエラーをすべて収集するべきである
- 保存されたエラーは後段で framework notice path によって処理される
- admin だけ画面表示し、non-admin には mail 通知する構造は現行 operating model の一部である

## admin 判定の役割

- `OP()->isAdmin()` は大きな environment decision point である
- debug visibility、notice rendering、developer-facing behavior に影響する
- localhost shortcut は将来 configurable になる可能性があっても、現行設計の一部である
