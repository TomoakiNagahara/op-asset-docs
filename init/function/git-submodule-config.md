# `GitSubmoduleConfig()` As-Is

## Overview

`asset/init/function/GitSubmoduleConfig.php` reads active submodule entries for a Git root.

It changes into the given Git root, reads submodule names whose local config has `active = true`, then reads each entry's `url`, `path`, `branch`, and `follow` values from the given `.gitmodules` file.

It also marks whether that submodule path contains another `.gitmodules` file.

After reading, it restores the original directory and returns the collected config array.
