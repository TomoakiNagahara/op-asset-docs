# class

Source folder: `asset/core/class/`

For overall OP-CORE context, see `overview.md`.

`class` defines the named core classes used by OP at runtime.

This folder owns class files such as `OP.class.php`, `Config.class.php`, `Env.class.php`, `Error.class.php`, `Session.class.php`, and `Unit.class.php`.

Use this folder for object-level core behavior and runtime state management. Do not place global helper functions, bootstrap include files, interface contracts, or trait-only shared implementation here.

When documenting behavior that depends on one class file, create a class-file-specific document instead of merging unrelated class details into one shared document.
