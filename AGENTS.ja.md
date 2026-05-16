# AGENTS.md

このファイルは、この repository を編集する AI coding agent のためのメイン作業ガイドです。

repository root の `AGENTS.md` は、意図的に `asset/docs/AGENTS.md` への symlink です。管理する実体は `asset/docs/` に置き、root の symlink は agent discovery 用の entry point として維持します。

文書の役割: 境界判断の詳細は `asset/docs/CUSTOMIZATION_MAP.md` に置きます。`AGENTS.md` は AI agent のメイン導線と一般作業ルールです。`asset/docs/CODEX.md` は Codex CLI 固有の説明だけに限定します。

## Project

この repository は ONEPIECE Framework の application skeleton です。

ONEPIECE Framework は Apache-2.0 license の OSS project です。

- `README.md` は、この project の概要と開始方法を説明します。
- `AGENTS.md` は AI coding agent のための root symlink entry point です。内容は `asset/docs/AGENTS.md` で管理します。
- `asset/docs/CUSTOMIZATION_MAP.md` は、安全な customization point と framework-managed core zone の source of truth です。
- `asset/docs/CODEX.md` は Codex CLI-specific workflow note だけを含みます。一般的な AI-agent policy は `AGENTS.md` に置きます。
- 一般的な framework 説明よりも、この repository 固有の作業ルールを優先します。

## Customization Boundaries

編集場所を決める前に `asset/docs/CUSTOMIZATION_MAP.md` を読んでください。

customization boundary、safe change pattern、framework-core ownership rule をこのファイルに重複して書かないでください。agent が参照する authoritative boundary map を 1 つに保つため、それらの詳細は `asset/docs/CUSTOMIZATION_MAP.md` に置きます。

`asset/docs/CUSTOMIZATION_MAP.md` は次の判断に使います。

- CORE と application-owned customization の判断。
- config、routing、template、layout、unit、module の安全な配置場所。
- app-specific behavior を dedicated UNIT または MODULE にするべきかどうか。
- framework bug を責任 package への pull request にするべきかどうか。
- `app.php`、`.htaccess`、bootstrap、init、submodule config など high-impact file の扱い。

## Runtime Flow

request flow は次のように考えてください。

1. Apache rewrite または direct entry により request が `app.php` に送られる。
2. `app.php` が `APP_ROOT` を設定し、存在する場合は `asset/bootstrap/index.php` を読み込む。
3. Bootstrap が core と config file を読み込み、その後 supporting bootstrap include を読み込む。
4. `OP()->Unit()->App()->Auto()` が application lifecycle を進める。
5. 最終的に framework routing と template API を通して template が選ばれ、render される。

## ONEPIECE Framework Rules

- HTML file は framework を pass-through できる。
- `.html` file 内の PHP code は実行される場合がある。
- `.html` file にも layout rendering が適用される場合がある。
- Directory-level `index.php` は controller として動作する。
- template には `.phtml` を使う。
- routing decision と template rendering concern を分離する。
- `index.php` の fallback startup logic を維持する。

## Coding Rules

- code comment は English で書く。
- documentation は clear and concise に保つ。
- raw PHP superglobal より framework API を優先する。
- 明示的に必要でない限り、raw `$_GET`、`$_POST`、`$_REQUEST`、`$_COOKIE`、`$_SESSION`、`$_SERVER` を使わない。
- 適切な場合は `OP()->Request()` を使う。
- debugging に `var_dump()` や `print_r()` を使わない。
- framework-aware debug output には `D()` を使う。

## Documentation Rules

- README.md は human 向け。
- AGENTS.md は AI coding agent 向けで、repository root では `asset/docs/AGENTS.md` への symlink として公開する。
- CUSTOMIZATION_MAP.md は safe customization point と framework ownership boundary を説明する。実体は `asset/docs/CUSTOMIZATION_MAP.md` として管理する。
- CODEX.md は Codex CLI-specific workflow note だけを含む。実体は `asset/docs/CODEX.md` として管理する。
- English document は AI consumption の canonical working document とする。
- user が日本語で document correctness を確認し、translation を使って English document が正確か検証するため、日本語訳も必要です。
- 日本語訳を追加する場合は、English file の隣に置き、`.ja.md` suffix を使う。
- 新しい translation の default location として `asset/docs/ja/`、`asset/core`、`asset/unit`、`asset/module` 配下の `docs/ja/`、または `asset/docs/spec/` を使わない。
- OP では class、trait、function などの feature が個別 file に分離されており、Git commit も通常は multi-file feature bundle 単位ではなく file 単位で行う。
- この file-by-file commit style は、OP が非常に loosely coupled であるため成立している。documentation もこの model に合わせる。
- 分離された 1 file に依存する documentation は、その file name の document を作成し、詳細を shared document にまとめない。
- file-level commit は rebase や cherry-pick の conflict を大きく減らすため、分離 file の documentation は file name ごとに分けて管理する。
- feature file を追加、変更、削除する場合、その feature file と同時に commit されやすい別 file は related CI test と same-named documentation である。documentation file name を class file や function file と対応させることで、commit unit を明確に保つ。
- current As-Is behavior を document 化する場合、authoritative detail は、その behavior を決めている code owner の近くに置く。たとえば responsible class、trait、function、unit、module の docs に置く。
- 同じ As-Is implementation detail を、責務の異なる複数 document に重複して書かない。owner 以外の document は、詳細をコピーせず owner document に link または参照する。
- documentation path は、最初にその話題が見つかった page ではなく、code owner と responsibility boundary から選ぶ。
- intended specification、current As-Is behavior、curated gap index は分離して保つ。gap index は詳細な owner にならず、As-Is owner を指す。
- web-server-related document には `asset/docs/httpd/` を使う。
- framework-level CI/CD philosophy、history、background、operating-model document には `asset/docs/cicd/` を使う。
- Unit system 自体の philosophy、history、background には `asset/docs/unit/` を使う。
- Module system 自体の philosophy、history、background には `asset/docs/module/` を使う。
- NEW WORLD philosophy、background、historical document には `asset/docs/new-world/` を使う。
- op-core philosophy、background、high-level core feature document には `asset/docs/core/` を使う。
- framework-wide philosophy、design intent、ONEPIECE Framework の background には `asset/docs/op/` を使う。
- skeleton-specific framework document には `asset/docs/skeleton/` を使う。
- framework-level document が上記のどれにも当てはまらない場合、`asset/docs/` 直下に置く。
- repository document に `/System/Volumes/...` のような local absolute file link を入れない。
- repository document では、clickable local-environment file link ではなく、plain repository-relative path を優先する。
- document が current problem、mismatch、risk、limitation、future fix direction を説明する場合、`[DOC-ISSUE]`、`[DOC-RISK]`、`[DOC-GAP]`、`[DOC-FUTURE]`、`[DOC-PRIORITY1]` などの searchable tag を付ける。
- specification がすでに明確だが current implementation が明らかに異なる場合は `[DOC-PRIORITY1]` を使う。

## Git / CI Rules

- push 前に project CI を実行する。
- 利用可能な場合は `cicd` command を優先する。
- 明示的に依頼されていない限り、Git hook を bypass しない。
- commit message には、次のような approved prefix を使う:
  - `New:`
  - `Add:`
  - `Chg:`
  - `Fix:`
  - `Doc:`
  - `Del:`

## Verification

変更後、変更の risk と scope に合う check を実行してください。

behavior change では、`asset/docs/CUSTOMIZATION_MAP.md` にある startup、route、template、layout、unknown-route behavior のうち関連するものを確認します。

docs-only change では、通常 diff review で十分です。

## Preferred Workflow

1. 既存 structure を確認する。
2. 編集場所を決める前に `asset/docs/CUSTOMIZATION_MAP.md` を読む。
3. 最小で安全な変更を特定する。
4. 適切な customization area の file だけを変更する。
5. 利用可能かつ関連する check を実行する。
6. 何をなぜ変更したか説明する。

## Troubleshooting

application startup、routing、rendering、layout、404 behavior が間違っている場合は、上記の runtime flow と `asset/docs/CUSTOMIZATION_MAP.md` の responsibility map を使って、最初に確認する file を選びます。

## Glossary

- `pass-through`
  HTML-oriented file が framework を通過しながら、PHP execution も可能にする design。
- `UNIT`
  framework または application behavior を整理するための functional unit。
- `Template`
  page content の rendering に選択される file。
- `Layout`
  shared page wrapper と common presentation structure。
- `Auto()`
  bootstrap 完了後に実行される application lifecycle step。
