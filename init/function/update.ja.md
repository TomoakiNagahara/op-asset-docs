# `Update()` As-Is

## Overview

`asset/init/function/Update.php` は、`Init()` の後で configured repositories を update します。

`asset/init/update.php` から load され、有効な submodule configuration ごとに次の形で呼び出されます。

```php
Update($type, $name, $config, $init)
```

## Existing Repository Path

`Update()` は次から repository directory を解決します。

- `Dir($type)`
- `$config['path'] ?? $name`

directory が存在しない場合、`Update()` は update work を行わずに return します。

## Init Result

`$init` argument は `Init()` の戻り値です。

`$init` が truthy の場合、`Update()` は fetch/pull path を実行しません。これは、`Init()` が clone した直後の repository をすぐに update しないためです。

`$init` が false の場合、`Update()` は repository が既存であると扱い、update path を実行します。

## Fetch And Rebase

既存 repository に対して、`Update()` は次を実行します。

```text
git fetch <target>
```

target は次から取得されます。

```php
Request('remote', '--all')
```

`pull` が有効な場合、続けて次を実行します。

```text
git pull --rebase <remote> <branch>
```

remote と branch は submodule config から取得され、default は `origin` と `_OP_APP_BRANCH_` です。

## Nested Git Submodules

repository に `.gitmodules` がある場合、`Update()` は `GitSubmoduleConfig()` を通じて読み込み、そこに記述された nested submodules を update します。

nested submodule path ごとに、requested target を fetch し、configured remote と branch を pull します。
