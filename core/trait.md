# trait

Source folder: `asset/core/trait/`

For overall OP-CORE context, see `overview.md`.

`trait` defines shared implementation mixed into core classes.

This folder owns trait files such as `OP_CORE.php`, `OP_ENV.php`, `OP_ERROR.php`, `OP_FUNCTION.php`, `OP_SESSION.php`, `OP_TEMPLATE.php`, `OP_UNIT.php`, and `OP_UNIT_MAPPER.php`.

Use this folder for reusable method groups that compose core behavior across classes. Do not place standalone global functions, complete class definitions, interface contracts, or test cases here.

Trait changes can affect several classes at once; document trait-specific behavior by trait file name and verify the classes that consume the trait.
