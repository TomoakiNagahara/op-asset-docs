# Documentation Authoring

## Purpose

This document defines how AI coding agents should create, move, and restructure documentation in the ONEPIECE Framework repository.

Agents must read this document before creating documentation.

## Keep AGENTS.md Thin

`AGENTS.md` is the main entry point for AI agents, but it is not the place to collect detailed rules.

Keep only the following in `AGENTS.md`:

- the minimum working guide needed at repository entry
- pointers to documents agents should read
- references for boundaries that agents commonly need first

Put detailed rules in the document that owns that responsibility.

- framework-wide topics: `asset/docs/op/`
- CORE topics: `asset/docs/core/` or owner package docs
- UNIT topics: `asset/docs/unit/` or `asset/unit/<unit>/docs/`
- MODULE topics: `asset/docs/module/` or `asset/module/<module>/docs/`
- general Git operation topics: `asset/docs/git/`
- CI/CD topics: `asset/docs/cicd/`
- skeleton topics: `asset/docs/skeleton/`
- web-server topics: `asset/docs/httpd/`

When you want to add a detailed rule to `AGENTS.md`, first identify the responsible document and add the detail there.
Only add the necessary pointer to `AGENTS.md`.

## Document Roles

- `README.md` is for humans.
- `AGENTS.md` is for AI coding agents and is exposed at the repository root through a symlink to `asset/docs/AGENTS.md`.
- `CUSTOMIZATION_MAP.md` describes safe customization points and framework ownership boundaries; maintain it as `asset/docs/CUSTOMIZATION_MAP.md`.
- `CODEX.md` is only for Codex CLI-specific workflow notes; maintain it as `asset/docs/CODEX.md`.

## Language And Translations

English documents are the canonical working documents for AI consumption.

Japanese translations are required because the user reviews document correctness in Japanese and uses the translation to validate whether the English document is accurate.

When adding a Japanese translation, place it beside the English file and use the `.ja.md` suffix.

Do not use Japanese file names.
All repository documentation file names must be ASCII English.
Japanese content belongs in the `.ja.md` translation file, not in the file name.

Do not use the following as default translation locations:

- `asset/docs/ja/`
- `docs/ja/` under `asset/core`
- `docs/ja/` under `asset/unit`
- `docs/ja/` under `asset/module`
- `asset/docs/spec/`

## File-By-File Documentation

OP separates features into individual files such as classes, traits, and functions, and Git commits are usually made per file rather than per multi-file feature bundle.

This file-by-file commit style works because OP is highly loosely coupled.
Documentation should follow that model.

When documentation depends on one separated file, create a document named for that file instead of merging those details into a shared document.

Keep separated-file documentation split by file name because file-level commits greatly reduce rebase and cherry-pick conflicts.

When adding, changing, or deleting a feature file, the related CI test and same-named documentation are the other files most likely to be committed with it.
Matching documentation file names to class or function file names keeps that commit unit clear.

## Sort-Friendly File Names

Choose documentation file names so that humans can find related documents by ordinary file sorting.

When a document belongs to a recognizable category, put the category term near the beginning of the file name. This groups related documents together in sorted directory listings.

Examples:

- CI-related work notes should use names such as `ci-logout-form-clear.md`, not `logout-form-clear-ci.md`.
- Git-related work notes should start with `git-` when the Git operation is the primary category.
- Template-related work notes should start with `template-` when template behavior is the primary category.

This is not a CI-only rule. It is a general naming rule for human scanability: choose the filename word order from the category a person is likely to browse first, then add the specific subject.

## As-Is / To-Be / Gap

When documenting current As-Is behavior, place the authoritative details beside the code owner that determines the behavior.

Examples:

- responsible class
- trait
- function
- unit
- module

Do not duplicate the same As-Is implementation detail across documents with different responsibility scopes.
Documents outside the owner should link or refer to the owner document instead of copying the details.

Choose documentation paths from the code owner and responsibility boundary, not only from the page where the topic was first noticed.

Keep intended specification, current As-Is behavior, and curated gap indexes separated.
Gap indexes should point to the As-Is owner rather than becoming the detailed owner themselves.

## Usage, Current Specification, And Troubleshooting

When creating agent-facing documentation for CORE features, UNIT packages, MODULE packages, or other reusable framework behavior, separate ordinary usage from source-level current specification.

The purpose is to reduce agent context usage.

Use separate documents by task intent:

- `usage.md`
  Short operational guidance for agents that only need to use the feature.
- `current-spec.md`
  Source-derived As-Is behavior for troubleshooting, behavior investigation, refactoring, or behavior changes.
- `troubleshooting.md`
  Optional focused guidance for common failures, known symptoms, and fast diagnosis paths.

Add matching `.ja.md` translations beside those files when adding Japanese documentation.

`usage.md` should stay small and should include only what an agent needs for normal use:

- how to call the feature
- the smallest useful configuration or code example
- common public methods or entry points
- important responsibility boundaries
- links to deeper documents

`usage.md` should not duplicate source-level internals from `current-spec.md`.

`current-spec.md` should be read when the task involves:

- source-code troubleshooting
- refactoring
- behavior changes
- edge-case behavior
- lifecycle, state, cache, token, session, storage, or request-flow details
- confirming current As-Is behavior from source

If a feature has both documents, agent-facing entry points should link to `usage.md` first.
Only direct agents to `current-spec.md` when the task actually needs source-level detail.

For example, an agent using a UNIT should read that UNIT's `usage.md`.
An agent debugging or refactoring that UNIT should read that UNIT's `current-spec.md`.

## Placement

Choose the documentation location from the responsibility scope.

- web-server-related documents: `asset/docs/httpd/`
- general Git operation documents unrelated to CI/CD: `asset/docs/git/`
- Git documents about CI/CD integration, hooks, and push gates: `asset/docs/cicd/git/`
- framework-level CI/CD philosophy, history, background, and operating-model documents: `asset/docs/cicd/`
- Unit system philosophy, history, and background: `asset/docs/unit/`
- Module system philosophy, history, and background: `asset/docs/module/`
- NEW WORLD philosophy, background, and historical documents: `asset/docs/new-world/`
- op-core philosophy, background, and high-level core feature documents: `asset/docs/core/`
- framework-wide philosophy, design intent, and ONEPIECE Framework background: `asset/docs/op/`
- skeleton-specific framework documents: `asset/docs/skeleton/`

If a framework-level document does not fit any of the categories above, store it directly under `asset/docs/`.

Implementation details for one specific unit or module belong in that package's own `docs/` directory.

## Dictation Notes

When the user explains an instruction orally or in chat and asks agents to preserve it, record it in the responsible dictation files.

Choose the destination by responsibility scope, not by where the topic was first mentioned.

- framework-wide instructions, AI-agent working rules, documentation-authoring rules, CORE-wide rules, and skeleton-wide rules: `asset/docs/`
- UNIT-specific instructions: `asset/unit/<unit>/docs/`
- MODULE-specific instructions: `asset/module/<module>/docs/`

Within the responsible directory, split dictation by language and audience.
ONEPIECE Framework is intended for developers around the world, so do not treat Japanese dictation as the only possible source language.

- `dictation.ja.md`: original dictation from Japanese-speaking users
- `dictation.<language>.md`: original dictation from speakers of other languages
- `dictation.md`: agent-facing summaries, cross-language notes, work notes, and memory aids derived from the language-specific dictation files

Use ASCII English file names.
The language suffix must also be ASCII, such as `dictation.en.md`, `dictation.fr.md`, or `dictation.zh.md`.

`dictation.*.md` files preserve the user's original instruction context and may be written in the speaker's language.
`dictation.md` should be understandable to AI agents and should summarize useful operational notes without replacing the original language-specific record.
It is not required reading for ordinary tasks; agents should consult it only when prior oral-instruction context matters or when the user asks to preserve or check dictation.

When a dictation note becomes a stable rule, move or summarize the durable rule into the responsible canonical document and keep `AGENTS.md` as a thin pointer only.

## Public Repository Safety

In public repository documents, abstract deployment-specific proper nouns such as real site names, hostnames, subdomains, layout names, user names, and local project names.

Use placeholders unless the proper noun itself is the subject of the document.

- `<site-name>`
- `<subdomain>`
- `<layout-name>`
- `<project-name>`

Do not put local absolute file links such as `/System/Volumes/...` into repository documents.

In repository documents, prefer plain repository-relative paths instead of clickable local-environment file links.

## Searchable Tags

When a document describes a current problem, mismatch, risk, limitation, or future fix direction, add a searchable tag.

Examples:

- `[DOC-ISSUE]`
- `[DOC-RISK]`
- `[DOC-GAP]`
- `[DOC-FUTURE]`
- `[DOC-PRIORITY1]`

Use `[DOC-PRIORITY1]` when the specification is already clear but the current implementation is clearly different.
