# UNIT と MODULE の境界

## 目的

この document は、ONEPIECE Framework における UNIT と MODULE の実用上の違いを説明します。

目的は、AI と contributor が新しい behavior を `asset/unit/`、`asset/module/`、または別の場所のどこに置くべきか判断しやすくすることです。

これは framework-level の責務 guide であり、特定 package ひとつの implementation detail ではありません。

## 短い判断基準

UNIT は、再利用される内部 capability です。

MODULE は、独立した feature package または request-facing feature です。

framework、application、他の unit、module、template、layout から横断的に呼ばれることを想定する behavior は UNIT にします。

明確な目的、entry point、または user-facing な役割を持つ、比較的 self-contained な feature は MODULE にします。

もうひとつの実用的な違いは、call direction です。

UNIT は、`OP()->Unit()` のような access path を通じて、多くの namespace から簡単に呼ばれることを想定します。

MODULE は、通常、namespace をまたいで共有 API として呼ばれ続ける設計ではありません。focused feature、endpoint、adapter として呼び出されることが多いものです。

## UNIT

UNIT は、汎用的な functional unit です。

完成した user feature というより、system behavior に近いものです。

典型的な UNIT の責務:

- 再利用可能な framework または application capability を提供する
- 他の code が依存できる API または contract を公開する
- unit、module、template、layout、application code など複数の caller を支える
- shared state、変換、validation、routing、output generation、orchestration を管理する
- unit contract が許す場合に framework mapping による差し替えや設定対象になる

UNIT 的な責務の例:

- application flow management
- routing
- layout rendering control
- validation
- form handling
- ORM / database access
- notice handling
- WebPack asset registration と grouped output generation
- CI / CD orchestration

多くの他 feature が呼び出す必要があるために有用な feature なら、おそらく UNIT です。

UNIT package の簡潔な一覧と、agent 向けの DATABASE / FORM / ORM 利用 rule は `../unit/available-units.ja.md` を参照します。

## 呼び出し方と開発者側の重心

UNIT は、framework や CORE developer 側に近い位置にあります。

これは CORE developer だけが unit を書けるという意味ではありません。ただし、UNIT は通常、framework-like な期待を背負います。

- 多くの caller が依存する可能性がある
- 異なる namespace から頻繁に呼び出される可能性がある
- `OP()->Unit()` または generic unit access path の下に置かれる可能性がある
- interface または stable contract を公開する可能性がある
- 他の unit、module、template、layout、application code が使う shared vocabulary の一部になる可能性がある

そのため、UNIT の変更は、self-contained な feature ひとつを変更する場合より影響範囲が広くなりやすいです。

database access、form handling、validation、routing、layout control などは UNIT 的です。他の framework part がそれらを呼び出すことを期待しており、それぞれの内部実装を各所が所有するべきではないためです。

MODULE は、end-user または application developer 側に近い位置にあります。

これは module が end user 専用という意味ではありません。ただし、MODULE は通常、より feature-oriented な重心を持ちます。

- application がその feature を必要とするため install または enable される
- focused entry point または request-facing role を持つことが多い
- 内部で複数の unit を組み合わせる場合がある
- 他の package は通常、その内部実装に依存するべきではない
- 設計を明確に変更しない限り hidden shared system layer になるべきではない

短く言えば、UNIT は shared mechanism です。MODULE は、その mechanism を使う feature package です。

## MODULE

MODULE は、独立した feature package です。

shared internal capability というより、完成した feature に近いものです。

典型的な MODULE の責務:

- 特定の feature purpose を所有する
- request-facing または feature-facing な entry point を公開する
- 内部で 1 つ以上の unit を組み合わせる
- 他の module から比較的独立している
- 明確な設計意図がない限り shared system layer にならない

MODULE 的な責務の例:

- access counter
- contact form
- sitemap delivery
- RSS delivery
- focused admin feature
- WebPack delivery endpoint

それ自体が complete feature または endpoint として有用な feature なら、おそらく MODULE です。

## UNIT と MODULE の関係

MODULE が UNIT を使うのは自然です。

例えば contact form module は、次を使うかもしれません。

通常の dependency direction は MODULE から UNIT です。

UNIT が MODULE の内部実装に依存する場合は注意が必要です。feature package が偶然 framework layer になってしまうためです。

- validation unit
- form unit
- mail-related unit
- template または layout path
- notice や session 関連の framework behavior

complete feature が contact form であるため、contact form は MODULE のままです。

その内部で使われる再利用可能な mechanics は UNIT の責務です。

## WebPack の例

WebPack は境界が分かりやすい例です。

`op-unit-webpack` は UNIT 側です。

所有するもの:

- asset registration state
- cache / minify / output behavior
- grouped output generation

`op-module-webpack` は MODULE 側です。

所有するもの:

- delivery-side request entry
- request-to-asset-type handling
- WebPack unit への handoff

module は external request を受けます。

unit は再利用可能な mechanism を所有します。

## 判断チェックリスト

UNIT を選ぶ場合:

- 他の unit や module がその behavior を呼ぶべきである
- behavior が再利用可能な internal capability である
- `OP()->Unit()` または generic unit access を通じて、異なる namespace から頻繁に到達されることを想定している
- 安定した API または contract が重要である
- mapping、replacement、framework-level configuration の対象になり得る
- complete feature というより infrastructure-like な behavior である

MODULE を選ぶ場合:

- behavior が complete feature である
- 明確な request-facing または user-facing entry point がある
- feature を独立して install、enable、disable、理解できる
- shared lower layer になるより、既存 unit を組み合わせることが主な役割である
- 他の feature が通常その内部に依存すべきではない
- framework-wide shared mechanism というより、application-facing package に近い behavior である

両方に見える場合は、まず「他の code が何に依存すべきか」を確認します。

多くの feature がその behavior に依存すべきなら、再利用部分を UNIT にします。

その behavior が主に 1 つの feature として価値を持つなら MODULE にし、再利用可能な mechanics は既存または新規の UNIT に置きます。

## Anti-Patterns

呼びやすいという理由だけで application-specific behavior を既存 framework UNIT に入れてはいけません。

設計が明確に変わっていない限り、MODULE を hidden shared system layer にしてはいけません。

UNIT にすべき reusable mechanics を複数の module に重複させてはいけません。

MODULE の方が境界を明確に保てる request-facing feature ownership を UNIT に移してはいけません。

## Documentation Placement

UNIT system の philosophy、background、history は `asset/docs/unit/` に置きます。

MODULE system の philosophy、background、history は `asset/docs/module/` に置きます。

UNIT と MODULE の framework-wide な responsibility boundary は `asset/docs/op/` に置きます。

特定 unit または module の implementation detail は、その package 自身の `docs/` directory に置きます。
