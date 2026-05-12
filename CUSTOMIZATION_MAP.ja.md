# 安全なカスタマイズ領域マップ

この文書は、`op-skeleton-2030` におけるアプリケーション側の安全な編集領域と、framework 管理の CORE 領域を分けるための判断基準です。

文書の役割: 境界判断の詳細は `CUSTOMIZATION_MAP.md` に置きます。`AGENTS.md` は AI agent のメイン導線と一般作業ルールです。`CODEX.md` は Codex CLI 固有の説明だけに限定します。

まずアプリケーション側の編集領域から検討してください。framework の挙動そのものを変える必要がある場合だけ、CORE 側に進みます。

CORE 開発者として作業していない場合、CORE 領域を安易に修正してはいけません。
アプリケーション固有の機能を CORE や既存の framework UNIT に追加してはいけません。
OP アプリケーションを独自に拡張したい場合は、そのアプリケーション専用の UNIT または MODULE を作成します。
framework の不具合を発見した場合は、アプリケーション側に隠して修正するのではなく、責任を持つ package に対して pull request を出します。

## 境界の要約

| 領域 | 所有者 | 安全な用途 | 原則避けること |
| --- | --- | --- | --- |
| `asset/config/*.php` | Application skeleton | 共有するアプリケーション既定値、機能フラグ、layout 選択、HTML metadata、unit mapping、webpack 設定。 | machine-local な secret や環境固有値の直書き。 |
| `asset/config/_*.php` | Local developer または deployment | `Config::Get()` により `name.php` の後に読み込まれる machine-local override。`.gitignore` の対象。 | local credential、admin IP、hostname、machine-local path の commit。 |
| `index.php` | Application skeleton | このアプリケーションの top-level route-to-template 判断。 | fallback の `app.php` handoff の削除、bootstrap 関心事との混在。 |
| `asset/layout/<site-layout>/` | Application または layout package | site framing、header、footer、menu、共通 page structure。project-owned design には site-specific layout を作る。 | framework-provided layout package を local app code のように編集すること。 |
| `asset/layout/<layout>/template/` | Layout-specific templates | 特定 layout の shared template override。`asset/template/` より先に探索される。 | 共通 layout markup を page template に重複させること。 |
| `asset/template/` | Framework default template package | skeleton/default template と generic fallback page。 | developer-specific または end-user-specific page template を置くこと。ただし所有境界の tradeoff を意図している場合を除く。 |
| New page templates | Application | site design に属する project-specific page は active layout の template 領域へ追加する。 | 単純な page 追加のために core routing や bootstrap を変更すること。 |
| `asset/unit/<unit>/` | Unit package | その unit package 自体を変更する場合、または dedicated project-owned unit を作る場合。 | application-specific 機能を既存 framework unit に追加すること。app-level config、route、view 変更のために unit を直接編集すること。 |
| `asset/module/<module>/` | Module package | その module package 自体を変更する場合、または dedicated project-owned module を作る場合。 | application-specific behavior を shared framework module に入れること。 |
| `asset/core/` | Framework core | CORE fix、CORE feature、upstream framework change。 | application behavior、project design、local environment config、one-off routing change。 |
| `asset/bootstrap/` | Framework bootstrap package | `app.php`、bootstrap include order、pre-core assumption を確認したうえでの startup contract 変更。 | 通常の app behavior、template、layout、routing、feature config。 |
| `app.php` | High-impact entry point | application launch contract が変わる場合だけ。 | bootstrap bypass、`APP_ROOT` の安易な変更、`OP()->Unit()->App()->Auto()` の置換。 |
| `.htaccess` | Web server routing contract | URL dispatch behavior を変える必要がある場合。 | template や page の変更。 |
| `asset/init/` | Skeleton initialization tooling | submodule setup、update、local initialization helper。 | runtime application behavior。 |
| `asset/config/submodule/**` | Skeleton dependency map | initialization が取得する submodule repository/path の設定。 | application runtime feature flag として扱うこと。 |

## 判断ルール

1. 設定変更なら、まず `asset/config/` から始める。
2. 1 台の machine または deployment に閉じる値なら、`asset/config/name.php` ではなく `asset/config/_name.php` を使う。
3. page output の変更なら、実際に route から使われる template を特定してから編集する。
4. shared page framing の変更なら、active layout under `asset/layout/` を優先する。
5. route selection の変更なら、`index.php`、`.htaccess`、router behavior を一緒に確認する。
6. `asset/core/`、`asset/bootstrap/`、`asset/unit/`、`asset/module/` が必要に見える場合、config、layout、template、project-owned unit、project-owned module で解決できないか先に確認する。
7. application-specific feature なら、CORE や既存 framework UNIT には追加しない。dedicated UNIT または MODULE を作る。
8. framework bug fix なら、責任を持つ framework package に pull request を出す。

## `asset/webpack/` の扱い

`asset/webpack/js/` と `asset/webpack/css/` は、framework が用意する standard function / standard style の置き場です。

これらの file は、存在するだけでは自動的に pack されません。

application や layout は、必要に応じて参考にしても、使っても、無視しても構いません。

推奨する使い方は、active layout の `js` / `css` directory から必要な file へ symbolic link を貼り、layout 側の asset として明示的に WebPack 登録することです。

詳細は `webpack.ja.md` を参照してください。

## CORE と既存 UNIT を触らない基準

CORE 開発者でない作業者にとって、`asset/core/` は基本的に read-mostly reference です。
アプリケーション固有の機能は CORE に属しません。
呼び出しやすいという理由だけで、既存の framework UNIT に application-specific behavior を入れてはいけません。

アプリケーション固有の拡張が必要な場合は、専用 UNIT または MODULE を作り、configuration または通常の extension point を通して接続します。

framework bug を見つけた場合は、次を行います。

1. どの package がその behavior を所有しているか特定する。
2. skeleton 側の変更と package 側の変更を分ける。
3. 責任を持つ package に pull request を出す。
4. 影響する startup、route、template の確認を行う。

## Common Tasks

| Task | Start here | Do not start here |
| --- | --- | --- |
| app title を変える | `asset/config/app.php` または local `_app.php` | `asset/core/` |
| active layout を変える | `asset/config/layout.php` | `asset/bootstrap/` |
| local admin IP を追加する | `asset/config/_admin.php` | committed shared config |
| page を追加する | `index.php` と責任を持つ template path | `.htaccess` または `asset/core/` |
| header/footer/menu を変える | active layout template directory | すべての page template |
| route parsing rule を変える | config と `index.php` を確認したうえで Router unit | page template |
| app-specific behavior を追加する | dedicated project UNIT または MODULE | CORE または既存 framework UNIT |
| framework bug を修正する | 責任を持つ framework package への pull request | CORE 内の hidden app-local workaround |

## Review Checklist

framework-managed area を編集する前に、次を確認します。

- `asset/config/` または local `_*.php` override で対応できないか。
- page output、shared layout、route selection、package behavior、startup behavior のどれか。
- 自分はこの変更について CORE 開発者として作業しているのか。それとも CORE は触らないべきか。
- application-specific behavior であり、dedicated UNIT または MODULE にすべきではないか。
- framework bug であり、pull request として修正すべきではないか。
- `php asset/init/update.php` 後にも変更が維持されるか。submodule drift を作らないか。
- 対象 file は skeleton repo の tracked file か、submodule/package 側の file か。
- `AGENTS.md`、`CUSTOMIZATION_MAP.md`、または package-level `docs/` に対応する文書更新が必要か。
