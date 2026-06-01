# `GitSubmoduleConfig()` As-Is

## Overview

`asset/init/function/GitSubmoduleConfig.php` は、指定された Git root の active submodule entries を読み取ります。

指定された Git root に移動し、local config で `active = true` になっている submodule names を読み、その後、指定された `.gitmodules` file から各 entry の `url`、`path`、`branch`、`follow` values を読み取ります。

また、その submodule path がさらに `.gitmodules` file を持つかどうかも mark します。

読み取り後、元の directory に戻り、収集した config array を返します。
