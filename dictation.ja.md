# Dictation

## 口頭指示の記録

 * ONEPIECE Framework は、日本語話者だけでなく世界中の開発者に使って貰いたいという concept を持つ。
 * user が口頭または chat で説明した指示は、該当する責任範囲の dictation files に残す。
 * 日本語話者の口述は `dictation.ja.md` に残す。
 * 他の言語の話者の口述は `dictation.<language>.md` に残す。
 * それらの agent-facing summary や cross-language guidance は `dictation.md` に残す。
 * framework 全体、AI agent の作業ルール、documentation 作成ルール、CORE や skeleton 全体に関わる指示は、framework 側の `asset/docs/` 配下の dictation files に残す。
 * 特定の UNIT に閉じた指示は、該当 UNIT 側の `asset/unit/<unit>/docs/` 配下の dictation files に残す。
 * 特定の MODULE に閉じた指示は、該当 MODULE 側の `asset/module/<module>/docs/` 配下の dictation files に残す。
 * どの `dictation.md` に残すべきかは、指示が最初に出た場所ではなく、責任範囲で判断する。
 * `AGENTS.md` は薄い導線に留め、詳細な判断基準は `asset/docs/documentation-authoring.md` に置く。

## コーディングルール

 * method の閉じかっこには、常に method name を comment として追加する。例: `} // Bar`
 * method name の大文字小文字は、実際の method name に合わせる。
 * 理由は、Git の diff があまり賢くなく、修正と関係のない隣の method の閉じかっこを diff に含めることがあるため。
 * 不要な diff は、rebase や merge 時の conflict の大きな原因になるため、closing comment を method boundary marker として使う。
 * local static variable に singleton instance を保持する場合は、`return $variable ??= new Class();` のように return expression へまとめず、明示的な `if(!$variable)` block で初期化してから variable を return する。
 * 理由は、Eclipse で variable が未使用であるという警告が出ることを避けるため。
 * UNIT / MODULE を coding する前に、`asset/docs/op/unit-module-authoring.md`、`asset/docs/op/coding-rules.md`、`asset/docs/cicd/ci-file-layout.md`、`asset/docs/op/common-recipes.md`、`asset/docs/op/design-philosophy.md` を読む。
 * UNIT / MODULE code では、無駄な class / method を増やさない。一箇所でしか呼ばれない method は、実際の benefit がない限り分けない。normal request memory を小さく保ち、entry file は薄くする。helper class は package subnamespace に隔離する。visible な `*.class.php` は CI target として扱う。CI file layout は class ごと、method ごとに分ける。戻り値は caller が必要とする最小限にする。session は raw `$_SESSION` ではなく `OP()->Session()` を使う。docs は `AGENTS.md` に詰め込まず、責任範囲ごとの document に分ける。
