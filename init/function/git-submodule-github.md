# `GitSubmoduleGithub()` As-Is

## Overview

`asset/init/function/GitSubmoduleGithub.php` prepares Git-managed submodules for the current repository.

If `.gitmodules` does not exist, it returns without doing work.

If `Request('github')` is set, it copies `.gitmodules` to `.gitmodules_origin` and replaces `onepiece-framework` in `.gitmodules` with the requested owner name.

After that, it runs:

```text
git submodule sync
git submodule init
git submodule update
```

## Scope

This function operates on the current working directory.

Request value loading is handled by `Request()`. See `request.md`.
