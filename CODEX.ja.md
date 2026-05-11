# CODEX.md

このファイルは、この repository で作業する Codex CLI 固有の補足です。

文書の役割: 境界判断の詳細は `CUSTOMIZATION_MAP.md` に置きます。`AGENTS.md` は AI agent のメイン導線と一般作業ルールです。`CODEX.md` は Codex CLI 固有の説明だけに限定します。

repository architecture、安全な customization boundary、framework rule、documentation rule、verification expectation については、まず `AGENTS.md` を読んでください。これらの rule はここに重複して書きません。

## Scope

- `AGENTS.md` はすべての AI coding agent 向けのメインガイドです。
- `CUSTOMIZATION_MAP.md` は、安全な customization point と framework-managed core zone の source of truth です。
- `CODEX.md` は、一般的な AI-agent policy ではない Codex CLI workflow detail だけを記録します。

## Codex CLI Workflow

- まず `AGENTS.md` を読み、変更が framework-managed package に触れる可能性がある場合は `CUSTOMIZATION_MAP.md` を読む。
- repository を調査するときは、`rg` や `rg --files` などの速い local search tool を使う。
- 編集前に、target layer を推測せず、関連 file と近くの context を確認する。
- manual edit には `apply_patch` を使う。
- dirty worktree にある user change を、user が明示的に依頼していない限り overwrite または revert しない。
- Codex の変更は narrow で review しやすい範囲に保つ。
- committed documentation では repository-relative path を優先する。
- docs-only change の後は、通常 diff review で十分です。runtime check は behavior が変わった場合だけ必要です。

## Codex CLI Reporting

user に作業結果を報告するとき:

- 変更した file を伝える。
- 実行した check を伝える。docs-only change で runtime check が不要だった場合は、その旨を明確に言う。
- intentionally left untouched にした pre-existing dirty file があれば伝える。

## Codex CLI Safety Notes

- task が明示的に framework development を対象にしていない限り、CORE と既存 framework package は read-mostly として扱う。
- application-specific request を CORE や既存 framework UNIT package の編集で解決しない。
- framework bug を見つけた場合、application customization に隠すのではなく、package-level fix または pull request として準備する。
