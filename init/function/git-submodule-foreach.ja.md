# `GitSubmoduleForeach()` As-Is

## Overview

`asset/init/function/GitSubmoduleForeach.php` は、指定された Git root から Git-managed submodules を recursive に扱います。

current directory を保存し、current repository の submodules を準備し、framework hook path を設定し、active submodule config を読み、その後 configured submodule path を順に訪問します。

## Per-Submodule Work

active submodule ごとに次を行います。

- submodule path に移動する
- `GitSubmoduleRepository()` により optional local / SSH remotes を追加する
- `GitCheckoutTargetBranch()` により target branch を checkout する
- その submodule に `core.hooksPath` を設定する
- submodule 自身が `.gitmodules` を持つ場合は recursion する

traversal 後、元の directory に戻ります。

## Related Documents

- `git-submodule-github.ja.md`
- `git-submodule-repository.ja.md`
- `git-submodule-config.ja.md`
- `git-checkout-target-branch.ja.md`
