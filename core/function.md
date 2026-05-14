# function

Source folder: `asset/core/function/`

For overall OP-CORE context, see `overview.md`.

`function` defines standalone global helper functions for OP.

This folder owns function files such as `OP.php`, `D.php`, `RootPath.php`, `Layout.php`, `Mail.php`, `Time.php`, `Timestamp.php`, path conversion helpers, encoding helpers, and MIME helpers.

Use this folder for small function-level behavior that is intentionally callable without a class instance. Do not place class state, trait composition, request include files, or interface contracts here.

When documenting one helper function, keep the document aligned to that function file name so the function, test, and documentation can move as one commit unit.
