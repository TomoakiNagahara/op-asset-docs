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

request lifecycle の詳細は `asset/docs/skeleton/runtime-lifecycle.ja.md` と `asset/docs/CUSTOMIZATION_MAP.ja.md` を読んでください。

## ONEPIECE Framework Rules

- pass-through behavior は `asset/docs/new-world/html-pass-through.ja.md` を読む。
- template placement は `asset/docs/skeleton/template-directory.ja.md` を読む。
- entry-point behavior は `asset/docs/skeleton/entry-point.ja.md` を読む。
- framework-wide design intent は `asset/docs/op/` 配下の document を読む。

## Coding Rules

- public-name spelling check、method closing comment、singleton local static variable、config readability、debugging を含む framework-wide coding rule は `asset/docs/op/coding-rules.ja.md` を読む。
- documentation は clear and concise に保つ。
- normal request path の memory を軽く保つ。詳細は `asset/docs/op/design-philosophy.ja.md` と `asset/docs/op/common-recipes.ja.md` を参照する。
- CDN、proxy、cloud、vendor の IP range など、外部で管理される変動 data を黙って hardcode しない。公開情報であっても時間とともに変わり、更新負荷と運用リスクを生むため、そのような logic を追加する前に user に確認するか、application code の外で保守される既存の trusted source を使う。
- JavaScript または CSS を追加・変更する前に `asset/docs/op/frontend-asset-authoring.ja.md` に従う。WebPack-managed JavaScript file では file-local code を closure の中に閉じ込める。
- raw PHP superglobal より framework API を優先する。
- UNIT / MODULE の作成または再構成では、namespace placement、CI visibility、lazy loading、path rule を含めて `asset/docs/op/unit-module-authoring.ja.md` を読む。
- 明示的に必要でない限り、raw `$_GET`、`$_POST`、`$_REQUEST`、`$_COOKIE`、`$_SESSION`、`$_SERVER` を使わない。
- 適切な場合は `OP()->Request()` を使う。

## Documentation Rules

- documentation を作成、移動、再構成する前に `asset/docs/documentation-authoring.ja.md` を読む。
- user が保存すべき口頭または chat の指示を出した場合は、original text を `dictation.ja.md` など責任範囲に対応する language-specific dictation file に記録し、agent-facing guidance は `dictation.md` に置く。framework、UNIT、MODULE のどこがその note を所有するかは `asset/docs/documentation-authoring.ja.md` で判断する。
- `AGENTS.md` は薄い導線として保つ。framework-wide、CORE、UNIT、MODULE、CI/CD、documentation 固有の rule は、それぞれ責任を持つ document に置き、ここには必要な参照だけを置く。

## Git / CI Rules

- push 前に project CI を実行する。
- 利用可能な場合は `cicd` command を優先する。
- 明示的に依頼されていない限り、Git hook を bypass しない。
- UNIT / MODULE class CI rule は `asset/docs/op/unit-module-authoring.ja.md` と `asset/docs/cicd/ci-file-layout.ja.md` を読む。
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
