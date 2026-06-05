# Dictation

## 口頭指示の記録

 * ONEPIECE Framework は、日本語話者だけでなく世界中の開発者に使って貰いたいという concept を持つ。
 * user が口頭または chat で説明した指示は、該当する責任範囲の dictation files に残す。
 * 日本語話者の口述は `dictation.ja.md` に残す。
 * 他の言語の話者の口述は `dictation.<language>.md` に残す。
 * それらの agent-facing summary、cross-language note、作業記録、備忘録は `dictation.md` に残す。
 * `dictation.md` は必読ではない。過去の口述 context が current task に必要な場合、または user が dictation の保存や確認を明示した場合だけ参照する。
 * framework 全体、AI agent の作業ルール、documentation 作成ルール、CORE や skeleton 全体に関わる指示は、framework 側の `asset/docs/` 配下の dictation files に残す。
 * 特定の UNIT に閉じた指示は、該当 UNIT 側の `asset/unit/<unit>/docs/` 配下の dictation files に残す。
 * 特定の MODULE に閉じた指示は、該当 MODULE 側の `asset/module/<module>/docs/` 配下の dictation files に残す。
 * どの `dictation.md` に残すべきかは、指示が最初に出た場所ではなく、責任範囲で判断する。
 * `AGENTS.md` は薄い導線に留め、詳細な判断基準は `asset/docs/documentation-authoring.md` に置く。

## 2026-05-29 初期化 document 導線の整理

 * 背景: agent が OP Framework の初期化処理について質問されたとき、必要な document を読み落としたり、逆に不要な document まで読んで context を増やしたりする問題があった。
 * 目的: 人間が agent に質問した場合に、agent が適切な document だけを読み、不要な document を読まずに済むようにする。把握すべき情報量を抑え、回答の精度を上げる。
 * README にある clone 後の最初の init から submodule clone までに関する質問では、`AGENTS.md` から `asset/docs/init/overview.md` などの関連 document へ辿れる導線を用意する。
 * ただし導線は広く読みすぎないようにする。`submodules.php` の質問では control が渡るため `update.php` document が必要になる場合があるが、`update.php` の質問では caller relationship が直接関係しない限り `submodules.php` document を読む必要はない。
 * `asset/init/submodules.php` は、main repository である OP Framework skeleton の初期化を行い、submodule initialization は `update.php` に引き継ぐ。
 * `asset/init/update.php` は、Git-managed / non-Git-managed を含む submodules を clone / update し、remote の追加、original remote の別名保持、local remote の作成、hooks の設定などを行う。
 * `asset/init/update.php` は、`asset/init/function/` directory の `Init.php` と `Update.php` を load し、`Init()` と `Update()` を行う。
 * `Init()` 対象の directory が既に存在する場合は、Init 処理を行わない。
 * `Update()` は、remote から fetch して rebase する。
 * `asset/init/submodules.php` の As-Is document では、その file の call order と branch point に集中する。呼び出し先 file / function については、その呼び出し先で何が行われるかだけを簡潔に書き、内部詳細は専用 document への導線を引いて参照させる。
 * `asset/init/update.php` についても同様に、file-level As-Is は call order と branch point に集中し、`Init()`、`Update()`、helper function の内部詳細はそれぞれの専用 document に分ける。
 * ONEPIECE Framework では、ほとんどの file が submodule/package repository として分離されている。submodule 化されていない main repository の file は、初期化処理に必要な `asset/init/` などが中心である。
 * skeleton root 側には現在 Git-managed submodules は無い。ただし end user が skeleton 側に独自の Git-managed submodule を連結する可能性はある。
 * CORE、UNIT、MODULE、LAYOUT、bootstrap、template は submodule package である。`asset/core/` は意図的に Git-managed nested submodules を持つため、agent が skeleton-owned file と誤解しないようにする。

## 2026-06-05 documentation file name の category-first rule

 * user 指示: 「ファイル名は、ci-logout-form-clear.md にして下さい。理由は、ciのトラブルなので、ciが先に来るべきです。そうしておくと、ファイルソートでci関連のトラブルだけがまとまり、人間にとって探しやすいからです。これは、ドキュメントを作成する上でのルールにして欲しいです。ルールは、CIだからという狭いルールではなく、人間がファイルソートした時に、あるカテゴリー順に探しやすくするために、ファイル名の付け方を工夫して欲しいというルールです。」

## コーディングルール

 * method の閉じかっこには、常に method name を comment として追加する。例: `} // Bar`
 * method name の大文字小文字は、実際の method name に合わせる。
 * 理由は、Git の diff があまり賢くなく、修正と関係のない隣の method の閉じかっこを diff に含めることがあるため。
 * 不要な diff は、rebase や merge 時の conflict の大きな原因になるため、closing comment を method boundary marker として使う。
 * local static variable に singleton instance を保持する場合は、`return $variable ??= new Class();` のように return expression へまとめず、明示的な `if(!$variable)` block で初期化してから variable を return する。
 * 理由は、Eclipse で variable が未使用であるという警告が出ることを避けるため。
 * agent の documentation lookup は task の大きさに比例させる。小さな修正では近傍 owner docs を先に読む。新規 UNIT / MODULE 作成、大きな再構成、CI layout、namespace placement、memory/loading design の場合は、`asset/docs/op/unit-module-authoring.md`、`asset/docs/op/coding-rules.md`、`asset/docs/cicd/ci-file-layout.md`、`asset/docs/op/common-recipes.md`、`asset/docs/op/design-philosophy.md` を読む。
 * UNIT / MODULE code では、無駄な class / method を増やさない。一箇所でしか呼ばれない method は、実際の benefit がない限り分けない。normal request memory を小さく保ち、entry file は薄くする。helper class は package subnamespace に隔離する。visible な `*.class.php` は CI target として扱う。CI file layout は class ごと、method ごとに分ける。戻り値は caller が必要とする最小限にする。package-scoped session state には `OP_SESSION` と `self::Session()` を使う。shared な `\OP\Session` facade wrapper が意図した storage scope の場合だけ `OP()->Session()` を使う。raw `$_SESSION` は避ける。docs は `AGENTS.md` に詰め込まず、責任範囲ごとの document に分ける。
 * `OP_SESSION` は `asset/core/trait/docs/op-session.md` と `asset/core/trait/docs/op-session.ja.md` に記録する。
