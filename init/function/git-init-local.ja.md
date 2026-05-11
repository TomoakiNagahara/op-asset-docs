# `GitInitLocal()` の As-Is

## 概要

`asset/init/function/GitInitLocal.php` は、skeleton initialization workflow で使う local bare repository を準備します。

init request で local repository path が有効な場合:

```text
local=1
```

`GitSubmoduleRepository()` から呼び出されます。

## 責務

`GitInitLocal()` は、local bare repository setup と local remote/fetch behavior を所有します。

これにより、local repository に関係する処理を 1 つの file に集約します。

- local repository directory が存在しない場合に作成する
- その path を bare Git repository として初期化する
- current repository に `local` remote を追加する
- local bare repository が既に存在する場合に `local` remote から fetch する

local remote/fetch behavior は以前 `GitSubmoduleRepository()` にありました。
新規作成直後の bare repository から refs がない状態で fetch して失敗することを避けるため、`GitInitLocal()` に移動されました。

## current behavior

target path が存在しない場合:

1. `GitInitLocal()` は directory を作成する。
2. `git init --bare <path>` を実行する。
3. bare initialization が成功した場合、current repository の `local` remote をその path に向けて追加する。
4. 新規作成した bare repository からは fetch しない。

target path が既に存在する場合:

1. `GitInitLocal()` は `<path>/HEAD` を確認する。
2. `HEAD` がなければ `false` を返す。
3. `HEAD` があれば、current repository の `local` remote をその path に向けて追加する。
4. remote 追加に成功した場合、`local` から fetch する。

この function は、最後に実行した Git operation の結果を返します。

## caller との関係

`GitSubmoduleRepository()` は、current repository の `origin` URL から local bare repository path を組み立てます。

その path を組み立てた後、次を呼び出します。

```php
GitInitLocal($local)
```

`GitSubmoduleRepository()` は、local remote/fetch sequence を直接所有しません。
その behavior は `GitInitLocal()` に属します。

## 移動した理由

local remote/fetch sequence は、新規作成した bare repository からすぐ fetch すると失敗する可能性があるため移動されました。

local bare repository creation、local remote registration、local fetch behavior を `GitInitLocal()` にまとめることで、local repository behavior の所有者が 1 つになり、メンテナンスしやすくなります。

## compatibility note

以前の実装は、`git diff`、rebase、pick workflow をスムーズにするため、source file 内に comment として意図的に残されています。
近くを編集するときに安易に削除しないでください。

