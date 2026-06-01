# `GitHooks()` As-Is

## Overview

`asset/init/function/GitHooks.php` sets the framework Git hook path.

It sets `core.hooksPath` for the current repository to:

```text
<skeleton-root>/asset/init/hooks/
```

It then runs the same configuration for Git submodules reachable through `git submodule foreach`.

## Scope

`GitHooks()` depends on `_ROOT_GIT_` being defined.

It configures repositories reachable from the current Git working directory. Repositories that are not reachable through that repository's `.gitmodules` are not configured by that particular call.
