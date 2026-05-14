# docs

Source folder: `asset/core/docs/`

OP-CORE 全体の前提は `overview.ja.md` を参照する。

`docs` は op-core 自体の package-owned documentation を含む。

この folder は `asset/core/` と一緒に移動する documentation submodule である。OP overview、request handling、URL handling、MetaPath、Unit system、`D()` function など、core package 側の concept と behavior を説明する。

op-core package に属する documentation を変更する場合はこの folder を使う。core に関する agent-facing または skeleton-facing reference は skeleton 側の `asset/docs/core/` を使う。

submodule update と skeleton documentation commit を明確に保つため、package-owned core docs と skeleton docs は分けて管理する。
