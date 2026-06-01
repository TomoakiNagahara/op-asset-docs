# `GitCheckoutTargetBranch()` As-Is

## Overview

`asset/init/function/GitCheckoutTargetBranch.php` switches the current repository to the requested branch when possible.

If the current branch already matches the requested branch, it returns without doing work.

If the requested remote branch does not exist, it prints a notice and returns.

Otherwise, it tries `git switch <branch>`. If that fails, it creates the local branch from `<remote>/<branch>` with `git checkout -b`.
