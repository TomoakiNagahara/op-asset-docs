# asset/docs

この directory には ONEPIECE Framework skeleton の documentation set を置いています。

GitHub で `asset/docs/` を表示すると、この file が最初に表示されます。そのため、この README は human-facing な documentation index として使います。

AI agent は working entry point として `AGENTS.md` を使ってください。人間の読者は、この README を documentation area の地図として使えます。

## Primary Entry Points

- `AGENTS.md`
  AI coding agent の working guide と routing entry point。
- `CUSTOMIZATION_MAP.md`
  safe customization point と framework-managed boundary。
- `CODEX.md`
  Codex CLI-specific workflow notes。
- `documentation-authoring.md`
  documentation の作成、移動、再構成の rule。
- `important-gaps.md`
  既知の gap、risk、current problem index。
- `to-be.md`
  future direction と intended improvements。

framework-level document の Japanese translation は English file の隣に置き、suffix は次を使います。

- `.ja.md`

## Topic Areas

- `init/`
  initialization、submodule setup、update flow、related helper functions。
- `cicd/`
  CI/CD concepts、Git hooks、push checks、Git operation rules。
- `skeleton/`
  skeleton runtime lifecycle、entry point behavior、public roots、template placement。
- `op/`
  framework-wide design intent、coding rules、common recipes、responsibility boundaries。
- `core/`
  core package concepts と package-owned behavior notes。
- `unit/` and `module/`
  Unit / Module system documentation。
- `new-world/`
  HTML pass-through と NEW WORLD design background。
- `httpd/`
  web-server-related documentation。

## About As-Is and To-Be

As-Is と To-Be は不用意に同じ場所へ混ぜないでください。

実用上の guideline は次です。

- local technical documents は主に As-Is を説明する
- local documents でも必要な場合は短い `[DOC-FUTURE]` note を残してよい
- broader future direction は `to-be.md` にも集める

理由は、As-Is と To-Be が別の質問に答えるものだからです。

- As-Is: framework が現在何をするか
- To-Be: framework が将来どうあるべきか

To-Be document を分けておくことで、人間と AI の両方が次を区別しやすくなります。

- すでに true なこと
- planned にすぎないこと
- current bug または gap
- future design direction
