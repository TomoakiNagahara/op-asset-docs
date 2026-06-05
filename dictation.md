# Dictation For Agents

## Purpose

This file summarizes user dictation notes for AI agents.

The original dictation text is stored by language:

- Japanese dictation: `dictation.ja.md`
- Other languages: `dictation.<language>.md`

This file is a work note and memory aid for agent-facing summaries derived from those language-specific dictation files.
It is not required reading for ordinary tasks.

## Current Guidance

- ONEPIECE Framework is intended for developers around the world, not only Japanese speakers.
- Preserve oral or chat instructions in the responsible dictation files.
- Choose the responsible dictation location by ownership scope, not by where the instruction first appeared.
- Framework-wide instructions, AI-agent working rules, documentation-authoring rules, CORE-wide rules, and skeleton-wide rules belong under `asset/docs/`.
- UNIT-specific instructions belong under `asset/unit/<unit>/docs/`.
- MODULE-specific instructions belong under `asset/module/<module>/docs/`.
- Japanese-speaking users' original dictation belongs in `dictation.ja.md`.
- Original dictation from other language speakers belongs in `dictation.<language>.md`.
- Agent-facing summaries, cross-language notes, and work-memory notes belong in `dictation.md`.
- `dictation.md` is not required reading; consult it only when prior oral-instruction context matters or when the user asks to preserve or check dictation.
- Keep `AGENTS.md` thin; it should point agents to the dictation/documentation-authoring rules instead of duplicating them.
- Initialization documentation routes should minimize the amount of context an agent must read. Start from `asset/docs/init/overview.md`, then follow only the document for the file or function being discussed.
- For OP Framework initialization, `asset/init/submodules.php` initializes the main skeleton repository and hands package initialization to `asset/init/update.php`; `update.php` clones and updates configured package repositories and nested Git-managed submodules when present, handles remotes and hooks, loads `Init.php` and `Update.php`, skips `Init()` when the target directory already exists, and uses `Update()` to fetch and rebase existing repositories.
- A `submodules.php` question may need the `update.php` document because control passes there, but an `update.php` question should not require reading the `submodules.php` document unless the caller relationship is directly relevant.
- File-level As-Is documents for initialization scripts should focus on call order and branch points. For each called file or function, describe only the immediate responsibility and link to the dedicated document for internal details.
- Most framework files are package repositories installed as submodules in the OP package sense. The skeleton root directly owns mainly initialization/bootstrap-for-install files such as `asset/init/` and the dependency map under `asset/config/submodule/**`; CORE, UNIT, MODULE, LAYOUT, bootstrap, and template are package areas, and `asset/core/` intentionally contains Git-managed nested submodules.
- Documentation file names should be sort-friendly for humans. When a document belongs to a category, put the category term first or near the beginning, such as `ci-logout-form-clear.md` for a CI trouble note, so sorted directory listings group related documents together. This is a general category-first naming rule, not only a CI rule.
- ONEPIECE Framework coding rules require method closing braces to include the exact method name as a trailing comment, such as `} // Bar`, to reduce unrelated Git diff ranges and lower rebase/merge conflict risk.
- For local static singleton variables, initialize with an explicit `if(!$variable)` block and return the variable afterward; avoid compact `return $variable ??= new Class();` expressions because Eclipse can warn that the variable is unused.
- Keep agent documentation lookup proportional to the task. For small edits, read nearby owner docs first. For new UNIT/MODULE creation, major restructuring, CI layout work, namespace placement, or memory/loading design, read `asset/docs/op/unit-module-authoring.md`, `asset/docs/op/coding-rules.md`, `asset/docs/cicd/ci-file-layout.md`, `asset/docs/op/common-recipes.md`, and `asset/docs/op/design-philosophy.md`.
- UNIT/MODULE code should avoid unnecessary classes and methods, avoid splitting one-call methods without real benefit, keep normal request memory small, keep entry files thin, keep helper classes in package subnamespaces, treat visible `*.class.php` files as CI targets, use split CI files by class and method, return only the minimum value needed by callers, use `OP_SESSION` and `self::Session()` for package-scoped session state, use `OP()->Session()` only when the shared `\OP\Session` facade wrapper is the intended storage scope, avoid raw `$_SESSION`, and keep detailed docs in responsible documents rather than `AGENTS.md`.
- `OP_SESSION` is documented in `asset/core/trait/docs/op-session.md`.
