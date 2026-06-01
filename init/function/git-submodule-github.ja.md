# `GitSubmoduleGithub()` As-Is

## Overview

`asset/init/function/GitSubmoduleGithub.php` は current repository の Git-managed submodules を準備します。

`.gitmodules` が存在しない場合は、何もせず return します。

`Request('github')` が設定されている場合、`.gitmodules` を `.gitmodules_origin` に copy し、`.gitmodules` 内の `onepiece-framework` を requested owner name に置換します。

その後、次を実行します。

```text
git submodule sync
git submodule init
git submodule update
```

## Scope

この function は current working directory に対して動作します。

request value loading は `Request()` が担当します。`request.ja.md` を参照してください。
