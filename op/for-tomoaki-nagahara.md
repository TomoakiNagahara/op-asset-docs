# For TomoakiNagahara

## Why These Three Documents Are Necessary

The following three documents are especially important for the long-term AI usability of the ONEPIECE Framework:

- `Invariants`
- `Responsibility Boundaries`
- `Common Recipes`

They are needed because the existing documentation already explains many individual specifications, but AI also needs a higher-order decision structure.

## Related Documents

- `invariants.md`
- `responsibility-boundaries.md`
- `common-recipes.md`

## 1. Why `Invariants` Is Necessary

AI does not only need to know how the framework currently works.

It also needs to know what must not be changed casually.

Without explicit invariants, AI may correctly understand local behavior while still proposing a change that breaks a fundamental framework contract.

`Invariants` gives AI a stable line between:

- safe change
- risky change
- framework-level contract breakage

## 2. Why `Responsibility Boundaries` Is Necessary

Many framework mistakes are not caused by misunderstanding a single function.

They are caused by editing the wrong layer.

For example:

- changing Router behavior when the issue belongs to Layout
- changing Core behavior when the issue belongs to a Unit
- mixing output concerns with configuration concerns

`Responsibility Boundaries` helps AI decide:

- where a change belongs
- which subsystem owns the behavior
- which layer should remain untouched

## 3. Why `Common Recipes` Is Necessary

AI often receives task-oriented requests rather than architecture questions.

Examples:

- add a page
- add local-only config
- make CI recognize a repository
- replace a unit
- debug a push failure

For these cases, AI needs procedural patterns.

`Common Recipes` gives AI practical response paths that connect framework knowledge to implementation work.

## Why These Three Work Better Together

The three documents are strongest when used together:

- `Invariants` tells AI what must remain stable
- `Responsibility Boundaries` tells AI where the change belongs
- `Common Recipes` tells AI how to carry the change out

Together, they turn documentation from passive knowledge storage into an active decision framework for AI.

## Meaning for the Long-Term Goal

If the long-term goal is that humans no longer need to learn the full framework in detail before using it effectively, these three documents are foundational.

They help AI answer not only:

- what the framework does

but also:

- what should be changed
- where it should be changed
- what must remain intact
- how to execute the change safely

That is why these three documents are not optional supplements.

They are structural documents for AI-guided framework use.
