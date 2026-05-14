# include

Source folder: `asset/core/include/`

For overall OP-CORE context, see `overview.md`.

`include` defines core include files that are loaded directly during startup or request setup.

This folder owns low-level include targets such as `Autoloader.php`, `Define.php`, `Error.php`, `Request.php`, `RequestWeb.php`, `RequestShell.php`, `isAdmin.php`, and `isLocalhost.php`.

Use this folder for bootstrapping support, environment/request setup, and procedural include files that must be available before or around normal class usage. Do not place normal class definitions, reusable traits, or general helper-function libraries here.

Changes here can affect startup and request behavior, so verify the bootstrap and request flow when editing these files.
