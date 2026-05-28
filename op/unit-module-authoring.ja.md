# UNIT/MODULE Authoring

## 目的

この document は、ONEPIECE Framework の UNIT / MODULE package を作成または再構成する時の authoring rule を記録します。

実装上の作法に焦点を置いています。
behavior を UNIT と MODULE のどちらに置くかの判断は `asset/docs/op/unit-module-boundary.ja.md` を参照してください。
public-name spelling check など framework-wide な coding rule は `asset/docs/op/coding-rules.ja.md` を参照してください。

## 必ず読む文書

UNIT / MODULE を coding する前に、次の documents を読んでください。

- `asset/docs/op/unit-module-authoring.md`
- `asset/docs/op/coding-rules.md`
- `asset/docs/cicd/ci-file-layout.md`
- `asset/docs/op/common-recipes.md`
- `asset/docs/op/design-philosophy.md`

作業前には、責任範囲に対応する dictation files も読んでください。
user dictation は話者の言語ごとに分け、例えば `asset/docs/dictation.ja.md` や `asset/docs/dictation.en.md` に保存し、agent-facing guidance は `asset/docs/dictation.md` にまとめます。

## 全数検査の原則

UNIT / MODULE package の code は、CI による全数検査を前提に設計します。

再利用可能な behavior を追加する時は、framework CI collector が検査しない directory や filename pattern に置いて CI から隠してはいけません。

原則:

- reusable behavior は class-based CI に参加できる class file に置く
- UNIT / MODULE package 内の visible な `*.class.php` file は class CI contract に従う
- CI target になる class は `OP_CI` を使う
- deterministic behavior は `CI_AllMethods()` に列挙する
- 検査対象 method には `ci/<ClassName>/<MethodName>.php` の method-level CI config を置く

memory-conscious design と full inspection は対立しません。

optional code を未走査 directory に隠して CI を避けてはいけません。
通常 request の memory を軽くするには、必要な時だけ lazy-load します。一方で、その code が package behavior を所有するなら、CI-visible な class として保持します。

例えば optional な MODULE feature は次のようにします。

- `OptionalFeature.class.php` を module root に置き、CI が検査できるようにする
- request entry file から、その optional feature が要求された時だけ読み込む
- `ci/OptionalFeature.php` と `ci/OptionalFeature/<MethodName>.php` で検査する

## CI file layout

`asset/docs/cicd/ci-file-layout.ja.md` に書かれた split CI file layout を使います。

class file と CI file は name を揃えます。

```text
asset/unit/<unit-name>/<ClassName>.class.php
asset/unit/<unit-name>/ci/<ClassName>.php
asset/unit/<unit-name>/ci/<ClassName>/<MethodName>.php

asset/module/<module-name>/<ClassName>.class.php
asset/module/<module-name>/ci/<ClassName>.php
asset/module/<module-name>/ci/<ClassName>/<MethodName>.php
```

behavior を所有する class で、空の `CI_AllMethods()` を final design として扱ってはいけません。
一時的な bridge にはできますが、望ましい最終状態は stable deterministic method を検査することです。

## namespace placement

UNIT と MODULE の main class は `OP\UNIT` または `OP\MODULE` に公開されます。

helper class や sub class は、他 package と衝突しないように package subnamespace に隔離します。

例:

- main module class: `OP\MODULE\Counter`
- module helper class: `OP\MODULE\COUNTER\Calendar`
- main unit class: `OP\UNIT\Html`
- unit helper class: `OP\UNIT\HTML\SomeHelper`

## runtime loading

通常の成功 request path は小さく保ちます。

optional、diagnostic、recovery、maintenance、error-only な class は、無関係な成功 request で読み込まないようにします。

runtime memory saving には lazy loading を使います。

```php
require_once __DIR__ . '/OptionalFeature.class.php';
```

同じ package 内の固定 file には `__DIR__` を使います。
public framework path API は、path abstraction が本当に必要な場合だけ使います。

## implementation discipline

UNIT / MODULE code は、小さく、直接的で、memory-conscious に保ちます。

- 無駄な class や method を増やさない。
- 分けられるという理由だけで method を分けない。
- 一箇所でしか呼ばれない method は、実際の複雑さを減らす、rare-path loading を隔離する、または既存 package pattern に合う場合を除き、分ける必要はありません。
- entry file は薄く保ち、initialize、responsible class の load、minimum package behavior の呼び出しに留めます。
- optional、diagnostic、recovery、maintenance、error-only code は normal request memory に載せません。
- 戻り値は caller が必要とする最小限にします。caller が success / failure だけ必要なら、array を組み立てて返してはいけません。
- raw PHP global ではなく framework API を使います。session state には raw `$_SESSION` ではなく `OP()->Session()` を使います。

## path API

end-user、application、UNIT、MODULE の code では、`_ROOT_ASSET_`、`_ROOT_APP_`、`_ROOT_CORE_` のような framework-internal root constant を使いません。

これらの constant は framework internal 用です。
end-user code や package code が依存すると、core 側で将来 deprecate または置換したい時の影響が大きくなります。

path abstraction が必要な場合は public meta-path API を使います。

- environment-dependent な local file path には `OP()->Path('asset:/...')`
- public URL には `OP()->URL('app:/...')`
- template include には `OP()->Template('asset:/...')`

同じ repository かつ同じ package 内の固定位置にある file を読み込む場合は、framework path abstraction は不要なので `__DIR__ . '/file.php'` を優先します。

## documentation

class file を追加した場合、その behavior の説明が必要なら package docs に same-named documentation を追加または更新します。

例:

- `Calendar.class.php`
- `docs/Calendar.class.md`
- `docs/Calendar.class.ja.md`

optional code を lazy-load する理由を document 化してください。同時に、その class が package behavior を所有する場合、lazy loading によって CI visibility を失わせてはいけないことも document 化してください。

詳細 rule を `AGENTS.md` に詰め込んではいけません。
`AGENTS.md` は薄い routing document として保ち、詳細 rule はこの file、`asset/docs/op/coding-rules.md`、`asset/docs/cicd/ci-file-layout.md` など、責任を持つ document に置きます。
