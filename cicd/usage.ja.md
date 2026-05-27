# CI/CD の使い方

## 概要

ONEPIECE Framework では、framework root に実行形式の `cicd` file があります。

そのため、CI/CD は次だけで起動できます。

```sh
./cicd
```

これは標準の統合 workflow ですが、必須の universal requirement ではありません。

開発者やエンドユーザーはこれを採用できますが、third-party 製や独自開発の CI/CD system を選んでも構いません。

ONEPIECE における CI/CD の意図した概念と用語境界については、`concept.ja.md` を参照してください。

## 標準実行

通常の使い方は次です。

```sh
./cicd
```

これは、現在 commit 済みの repository 状態に対して CI を実行します。

[DOC-GAP] current の `cicd` 実装は、CI 成功後かつ dry-run でない場合に CD へ進むことがあります。これは、CI が application 全体の合格を返した後である場合にだけ正当です。full CI run が完了する前に CD が始まるなら、その挙動は異常であり、`concept.ja.md` に書かれた flow invariant に違反します。

## よく使うオプション

operator 向けによく使う option には次があります。

- `ci=1` または `ci=0`
- `cd=1` または `cd=0`
- `unit=core`
- `unit=<unit-name>`

## unit 単位の実行

次を指定した場合:

```sh
./cicd unit=core
```

または:

```sh
./cicd unit=<unit-name>
```

current 実装では、自動的に dry-run 挙動になります。

つまり、unit を絞った実行は暗黙的に `test=1` と同等の扱いになります。

実務上は次の意味になります。

- `unit=core` は op-core だけをテストしたい時の指定
- `unit=<unit-name>` は指定した unit だけをテストしたい時の指定
- どちらも自動的に dry-run style の実行になる

## class 単位の実行

unit の中の特定 class だけをテストしたい場合は、次を使えます。

```sh
./cicd unit=app class=App
```

これは `op-unit-app` の `App` class だけをテストします。

## method 単位の実行

その class の特定 method だけをテストしたい場合は、次を使えます。

```sh
./cicd unit=app class=App method=Title
```

これは `App` class の `Title` method だけをテストします。

## CI File Layout

UNIT / MODULE の class CI file は、`ci-file-layout.md` に書かれた分割 CI file layout を使ってください。

要点は次の通りです。

- `<ClassName>.class.php` が class target です。
- `ci/<ClassName>.php` が CI loader です。
- `ci/<ClassName>/<MethodName>.php` が method-level case を持ちます。

## 未コミット変更が一時的に消える理由

CI 実行前に、`op-unit-ci` は repository 群に対して自動的に `git stash save` を行います。

そのため、未コミットの変更は、CI 実行中は working tree から一時的に消えます。

この挙動は、標準の CI flow が local の dirty working tree ではなく、commit 済みの状態を検査するためです。

## 未コミット変更をテストしたい場合

たとえば CI failure の原因を調べるために、未コミットの code もそのままテストしたい場合は、次を使えます。

```sh
./cicd test=1
```

または:

```sh
./cicd dry-run=1
```

この mode では次のようになります。

- 未コミットの変更は見えたままでテストできる
- 実行は dry-run として扱われる
- CI 通過済み commit ID の marker は保存されない

この dry-run 挙動は、`unit=...` を使った場合にも自動的に適用されます。

## 運用上の意味

実務上の違いは次です。

- `./cicd`
  commit 済みの状態を検査し、CI 通過済み commit ID を記録する
- `./cicd test=1`
  調査のために current working tree をそのまま検査するが、CI 通過済み commit ID は記録しない

これにより、標準 CI は厳密さを保ちつつ、開発者には現実的な調査経路も残されています。
