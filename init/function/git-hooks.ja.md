# `GitHooks()` As-Is

## Overview

`asset/init/function/GitHooks.php` は framework Git hook path を設定します。

current repository の `core.hooksPath` を次に設定します。

```text
<skeleton-root>/asset/init/hooks/
```

その後、`git submodule foreach` で到達できる Git submodules にも同じ設定を行います。

## Scope

`GitHooks()` は `_ROOT_GIT_` が define 済みであることに依存します。

current Git working directory から到達できる repositories を設定します。その repository の `.gitmodules` から到達できない repositories は、その call では設定されません。
