# Documentation Authoring

## 目的

この document は、AI coding agent が ONEPIECE Framework repository で documentation を作成、移動、再構成する時の指針です。

agent は documentation を作成する前に、この document を参照してください。

## AGENTS.md を薄く保つ

`AGENTS.md` は AI agent の main entry point ですが、詳細な rule を集める場所ではありません。

`AGENTS.md` には、次だけを置きます。

- repository の入口として必要な最小限の working guide
- どの document を読むべきかの導線
- agent が最初に迷いやすい boundary への参照

詳細な rule は、それぞれ責任を持つ document へ分離します。

- framework 全体に関する内容: `asset/docs/op/`
- CORE に関する内容: `asset/docs/core/` または owner package の docs
- UNIT に関する内容: `asset/docs/unit/`、`asset/unit/<unit>/docs/`
- MODULE に関する内容: `asset/docs/module/`、`asset/module/<module>/docs/`
- CI/CD に関する内容: `asset/docs/cicd/`
- skeleton に関する内容: `asset/docs/skeleton/`
- web server に関する内容: `asset/docs/httpd/`

`AGENTS.md` に詳細 rule を追加したくなった場合は、まずその rule の責任範囲を特定し、責任 document に追加してください。
`AGENTS.md` には必要な参照だけを追加します。

## document role

- `README.md` は human 向け。
- `AGENTS.md` は AI coding agent 向けで、repository root では `asset/docs/AGENTS.md` への symlink として公開する。
- `CUSTOMIZATION_MAP.md` は safe customization point と framework ownership boundary を説明する。実体は `asset/docs/CUSTOMIZATION_MAP.md` として管理する。
- `CODEX.md` は Codex CLI-specific workflow note だけを含む。実体は `asset/docs/CODEX.md` として管理する。

## 言語と翻訳

English document は AI consumption の canonical working document とします。

user が日本語で document correctness を確認し、translation を使って English document が正確か検証するため、日本語訳も必要です。

日本語訳を追加する場合は、English file の隣に置き、`.ja.md` suffix を使います。

新しい translation の default location として、次は使いません。

- `asset/docs/ja/`
- `asset/core` 配下の `docs/ja/`
- `asset/unit` 配下の `docs/ja/`
- `asset/module` 配下の `docs/ja/`
- `asset/docs/spec/`

日本語 file name は使いません。
repository documentation の file name は、すべて ASCII English にします。
日本語の内容は file name ではなく、`.ja.md` translation file に置きます。

## file-by-file documentation

OP では class、trait、function などの feature が個別 file に分離されており、Git commit も通常は multi-file feature bundle 単位ではなく file 単位で行います。

この file-by-file commit style は、OP が非常に loosely coupled であるため成立しています。
documentation もこの model に合わせます。

分離された 1 file に依存する documentation は、その file name の document を作成し、詳細を shared document にまとめません。

file-level commit は rebase や cherry-pick の conflict を大きく減らすため、分離 file の documentation は file name ごとに分けて管理します。

feature file を追加、変更、削除する場合、その feature file と同時に commit されやすい別 file は related CI test と same-named documentation です。
documentation file name を class file や function file と対応させることで、commit unit を明確に保ちます。

## As-Is / To-Be / gap

current As-Is behavior を document 化する場合、authoritative detail は、その behavior を決めている code owner の近くに置きます。

例:

- responsible class
- trait
- function
- unit
- module

同じ As-Is implementation detail を、責務の異なる複数 document に重複して書いてはいけません。
owner 以外の document は、詳細をコピーせず owner document に link または参照します。

documentation path は、最初にその話題が見つかった page ではなく、code owner と responsibility boundary から選びます。

intended specification、current As-Is behavior、curated gap index は分離して保ちます。
gap index は詳細な owner にならず、As-Is owner を指します。

## placement

document の置き場所は責任範囲で決めます。

- web-server-related document: `asset/docs/httpd/`
- framework-level CI/CD philosophy、history、background、operating-model document: `asset/docs/cicd/`
- Unit system 自体の philosophy、history、background: `asset/docs/unit/`
- Module system 自体の philosophy、history、background: `asset/docs/module/`
- NEW WORLD philosophy、background、historical document: `asset/docs/new-world/`
- op-core philosophy、background、high-level core feature document: `asset/docs/core/`
- framework-wide philosophy、design intent、ONEPIECE Framework の background: `asset/docs/op/`
- skeleton-specific framework document: `asset/docs/skeleton/`

framework-level document が上記のどれにも当てはまらない場合は、`asset/docs/` 直下に置きます。

特定 unit または module の implementation detail は、その package 自身の `docs/` directory に置きます。

## dictation note

user が口頭または chat で説明した指示を保存するように求めた場合は、責任範囲に対応する dictation files に記録します。

置き場所は、その話題が最初に出た場所ではなく、責任範囲で決めます。

- framework-wide な指示、AI-agent working rule、documentation-authoring rule、CORE-wide rule、skeleton-wide rule: `asset/docs/`
- UNIT-specific な指示: `asset/unit/<unit>/docs/`
- MODULE-specific な指示: `asset/module/<module>/docs/`

責任範囲に対応する directory の中で、dictation は言語と audience に分けます。
ONEPIECE Framework は世界中の開発者に使われることを目指しているため、日本語の dictation だけを唯一の source language として扱ってはいけません。

- `dictation.ja.md`: Japanese-speaking user の original dictation
- `dictation.<language>.md`: 他の言語の話者の original dictation
- `dictation.md`: language-specific dictation files から導いた agent-facing summary、cross-language note、作業記録、備忘録

file name は ASCII English にします。
language suffix も `dictation.en.md`、`dictation.fr.md`、`dictation.zh.md` のように ASCII にします。

`dictation.*.md` は、user の original instruction context を保存する file であり、その話者の言語で書いて構いません。
`dictation.md` は AI agent が理解できる内容にし、language-specific record を置き換えず、有用な operational note を要約します。
ordinary task で必ず読む file ではありません。過去の口述 context が必要な場合、または user が dictation の保存や確認を明示した場合だけ参照します。

その指示が stable rule になった場合は、責任を持つ canonical document に durable rule として移動または要約し、`AGENTS.md` は薄い導線だけに保ちます。

## public repository safety

public repository document では、実際の site name、hostname、subdomain、layout name、user name、local project name など deployment-specific な固有名詞を抽象化します。

その固有名詞自体が document の主題でない限り、次のような placeholder を使います。

- `<site-name>`
- `<subdomain>`
- `<layout-name>`
- `<project-name>`

repository document に `/System/Volumes/...` のような local absolute file link を入れません。

repository document では、clickable local-environment file link ではなく、plain repository-relative path を優先します。

## searchable tags

document が current problem、mismatch、risk、limitation、future fix direction を説明する場合、searchable tag を付けます。

例:

- `[DOC-ISSUE]`
- `[DOC-RISK]`
- `[DOC-GAP]`
- `[DOC-FUTURE]`
- `[DOC-PRIORITY1]`

specification がすでに明確だが current implementation が明らかに異なる場合は `[DOC-PRIORITY1]` を使います。
