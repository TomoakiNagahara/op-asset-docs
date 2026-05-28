# Dictation For Agents

## Purpose

This file summarizes user dictation notes for AI agents.

The original dictation text is stored by language:

- Japanese dictation: `dictation.ja.md`
- Other languages: `dictation.<language>.md`

Use this file for agent-facing guidance derived from those language-specific dictation files.

## Current Guidance

- ONEPIECE Framework is intended for developers around the world, not only Japanese speakers.
- Preserve oral or chat instructions in the responsible dictation files.
- Choose the responsible dictation location by ownership scope, not by where the instruction first appeared.
- Framework-wide instructions, AI-agent working rules, documentation-authoring rules, CORE-wide rules, and skeleton-wide rules belong under `asset/docs/`.
- UNIT-specific instructions belong under `asset/unit/<unit>/docs/`.
- MODULE-specific instructions belong under `asset/module/<module>/docs/`.
- Japanese-speaking users' original dictation belongs in `dictation.ja.md`.
- Original dictation from other language speakers belongs in `dictation.<language>.md`.
- Agent-facing summaries and cross-language guidance belong in `dictation.md`.
- Keep `AGENTS.md` thin; it should point agents to the dictation/documentation-authoring rules instead of duplicating them.
- ONEPIECE Framework coding rules require method closing braces to include the exact method name as a trailing comment, such as `} // Bar`, to reduce unrelated Git diff ranges and lower rebase/merge conflict risk.
- For local static singleton variables, initialize with an explicit `if(!$variable)` block and return the variable afterward; avoid compact `return $variable ??= new Class();` expressions because Eclipse can warn that the variable is unused.
- Before UNIT/MODULE coding, read `asset/docs/op/unit-module-authoring.md`, `asset/docs/op/coding-rules.md`, `asset/docs/cicd/ci-file-layout.md`, `asset/docs/op/common-recipes.md`, and `asset/docs/op/design-philosophy.md`.
- UNIT/MODULE code should avoid unnecessary classes and methods, avoid splitting one-call methods without real benefit, keep normal request memory small, keep entry files thin, keep helper classes in package subnamespaces, treat visible `*.class.php` files as CI targets, use split CI files by class and method, return only the minimum value needed by callers, use `OP()->Session()` instead of raw `$_SESSION`, and keep detailed docs in responsible documents rather than `AGENTS.md`.
