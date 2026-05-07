# Meta Path

## 概要

ONEPIECE Framework の大きな特徴のひとつに、**meta path** の仕組みがあります。

framework では、すべての path を生の local filesystem path として書くのではなく、名前付き root を使って表現できます。

代表例は次です。

- `doc:/foo/bar/index.php`
- `app:/hoge/`
- `op:/asset/`

これらの label により、path を単なる絶対位置ではなく、役割や意味を持った root から表現できます。

この label 集合は built-in の例だけに限定されません。

必要に応じて、開発者やエンドユーザーが独自の meta path label を定義しても構いません。

## 主な meta path の例

### `doc:/`

`doc:/` は document root からの path を意味します。

例:

```text
doc:/foo/bar/index.php
```

### `app:/`

`app:/` は、現在の application が配置され実行されている directory を基点にした path を意味します。

例:

```text
app:/hoge/
```

これは特に便利です。

application は document root 直下の固定位置に設置されている必要がありません。

framework が application root を把握していれば、document root 配下のどこに application が展開されていても、同じ meta path 記法を使えます。

そのため、application の移設や deployment 構造の違いに強くなります。

### `op:/`

`op:/` は ONEPIECE Framework 全体のトップディレクトリからの path を意味します。

例:

```text
op:/asset/
```

これは、framework 全体の resource、tooling、shared asset を安定した基点から参照したい場合に便利です。

### `git:/`

`git:/` も、2030 世代の移行期間中は引き続き利用できます。

これは互換性のためです。

## 歴史的経緯

現在の convenience method である `OP()->Path()` は、2030 版から導入されました。

導入理由は実務的に次の 3 つです。

- 従来の path 処理は煩雑だった
- interface を統一したかった
- 常に `OP()` から一貫して access できるようにしたかった

ただし、meta path という考え方自体はそれ以前から存在しています。

次の関数は、すでに 2020 版から存在しています。

- `RootPath()`
  meta path の root を登録する
- `ConvertPath()`
  meta path から full path へ変換する
- `CompressPath()`
  full path から meta path へ変換する

つまり、2030 で新しくなったのは meta path そのものではありません。

2030 では、それらをまとめて扱いやすくする unified な入口として `OP()->Path()` が加わりました。

application 利用のレベルでは、これが public face です。

その意味で、`OP()->Path()` は、従来からある meta path mechanics をまとめた wrapper と理解できます。

下位の関数群も重要ではありますが、それを知っておくべきなのは主に AI であり、人間の利用者は通常それらを直接意識する必要はありません。

それらは unified entry point の背後にある内部 mechanics として扱われます。

## `op:/` の歴史的意味

初版の ONEPIECE Framework では、`OP:/` は `op-core` directory 自体を指していました。

2020 世代からは、`op-core` は `core:/` になりました。

時代が進むにつれ、古い `OP:/` の意味に依存する code は無くなりました。

そのため現在は、`op:/` を framework 全体のトップディレクトリとして扱う意図になっています。

新たに追加された `_ROOT_OP_` 定数は、従来の `_ROOT_GIT_` という命名を置き換えるためのものです。

2030 世代では、移行期間中は `git:/` と `op:/` の両方が利用できます。

これは、ONEPIECE Framework が互換性を非常に重視するためです。

現在も code 中には `git:/` が大量に残っています。

今後は徐々に `op:/` へ置き換えていきますが、これからの推奨名は `op:/` です。

## なぜ meta path が重要か

meta path の仕組みは、framework を次のように保つ助けになります。

- simple
- readable
- portable
- hard-coded な local filesystem path に依存しにくい

これは、明示的で理解しやすい挙動を好む ONEPIECE Framework の設計思想にも合っています。

例えば `app:/config/database.php` という記述は、何を基点にしている path なのかを即座に伝えます。

また project ごとに独自の path vocabulary を導入しつつ、統一された meta path model を保てる点も利点です。

## まとめ

meta path は、ONEPIECE Framework の中核的な convenience feature のひとつです。

これにより code は、

- document root
- application root
- framework root

を raw な絶対 path に依存せずに参照できます。
