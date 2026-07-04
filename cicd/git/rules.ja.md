# Git運用ルールの仕様

## 概要

ONEPIECE Framework では、Git の運用ルールのいくつかを hook と関連処理によって制御しています。

これらのルールは、commit message、CI/CD の状態、repository 間の整合性を保つためのものです。

変更種別を表す commit message prefix の意味と使い分けは、`asset/docs/git/commit-message-prefix.ja.md` を参照してください。

## rebase のルール

### コミットメッセージ prefix のチェック

`git rebase` の際には、`pre-rebase` hook によりコミットメッセージの先頭 prefix が検査されます。

許可されていない prefix の場合は、rebase は中止されます。

### prefix のホワイトリスト

prefix の基本設定は、次のファイルにあります。

`asset/config/git-rebase-rules.php`

このファイルに、許可される commit message prefix がホワイトリスト形式で定義されています。

### config 以外で特別に許可される prefix

設定ファイルの内容に加えて、現在の branch 名と同じ prefix は自動的に許可されます。

たとえば、現在の branch 名が `2030` なら、次の prefix は自動的に許可されます。

```text
2030:
```

### Year prefix について

Year の prefix は、hook が動的に「今年以下」を計算して許可しているわけではありません。

実際には、`asset/config/git-rebase-rules.php` に明示的に列挙されている Year prefix が許可されています。

つまり、実装上の挙動は次の通りです。

- config に列挙された Year prefix を許可
- 現在の branch 名 prefix を自動許可

## push のルール

### コミットメッセージ prefix のチェック

`git push` の際にも、push 対象 commit のコミットメッセージ先頭 prefix が検査されます。

許可されていない prefix が含まれている場合は、push は拒否されます。

この prefix 判定も、同じく次の設定を使います。

`asset/config/git-rebase-rules.php`

また、現在の branch 名 prefix も自動的に許可されます。

### CI 通過チェック

`git push` の前には、hook により `ci.sh` または `.ci.sh` が実行されます。

このスクリプトの目的は、その branch が CI を通過しているかどうかを確認することです。

CI を通過していない場合は、push は拒否されます。

判定は次の比較で行います。

- 現在の branch の commit ID
- CI 通過済みとして保存されている commit ID

### `local` remote の例外

remote 名が `local` の場合は、CI チェックはスキップされます。

そのため、CI を通過していなくても `local` への push は可能です。

ただし、commit message prefix のチェック自体は引き続き適用されます。

### `local` remote で CI をスキップする理由

この `local` remote 例外は、実務上のワークフロー上の理由で存在します。

1. GitHub への push は遅い
   数秒程度の差でも、local repository への push の方がかなり速く済みます。
2. インターネットに繋がっていなくても local push なら履歴を保存できる
   常にオフィスや自宅などの安定した online 環境で作業しているとは限らないためです。
3. お試しコミットを扱いやすくする
   WIP、開発途中の test code、中間状態の commit を private な local repository に気軽に push し、その後で整理してから shared / public repository へ push できます。

## GitHub への push に対する追加制限

hook の prefix チェックや CI チェックとは別に、GitHub へ push できる branch 名にも制限があります。

その制御は、次のファイルにあります。

`asset/unit/cd/function/isCanPushToGithub.php`

### GitHub へ push できる branch

GitHub への push は、次のいずれかを満たす場合に許可されます。

- branch 名が `_OP_APP_BRANCH_` と一致する
- branch 名が `2025`, `2026`, `2030`, `2031` のような Year 形式である
- CD 設定で明示的に許可されている

実務上、追加の branch 名を許可したい場合の標準的な手順は次です。

1. `asset/unit/cd/config.php` をコピーする
2. `asset/config/cd.php` を作成する
3. 許可したい branch 名を allowed `branch` list に追加する

これにより、current の CD rule set の下でその branch を push できるようになります。

push 先が GitHub ではない場合、この GitHub 向け branch 制限は適用されません。

その意図は、GitHub ではない remote はプライベートな repository 領域だと判断しているためです。

この前提では、うっかり push しても GitHub に公開してしまう場合より問題が小さいとみなしています。

push 先が GitHub であり、上記の許可条件に当てはまらない場合は、push はブロックされます。

この GitHub 向け branch 制限は、commit message prefix ルールとも、CI チェックとも別の制御です。

その意味では、ONEPIECE Framework は GitHub に対して部分的にベンダーロックインしているとも言えます。より強い公開制御が GitHub push を中心に設計されているためです。

### branch 名で push をブロックする目的

この branch 名による push 制限の目的は、意図しないコードの push を防ぐことです。

特に、次のようなコードが GitHub に push されることを防ぐ意図があります。

- 開発途中のコード
- 研究中のコード
- 共有履歴に載せる準備ができていない branch

これにより、次のような問題を減らすことを目的としています。

- 履歴の汚染
- 不要なコンフリクト
- 未完成な作業内容の意図しない共有

また、例えば次のような開発途中のコミットメッセージ:

- `WIP: ALL`

を、そのまま公開 repository に push してしまうことを防ぐ意図もあります。

## `hook-the-hooks.sh` の意図

ONEPIECE Framework は `core.hooksPath` によって framework 側の hook を強制します。

そのままだと、ユーザー定義の hook を実行しにくくなります。

その問題を解決するために `hook-the-hooks.sh` があります。

このスクリプトは、framework 側の hook から次の hook も実行できるようにします。

- `.git/hooks/` に置かれたユーザー定義の local hook
- global `core.hooksPath` に置かれたユーザー定義の global hook

つまり、ONEPIECE Framework の hook を強制しつつ、ユーザー定義の hook も併用できるようにするのが目的です。

## その他に見つかったルール

### private local file の ignore ルール

repository の `.gitignore` には、次の広い pattern が含まれています。

- `.*`
- `_*`

歴史的には、この ignore 挙動の方が先にありました。

`_` で始まる config override 規約は、それより後から追加されたものです。

これは、ONEPIECE Framework の運用テクニックの一つでもあります。

意図は、`.` で始まるファイルと `_` で始まるファイルを、通常は local 専用ファイルとして扱い、通常の `git add .` の流れに乗らないようにすることです。

これにより、local 専用ファイルが次へうっかり公開されることを減らせます。

- production deployment の流れ
- 共有 repository
- 公開 GitHub repository

実務上の運用ルールとしては、repository に保存したくない file や directory がある場合、`_` で始まる名前にしておくのがよい基本方針です。

これにより、local 環境に閉じ込めておきたかったものを、うっかり commit し、push し、全世界に公開してしまう悲劇を減らせます。

代表例としては、次のような local override file があります。

- `_admin.php`
- `_database.php`
- `_php.php`

つまり、これらの config override file は、もともと存在していた underscore-ignore の挙動に乗る形で追加された後発機能として理解するべきです。

ただし、`git add -f` を使えば Git に追加できるため、これは絶対的な保護ではなく、うっかりミスを減らすための guardrail です。

### hook bypass についての注意

hook によってルール違反として操作がブロックされても、Git 自体には一部の操作で bypass 手段があります。

例えば次です。

- `git commit -n`
- `git commit --no-verify`
- `git push --no-verify`

これらの option を使うと、通常の verification hook を実行せずに処理を進めることができます。

実務上は、例えば次のように理解できます。

- `git commit -n` または `git commit --no-verify` は `pre-commit` を bypass できる
- `git push --no-verify` は `pre-push` を bypass できる

ONEPIECE Framework の運用文脈では、これは通常運用ではなく、例外的または緊急時の escape hatch として扱うべきです。

この注意を書いておく目的は、実務上の認識合わせです。

- hook は強い guardrail である
- ただし絶対的な sandbox ではない
- 操作者が意図的に bypass することはできる

### deny word の仕組み

rebase と push の prefix チェックには、deny word の仕組みもあります。

現在の config では deny は空ですが、仕組み自体は存在します。

### pre-commit の制限

`pre-commit` hook では、staged な `php`, `phtml`, `html` の変更を検査します。

追加行に次のようなパターンが含まれていると commit は拒否されます。

- `var_dump`
- `print_r`
- `exit;`
- `$_GET`
- `$_POST`
- `$_REQUEST`
- `$_COOKIE`
- `$_SESSION`
- `$_SERVER`

[DOC-GAP] pre-commit non-source-document diff-context mismatch.

現在の user-side `pre-commit` 実装は、上記の intended rule より広く動作する場合があります。

観測された齟齬:

- source file だけでなく、documentation などの non-source file にも check が適用される場合がある
- 変更で実際に追加された行だけでなく、変更前後の diff context line まで検査対象になる場合がある
- そのため、編集箇所の近くに既存の forbidden pattern があるだけで commit が拒否されることがある

なぜ重要か:

- documentation-only work が source-code guardrail によって block される
- 既存の近接 text によって、無関係な documentation edit が失敗する
- documented rule と actual hook behavior の差が、agent と human の reasoning を難しくする

将来方針:

- hook の対象を intended target file type に限定する。もし広い対象が望ましい policy なら、そのことを明示的に document 化する
- forbidden-pattern check は added line だけに限定する
- 後から検索できるように、`pre-commit`, `non-source document`, `documentation hook`, `diff context`, `added lines only` を検索語として残す

## まとめ

現在の実装では、次のルールが制御されています。

- rebase 時に commit message prefix を検査する
- push 時に commit message prefix を検査する
- push 時は、remote が `local` でない限り CI 通過を要求する
- GitHub への push は CD 側の branch ルールでも制限する
- framework hook を強制しつつ、`hook-the-hooks.sh` によりユーザー定義 hook も実行できる
- `.` で始まるファイルと `_` で始まるファイルは、local override 運用の一部として既定で ignore される

なお、口頭の説明と実装が少し違う点は次です。

- Year prefix は「今年以下を自動許可」しているわけではない
- 実際には config に明示列挙された Year prefix と、現在の branch 名 prefix が許可される
