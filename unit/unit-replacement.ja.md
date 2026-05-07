# Unit Replacement

## 概要

ONEPIECE Framework の大きな特徴のひとつは、unit を次のような統一形式で呼び出せることです。

```php
OP()->Unit()->App()
```

同時に、その実体の unit 実装は差し替え可能です。

## 関連 framework 文書

- `../op/invariants.ja.md`
- `../op/responsibility-boundaries.ja.md`
- `../op/common-recipes.ja.md`

## 基本的な考え方

重要なのは、呼び出し側は同じ高水準の access path を使い続けながら、その背後にある具体的な unit を変更できることです。

これを支えているのは次の 3 要素です。

- `asset/core/interface/` にある unit interface 群
- `asset/core/trait/OP_UNIT_MAPPER.php` にある unit mapper
- `asset/config/unit.php` にある application 側の mapping 設定

## 2 つの access style

framework には現在、2 つの unit access style があります。

- `OP()->Unit()->App()`
- `OP()->Unit('App')`

method-chain style は新しい typed style です。

string 引数 style は、interface 化されておらず、typed mapping として公式に露出していない unit を呼ぶ場合に引き続き重要です。

## 歴史的背景

古い世代では、次の書き方しかありませんでした。

```php
OP()->Unit('UnitName')
```

当時は、interface ベースの method chain はまだ導入されていませんでした。

その経緯があるため、古い repository には、現在では typed method chain でも到達できる unit であっても、古い呼び出し方が残っている場合があります。

## これが重要な理由

この設計により次が可能になります。

- 呼び出し側の記法を安定させる
- 呼び出し元をすべて書き換えずに unit 実装を差し替える
- framework-level の一貫性を保ちつつ、application-specific な置き換えを許す
- 必要な contract を満たす限り、third-party 製や fork した実装も許容する

## 意味

言い換えると、この framework は次のように設計されています。

- 呼び出し側は unit contract に依存する
- 実際の implementation は mapping によって差し替え可能である

これは、ONEPIECE Framework に unit layer の柔軟性を与えている特徴のひとつです。

## ユニークさ

差し替え可能な subsystem を持つ framework 自体は存在します。特に DI container 型や adapter 型の ecosystem では珍しくありません。

ただし、ONEPIECE Framework には次の組み合わせによる特徴があります。

- `OP()->Unit()->App()` のような統一された高水準 access path
- interface による contract 保証
- application 側 mapping による差し替え
- interface に規定されていない部分については、unit 実装側に自由度を残すこと

これは次のように表現できます。

- `interface-driven replaceable subsystem`
- `contract-based swappable unit architecture`

重要なのは、interface が必要な仕様を保証し、かつ拘束する一方で、interface に規定されていない挙動は unit 実装側が自由に設計できることです。

この「差し替え可能性」と「実装自由度」のバランスが、ONEPIECE Framework のユニークな特徴のひとつです。

## 実務上の価値

この差し替え可能な unit system は、特に次のような領域で重宝します。

- Form
- SQL
- database access

利用者側にとって重要なのは、どの unit 実装を使うかを自由に選べることです。

内部でどのような処理がされているかは unit 内に隠蔽されたままで構いませんが、利用者は contract によって拘束された統一 interface を通して利用できます。

つまり、method 名と引数は共通化されており、実装が差し替わっても、同じ interface に対して同種の結果を期待できます。

## 純正実装だけに限定しない

この柔軟性は、純正 unit だけに限定されません。

framework 全体の設計思想として、必要な役割と contract を満たしている限り、サブシステムは差し替え可能であるべきだと考えています。

実務上は次を含みます。

- 純正実装
- third-party 製実装
- fork した独自実装

repository 運用レベルでは、設定された repository URL を変更してその実装を使うことが、その具体的な実現方法のひとつです。
