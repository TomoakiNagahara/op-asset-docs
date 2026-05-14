# OP-CORE Overview

Source folder: `asset/core/`

OP-CORE is the framework core used by this Skeleton, but it is not specific to this Skeleton.

The official OP Skeleton is one application that consumes OP-CORE. Third-party applications may also use OP-CORE directly or through their own skeletons.

Because OP-CORE is shared framework core, avoid describing its behavior as if it only serves this repository. Core behavior, contracts, tests, and documentation should be written so they remain valid for official OP applications and external applications.

## Folder Model

`asset/core/` is split into submodule folders. Each folder groups one kind of core responsibility:

- `ci/`: CI-facing entry points for core behavior.
- `class/`: named runtime core classes.
- `docs/`: package-owned documentation for op-core itself.
- `function/`: standalone global helper functions.
- `include/`: startup and request include files.
- `interface/`: framework contracts for units, modules, services, and integrations.
- `testcase/`: runnable or inspectable core test cases.
- `trait/`: shared implementation mixed into core classes.
- `tutorial/`: runnable or renderable examples for learning core behavior.

## Documentation Model

Keep overview-level context in this file.

Keep folder-specific definitions in each folder document, such as `class.md`, `function.md`, or `interface.md`.

When documentation depends on one separated class, trait, function, include file, interface, testcase, or tutorial page, create a document named for that file instead of merging those details into an overview or shared folder document.

This keeps OP's file-level commit model clear: a feature file, its matching test or CI file, and its same-named documentation can move together while reducing rebase and cherry-pick conflicts.
