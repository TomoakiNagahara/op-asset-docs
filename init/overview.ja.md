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

## 運用上の意味

通常の初期化では、user は次を実行します。

```text
php asset/init/submodules.php
```

Git-managed submodules がすでに存在し、主に non-Git-managed submodule layer を initialize または update したい場合には、`update.php` を直接実行することが有用です。この mode でも、`update.php` は non-Git-managed config を処理する前に既存の Git-managed submodules を refresh します。
