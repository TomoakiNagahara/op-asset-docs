# `update.php` As-Is

## Overview

`asset/init/update.php` は configured submodule repositories を initialize / update します。

次の 2 つの mode で動作します。

- `asset/init/submodules.php` から include される
- `php asset/init/update.php` として直接実行される

`submodules.php` から include される場合、`_ROOT_GIT_` と `_OP_APP_BRANCH_` はすでに利用可能であることが期待されます。

直接実行される場合、`update.php` は `_ROOT_GIT_` を define し、`git submodule foreach` により既存の Git-managed submodules を refresh し、`_OP_APP_BRANCH_` が未定義なら `asset/config/op.php` を load します。

## Execution Flow

1. `_ROOT_GIT_` が存在することを保証する。
2. 直接実行されている場合、既存の Git-managed submodules を refresh する。
3. 必要に応じて `asset/config/op.php` を include し、`_OP_APP_BRANCH_` が存在することを保証する。
4. init helper functions を load する。
5. `Request('indicator', '1')` で `indicator` を読む。
6. `asset/config/submodule/*/*.php` を走査する。
7. config file path から `$type` と `$name` を導出する。
8. config file を include する。
9. truthy な `skip` を持つ entry を skip する。
10. `Init($type, $name, $config)` を呼ぶ。
11. `Update($type, $name, $config, $init)` を呼ぶ。
12. enabled の場合、progress indicator を表示する。

## Direct Execution Branch

`_ROOT_GIT_` が define されていない場合、`update.php` は直接実行されたものとして扱います。

この branch では次を行います。

- `git rev-parse --show-toplevel` から `_ROOT_GIT_` を define する
- `git submodule foreach git fetch --all` を実行する
- `git submodule foreach git pull` を実行する

これにより、configured repositories を処理する前に既存の Git-managed submodules を refresh します。

## Config Loop

config loop は次を scan します。

```text
asset/config/submodule/*/*.php
```

parent directory name が `$type` になり、`.php` を除いた file basename が `$name` になります。

各 config file は、`Init()` と `Update()` が使う repository settings を返します。

## Called Functions

`Request()` は config と CLI arguments から initialization options を読みます。

詳細は `function/request.ja.md` を参照してください。

`Init()` は、missing configured repository を create / clone し、clone 後に hooks と remotes を設定し、初期化を行ったかどうかを返します。

詳細は `function/init.ja.md` を参照してください。

`Update()` は、`Init()` の後で existing configured repository を update します。

詳細は `function/update.ja.md` を参照してください。

`Dir()` は `update.php` 内で定義されます。config type を target directory に変換します。

- `public_html` は `_ROOT_GIT_` に対応する
- `asset` は `_ROOT_GIT_/asset/` に対応する
- その他の type は `_ROOT_GIT_/asset/<type>` に対応する

## Scope

`update.php` は skeleton repository 自体を fetch / pull しません。

current update gap は `overview.ja.md` を参照してください。
