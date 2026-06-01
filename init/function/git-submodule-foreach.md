# `GitSubmoduleForeach()` As-Is

## Overview

`asset/init/function/GitSubmoduleForeach.php` recursively handles Git-managed submodules from a given Git root.

It saves the current directory, prepares submodules for the current repository, sets the framework hook path, reads active submodule config, and then visits each configured submodule path.

## Per-Submodule Work

For each active submodule, it:

- changes into the submodule path
- adds optional local or SSH remotes through `GitSubmoduleRepository()`
- checks out the target branch through `GitCheckoutTargetBranch()`
- sets `core.hooksPath` for that submodule
- recurses when the submodule itself has `.gitmodules`

After traversal, it restores the original directory.

## Related Documents

- `git-submodule-github.md`
- `git-submodule-repository.md`
- `git-submodule-config.md`
- `git-checkout-target-branch.md`
