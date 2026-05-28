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
