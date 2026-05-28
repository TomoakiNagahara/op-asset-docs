# AGENTS.md

This file is the main working guide for AI coding agents editing this repository.

The repository-root `AGENTS.md` is intentionally a symlink to `asset/docs/AGENTS.md`. Keep the maintained document in `asset/docs/` and keep the root symlink as the agent discovery entry point.

Document roles: boundary details belong in `asset/docs/CUSTOMIZATION_MAP.md`; `AGENTS.md` is the main entry point and general working guide for AI agents; `asset/docs/CODEX.md` is intentionally limited to Codex CLI-specific instructions.

## Project

This repository is the application skeleton for the ONEPIECE Framework.

ONEPIECE Framework is an Apache-2.0 licensed OSS project.

- `README.md` explains what the project is and how to start it.
- `AGENTS.md` is the root symlink entry point for AI coding agents; maintain its content in `asset/docs/AGENTS.md`.
- `asset/docs/CUSTOMIZATION_MAP.md` is the source of truth for safe customization points versus framework-managed core zones.
- `asset/docs/CODEX.md` contains Codex CLI-specific workflow notes only; general AI-agent policy belongs in `AGENTS.md`.
- Prefer repository-specific working rules over general framework descriptions.

## Customization Boundaries

Before deciding where to edit, read `asset/docs/CUSTOMIZATION_MAP.md`.

Do not duplicate customization boundaries, safe change patterns, or framework-core ownership rules in this file. Keep those details in `asset/docs/CUSTOMIZATION_MAP.md` so agents have one authoritative boundary map.

Use `asset/docs/CUSTOMIZATION_MAP.md` for:

- CORE versus application-owned customization decisions.
- Safe locations for config, routing, templates, layouts, units, and modules.
- Whether app-specific behavior should become a dedicated UNIT or MODULE.
- Whether a framework bug should become a pull request to the responsible package.
- High-impact file guidance for `app.php`, `.htaccess`, bootstrap, init, and submodule config.

## Runtime Flow

For request lifecycle details, read `asset/docs/skeleton/runtime-lifecycle.md` and `asset/docs/CUSTOMIZATION_MAP.md`.

## ONEPIECE Framework Rules

- For pass-through behavior, read `asset/docs/new-world/html-pass-through.md`.
- For template placement, read `asset/docs/skeleton/template-directory.md`.
- For entry-point behavior, read `asset/docs/skeleton/entry-point.md`.
- For framework-wide design intent, read documents under `asset/docs/op/`.

## Coding Rules

- For framework-wide coding rules, including public-name spelling checks, method closing comments, singleton local static variables, config readability, and debugging, read `asset/docs/op/coding-rules.md`.
- Keep documentation clear and concise.
- Keep normal request paths memory-light; see `asset/docs/op/design-philosophy.md` and `asset/docs/op/common-recipes.md`.
- Do not silently hardcode externally maintained data such as CDN, proxy, cloud, or vendor IP ranges. Even if the data is public, it can change over time and creates update burden and operational risk; ask the user before adding such logic, or use an existing trusted source maintained outside application code.
- Before adding or changing JavaScript or CSS, follow `asset/docs/op/frontend-asset-authoring.md`; WebPack-managed JavaScript files should keep file-local code inside a closure.
- Prefer framework APIs over raw PHP superglobals.
- For UNIT/MODULE creation or restructuring, including namespace placement, CI visibility, lazy loading, and path rules, read `asset/docs/op/unit-module-authoring.md`.
- Do not use raw `$_GET`, `$_POST`, `$_REQUEST`, `$_COOKIE`, `$_SESSION`, or `$_SERVER` unless explicitly necessary.
- Use `OP()->Request()` where appropriate.

## Documentation Rules

- Before creating, moving, or restructuring documentation, read `asset/docs/documentation-authoring.md`.
- When the user gives oral or chat instructions that should be preserved, record the original text in the responsible language-specific dictation file such as `dictation.ja.md`, and keep agent-facing guidance in `dictation.md`; use `asset/docs/documentation-authoring.md` to decide whether the framework, UNIT, or MODULE owns that note.
- Keep `AGENTS.md` as a thin routing guide. Put framework-wide, CORE, UNIT, MODULE, CI/CD, and documentation-specific rules in their responsible documents and link to them from here only when needed.

## Git / CI Rules

- Run the project CI before pushing changes.
- Prefer the `cicd` command when available.
- Do not bypass Git hooks unless explicitly requested.
- For UNIT/MODULE class CI rules, read `asset/docs/op/unit-module-authoring.md` and `asset/docs/cicd/ci-file-layout.md`.
- Commit messages should use approved prefixes such as:
  - `New:`
  - `Add:`
  - `Chg:`
  - `Fix:`
  - `Doc:`
  - `Del:`

## Verification

After making changes, run checks that match the risk and scope of the change.

For behavior changes, verify the relevant startup, route, template, layout, and unknown-route behavior described in `asset/docs/CUSTOMIZATION_MAP.md`.

For docs-only changes, a diff review is usually enough.

## Preferred Workflow

1. Inspect the existing structure.
2. Read `asset/docs/CUSTOMIZATION_MAP.md` before choosing an edit location.
3. Identify the smallest safe change.
4. Modify files only in the appropriate customization area.
5. Run checks if available and relevant.
6. Explain what changed and why.

## Troubleshooting

When application startup, routing, rendering, layout, or 404 behavior is wrong, use the runtime flow above and the responsibility map in `asset/docs/CUSTOMIZATION_MAP.md` to choose the first files to inspect.

## Glossary

- `pass-through`
  A design where HTML-oriented files flow through the framework while still allowing PHP execution.
- `UNIT`
  A functional unit used to organize framework or application behavior.
- `Template`
  The file selected for rendering page content.
- `Layout`
  The shared page wrapper and common presentation structure.
- `Auto()`
  The application lifecycle step triggered after bootstrap completes.
