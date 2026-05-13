# 初期化の概要

## Entry Point

ONEPIECE Framework skeleton の通常の初期化 entry point は次です。

```text
php asset/init/submodules.php
```

file name は plural の `submodules.php` です。

## 2 種類の submodule

初期化 workflow は、repository-managed part として次の 2 種類を扱います。

1. Git-managed Git submodules
2. `asset/config/submodule/**` で記述される non-Git-managed submodules

ONEPIECE Framework skeleton は、もともと Git-managed Git submodules だけを使っていました。
その後、Git の `.gitmodules` mechanism で管理されない submodule も扱えるようになりました。

`asset/init/update.php` は、その 2 つ目の category のために存在します。

## Flow

`asset/init/submodules.php` は、まず Git-managed Git submodules を初期化します。

たとえば次のような処理を行います。

- Git hooks を設定する
- `.gitmodules` に対して GitHub owner replacement を適用する
- optional remote を追加する
- `GitSubmoduleForeach()` により、recursive Git submodule update、branch checkout、hook setup、nested submodule handling を行う

その後、`asset/init/submodules.php` は次を include します。

```php
asset/init/update.php
```

`asset/init/update.php` は、次の配下にある non-Git-managed submodule configuration files を扱います。

```text
asset/config/submodule/*/*.php
```

`update.php` を直接実行した場合は、`update.php` 自身が Git root を定義し、non-Git-managed submodule config を処理する前に `git submodule foreach git fetch --all` と `git submodule foreach git pull` を実行します。

有効な config file ごとに、`update.php` は次を呼び出します。

```php
Init($type, $name, $config)
Update($type, $name, $config, $init)
```

truthy な `skip` を持つ config file は無視されます。

## 責務分担

`submodules.php` は top-level initializer です。

全体の initialization sequence を担当し、内部で `update.php` を呼び出します。

`update.php` は non-Git-managed submodules の initializer/updater です。

`Init()` は、設定された repository が存在しない場合に作成または clone します。設定された `url`、`path`、`branch` を使い、request value によって GitHub owner や clone URL scheme を変更する場合があります。また、hooks を設定し、clone した repository 内の nested Git submodules も初期化します。

`Update()` は、init step の後で repository を update します。`Init()` が直前に repository を clone した場合、`Update()` は fetch/pull path を意図的に実行しません。既存 repository の場合は、`Request('remote', '--all')` を fetch し、`pull=0` でない限り configured remote と branch から pull します。また、`.gitmodules` が存在する場合は nested Git submodules も update します。

`Dir()` は config type を target directory に変換します。`public_html` は Git root、`asset` は `asset/`、その他の type は `asset/<type>` に対応します。

## Optional local / SSH remotes

`GitSubmoduleRepository()` は、request で `local=1` または `ssh=1` が有効な場合に optional remote を追加できます。

`local=1` の場合、`dir` value は local filesystem path です。Git は local remote path の `~` を展開しないため、`GitSubmoduleRepository()` は path を `GitInitLocal()` に渡す前に、先頭の `~` を local user の home directory に展開します。

`ssh=1` の場合、`dir` value は `host:~/repo/...` のような SSH remote path の一部です。local PHP process は remote host の home directory を知ることができません。そのため、先頭の `~` は保持し、Git/SSH が remote host 側で解決します。

## 現在の update gap

[DOC-GAP] 現在の `update.php` 実装は、初期化後の日常的な update には有用ですが、skeleton repository 自体は update しません。

`asset/init/update.php` を直接実行した場合に update する対象は次です。

- `git submodule foreach` による既存の Git-managed Git submodules
- `asset/config/submodule/*/*.php` に記述された repositories
- それらの repository 内に `.gitmodules` がある場合の nested Git submodules

skeleton の Git root 自体では、`git fetch` や `git pull` は実行されません。

この gap は、skeleton がもともと Git-managed Git submodules だけを使っていたことに由来します。non-Git-managed submodule layer は後から追加され、その後から追加された layer の update 責務が `asset/init/update.php` に置かれました。その結果、`update.php` は framework packages の daily update command になりましたが、main skeleton repository まではまだ update しない状態になっています。

[DOC-FUTURE] 理想的には、`asset/init/update.php` だけで、skeleton repository 自体を含む working tree 全体の日常 update が完了するべきです。その model では、user が `update.php` の前後に別途 skeleton-level の `git pull` を覚えて実行する必要はありません。

## 運用上の意味

通常の初期化では、user は次を実行します。

```text
php asset/init/submodules.php
```

初期化後の日常的な package update では、`update.php` を直接実行することが有用です。現在の実装では、`update.php` は non-Git-managed config を処理する前に既存の Git-managed submodules を refresh しますが、skeleton repository 自体は別途 update する必要があります。
