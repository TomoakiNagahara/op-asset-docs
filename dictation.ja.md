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
