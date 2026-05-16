# `OP()` の思想的背景

## 概要

`OP()` は、ONEPIECE Framework の実務で最も多用される入口のひとつです。

これは、framework の機能を単一の統一入口から呼び出せるようにするための仕組みです。

## 背景

`OP()` が生まれた背景には、実務上の記述負荷があります。

統一入口がない場合、異なる namespace 間で core 機能や unit 機能を呼び出す記述が煩雑になります。

開発者は毎回、次のことを意識しなければなりません。

- 現在の class namespace
- 呼び出し先 class namespace
- fully-qualified class name
- static / object access の書き分け

## 目的

`OP()` の目的は、framework の利用を単純で統一されたものにすることです。

`OP()` を使うことで、開発者は class namespace を毎回意識せずに framework 機能を呼び出せるようになります。

実装レベルでは、この統一 access は `\OP\OP` の singleton instance によって支えられています。

これにより、次の効果があります。

- どこからでも core 機能を使いやすい
- どこからでも unit 機能を使いやすい
- 記述を短く保ちやすい
- namespace に起因する日常的な煩わしさを減らせる

## 設計上の意味

実務上の意味として、`OP()` は framework の統一 gateway として機能します。

より具体的には、global function `OP()` は、再利用される `\OP\OP` の singleton instance を返します。

そのため、ONEPIECE Framework における開発体験の中核的な仕組みのひとつになっています。

## Namespace 解決が不要な入口

ONEPIECE Framework の重要な特徴のひとつは、`OP()` が namespace 内の code からでも意図的に呼びやすくした global function の entry point であることです。

多くの PHP framework では、namespace 内の code から framework symbol を使うために、import、fully-qualified name、service container、facade、dependency injection などで解決する必要があります。
ONEPIECE Framework の `OP()` は、それとは違う役割を持っています。`OP()` は framework へ到達するための namespace-free gateway です。

これは偶然の convenience ではありません。framework の developer ergonomics の一部です。
application、unit、module、template、framework code が、毎回 namespace access を解決し直さずに common framework features へ到達できるようにすることが目的です。

application や module の code が namespace の中にあっても、次のようにそのまま呼び出します。

```php
OP()->Request('key');
OP()->isAdmin();
OP()->Template('file.phtml');
```

namespace 解決、import、class のような fully-qualified call を機械的に追加してはいけません。

次は誤りです。

```php
\OP\OP();
use function OP\OP;
```

`\OP\OP` という名前は、global function の背後で使われる framework class です。
呼び出す function name ではありません。

実装を見ても、この違いは確認できます。`asset/core/function/OP.php` は namespace 宣言を持たず、`function OP()` を定義しています。

## エージェント向け注意

[DOC-RISK] namespace の修正は機械的に行ってはいけません。

framework API の呼び出しに `\`、`use function`、その他の import を追加する前に、その symbol の実際の宣言と framework contract を確認してください。

`OP()` の contract は次の通りです。

- `OP()` が呼び出す global function である。
- `\OP\OP` はその function から返される class instance である。
- `OP\OP()` は有効な function call ではない。
- `use function OP\OP;` はこの用途では誤りである。

この rule が防ぐ具体的な失敗と AI agent のミス記録は、`asset/docs/agent-mistake/op-function.md` を参照してください。
