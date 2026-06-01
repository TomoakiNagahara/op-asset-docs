# `submodules.php` As-Is

## Overview

`asset/init/submodules.php` は、OP Framework skeleton を clone した後に通常最初に実行する initializer です。

main skeleton repository を準備し、current repository から到達できる Git-managed submodules を初期化し、configured repository setup を `asset/init/update.php` に引き継ぎ、最後に framework Git hook path を適用します。

より広い initialization model は `overview.ja.md` を参照してください。

## Execution Flow

1. helper functions を load する。
2. `git rev-parse --show-toplevel` から `_ROOT_GIT_` を define する。
3. current directory を `_ROOT_GIT_` に移動する。
4. branch や application constants を利用できるように `asset/config/op.php` を include する。
5. Git-managed submodule preparation helpers を実行する。
6. `asset/init/update.php` を include する。
7. `update.php` から戻った後で `GitHooks()` を実行する。

submodule preparation 前の `GitHooks()` call は、現在 comment out されています。current As-Is では、hook configuration は `update.php` から戻った後に適用され、recursive submodule handling の途中でも helper path が hooks を設定する場合があります。

## Hook Ordering Reason

hook package 自体は `asset/init/hooks/` の submodule として設定されています。

その hooks submodule が clone される前に `GitHooks()` が実行されると、`core.hooksPath` はまだ存在しない directory を指します。そのため、hooks directory が存在しないという warning が発生していました。

このため、top-level の `GitHooks()` call は `asset/init/update.php` の後へ移動され、hooks package を clone してから main hook path を適用する順序になっています。

## Remaining Hook Ordering Gap

[DOC-GAP] `GitSubmoduleForeach()` は、recursive Git submodule handling の途中でまだ `core.hooksPath` を設定します。

`asset/init/hooks/` が存在する前に `GitSubmoduleForeach()` が実行される場合、repositories に missing hooks directory を指す設定を入れる可能性がまだ残ります。current top-level order は元の warning path を減らしますが、hook setup は hooks package の存在が保証された後に完全には集約されていません。

OP-managed `Init()` path では、nested Git submodules の初期化後に、それらの submodule に hooks を設定するようになっています。これにより、`asset/core/class` や `asset/core/interface` のような initial clone case は扱われます。この remaining gap は、別経路である `GitSubmoduleForeach()` helper path に関するものです。

future cleanup では、次のどちらかの明確な rule に寄せるべきです。

- `asset/init/hooks/` が存在する場合だけ `core.hooksPath` を設定する
- recursive helper paths から hook setup を外し、`update.php` 後に集約する

## Helper Calls

`GitSubmoduleGithub()` は、request に応じて `.gitmodules` の owner replacement を行い、basic Git submodule sync/init/update commands を実行します。

詳細は `function/git-submodule-github.ja.md` を参照してください。

`GitSubmoduleRepository()` は、対応する request values が有効な場合に optional local / SSH remotes を追加します。

詳細は `function/git-submodule-repository.ja.md` を参照してください。

`GitSubmoduleForeach(_ROOT_GIT_)` は active Git submodules を走査し、target branch checkout、hooks 適用、nested Git submodules への recursion を行います。

詳細は `function/git-submodule-foreach.ja.md` を参照してください。

`asset/init/update.php` は、`Init()` と `Update()` の function flow を含む configured submodule repository initialization / update を扱います。

詳細は `overview.ja.md`、`function/init.ja.md`、`function/update.ja.md` を参照してください。

`GitHooks()` は、current repository とその Git submodules に対して `core.hooksPath` を `asset/init/hooks/` に設定します。

詳細は `function/git-hooks.ja.md` を参照してください。
