# 初期化の概要

## Entry Point

ONEPIECE Framework skeleton の通常の初期化 entry point は次です。

```text
php asset/init/submodules.php
```

file name は plural の `submodules.php` です。

## Repository And Submodule Model

ONEPIECE Framework の working tree では、ほとんどの file が広い意味での submodule package repository として install されます。

skeleton repository 自体では、application/runtime package files のほとんどを直接 track しません。main repository が主に保持するのは、初期化を bootstrap するために必要な file、特に `asset/init/` と、どの package を install するかを示す configuration です。

current skeleton setup では、skeleton root の `.gitmodules` に Git-managed submodule はありません。

ただし、end user が skeleton 側に独自の Git-managed submodule を連結する可能性はあります。そのため、initialization code には Git-managed submodule handling の path が残っています。

CORE、UNIT、MODULE、LAYOUT、bootstrap、template などの framework package areas は initialization によって install される submodule packages です。skeleton root では、これらは root `.gitmodules` ではなく `asset/config/submodule/**` で設定されます。

install された package repository 自体が、さらに Git-managed nested submodules を持つ場合があります。`asset/core/` は意図的な例です。core package は class、interface、function、trait、include、ci、testcase、tutorial、docs などを Git-managed nested submodules として持ちます。

`asset/init/update.php` は、`asset/config/submodule/**` 配下で設定された package repositories の main initializer/updater です。

## Flow

`asset/init/submodules.php` は、main repository である OP Framework skeleton の最初の initializer です。

Skeleton repository を準備し、skeleton 側に存在し得る Git-managed submodule pass を扱い、`asset/init/update.php` を include し、`update.php` から戻った後で hooks を適用します。

current file-level As-Is flow は `submodules.ja.md` を参照してください。

`asset/init/update.php` は、OP-managed package と、それらの package 内に存在する nested Git-managed submodules を含む configured package repository clone / update work を扱います。

current file-level As-Is flow は `update.ja.md` を参照してください。

configured package repository について、`update.php` は次の配下にある configuration files を読みます。

```text
asset/config/submodule/*/*.php
```

`update.php` を直接実行した場合は、`update.php` 自身が Git root を定義し、configured package repositories を処理する前に `git submodule foreach git fetch --all` と `git submodule foreach git pull` を実行します。default skeleton root では通常この Git-managed pass は空ですが、end user が root Git submodules を追加している場合には意味を持ちます。

有効な config file ごとに、`update.php` は次を呼び出します。

```php
Init($type, $name, $config)
Update($type, $name, $config, $init)
```

truthy な `skip` を持つ config file は無視されます。

## 責務分担

`submodules.php` は main skeleton repository の top-level initializer です。

skeleton initialization sequence を開始し、submodule initialization を継続するために内部で `update.php` を呼び出します。

`submodules.php` の call order と branch behavior は `submodules.ja.md` を参照してください。

`update.php` は、`asset/config/submodule/**` に記述された configured package repositories の initializer/updater であり、それらの repository 内に Git-managed nested submodules が存在する場合はそれも update します。

`update.php` は `asset/init/function/Init.php` と `asset/init/function/Update.php` を load し、有効な configuration ごとに `Init()` と `Update()` を呼び出します。

`update.php` の call order と branch behavior は `update.ja.md` を参照してください。

`Init()` は、設定された repository が存在しない場合に作成または clone します。設定された `url`、`path`、`branch` を使い、request value によって GitHub owner や clone URL scheme を変更する場合があります。また、remote の追加、元の remote の別名保持、local remote の作成、hooks の設定を行い、clone した repository 内の nested Git submodules も初期化します。対象 directory がすでに存在する場合、`Init()` は init path を実行しません。

`Update()` は、init step の後で repository を update します。`Init()` が直前に repository を clone した場合、`Update()` は fetch/pull path を意図的に実行しません。既存 repository の場合は、`Request('remote', '--all')` を fetch し、`pull=0` でない限り `git pull --rebase` で rebase します。また、`.gitmodules` が存在する場合は nested Git submodules も update します。

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

この gap は、package update responsibility が `asset/init/update.php` に集約されている一方で、skeleton repository update はその command の外に残っていることに由来します。その結果、`update.php` は framework packages の daily update command になりましたが、main skeleton repository まではまだ update しない状態になっています。

[DOC-FUTURE] 理想的には、`asset/init/update.php` だけで、skeleton repository 自体を含む working tree 全体の日常 update が完了するべきです。その model では、user が `update.php` の前後に別途 skeleton-level の `git pull` を覚えて実行する必要はありません。

## 運用上の意味

通常の初期化では、user は次を実行します。

```text
php asset/init/submodules.php
```

初期化後の日常的な package update では、`update.php` を直接実行することが有用です。現在の実装では、`update.php` は configured package repositories を処理する前に current skeleton root から到達できる既存の Git-managed submodules を refresh しますが、skeleton repository 自体は別途 update する必要があります。
