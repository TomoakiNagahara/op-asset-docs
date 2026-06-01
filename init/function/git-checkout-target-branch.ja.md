# `GitCheckoutTargetBranch()` As-Is

## Overview

`asset/init/function/GitCheckoutTargetBranch.php` は、可能な場合に current repository を requested branch へ切り替えます。

current branch がすでに requested branch と一致している場合は、何もせず return します。

requested remote branch が存在しない場合は、notice を表示して return します。

それ以外の場合は `git switch <branch>` を試します。失敗した場合は、`git checkout -b` で `<remote>/<branch>` から local branch を作成します。
