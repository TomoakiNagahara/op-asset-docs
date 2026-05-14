# testcase

Source folder: `asset/core/testcase/`

For overall OP-CORE context, see `overview.md`.

`testcase` defines runnable core test cases.

This folder owns test files for core behavior such as `AppID.php`, `Cookie.php`, `EMail.php`, `Encrypt.php`, `Error.php`, `Request.php`, `RootPath.php`, `Session.php`, `Time.php`, and `Timestamp.php`.

Use this folder for executable or inspectable test coverage of core files. Do not place runtime implementation, tutorial pages, or package documentation here.

When a core class, function, include, or trait changes, the matching testcase is an expected companion file for the same file-level commit unit.
