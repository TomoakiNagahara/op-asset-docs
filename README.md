# asset/docs

This directory contains the documentation set for the ONEPIECE Framework skeleton.

GitHub displays this file when browsing `asset/docs/`, so this README is a human-facing index for finding the right document.

AI agents should use `AGENTS.md` as their working entry point. Human readers can use this README as a map of the documentation area.

## Primary Entry Points

- `AGENTS.md`
  Working guide and routing entry point for AI coding agents.
- `CUSTOMIZATION_MAP.md`
  Safe customization points and framework-managed boundaries.
- `CODEX.md`
  Codex CLI-specific workflow notes.
- `documentation-authoring.md`
  Rules for creating, moving, and restructuring documentation.
- `important-gaps.md`
  Known gaps, risks, and current problem indexes.
- `to-be.md`
  Future direction and intended improvements.

Japanese translations of framework-level documents are stored next to the English file and use the suffix:

- `.ja.md`

## Topic Areas

- `init/`
  Initialization, submodule setup, update flow, and related helper functions.
- `cicd/`
  CI/CD concepts, Git hooks, push checks, and Git operation rules.
- `skeleton/`
  Skeleton runtime lifecycle, entry point behavior, public roots, and template placement.
- `op/`
  Framework-wide design intent, coding rules, common recipes, and responsibility boundaries.
- `core/`
  Core package concepts and package-owned behavior notes.
- `unit/` and `module/`
  Unit and module system documentation.
- `new-world/`
  HTML pass-through and NEW WORLD design background.
- `httpd/`
  Web-server-related documentation.

## About As-Is and To-Be

As-Is and To-Be should not be mixed carelessly in one place.

The practical guideline is:

- local technical documents should primarily describe As-Is
- local documents may still keep short `[DOC-FUTURE]` notes when necessary
- broader future direction should also be collected in `to-be.md`

The reason is that As-Is and To-Be answer different questions:

- As-Is: what the framework does now
- To-Be: what the framework should become later

Keeping a separate To-Be document makes it easier for both humans and AI to tell:

- what is already true
- what is only planned
- what is a current bug or gap
- what is a future design direction
