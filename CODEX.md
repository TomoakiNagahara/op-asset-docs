# CODEX.md

## Purpose

This file is the working guide for Codex and contributors editing this repository.

- `README.md` explains what the project is and how to start it.
- `CODEX.md` explains how to change it safely.
- `CUSTOMIZATION_MAP.md` maps safe application customization points against framework-managed core zones.
- Prefer repository-specific working rules over general framework descriptions.

## Project Snapshot

This repository is the application skeleton for the ONEPIECE Framework.

- The web entry point is `app.php`.
- `app.php` loads `asset/bootstrap/index.php` when bootstrap files are available.
- After bootstrap, the application proceeds through `OP()->Unit()->App()->Auto()`.
- `index.php` shows the basic pattern for resolving router args and selecting a template.
- Git submodules are required for normal application startup.

## First Files To Read

Read these before making changes:

1. `README.md`
   Setup, philosophy, local run commands.
2. `app.php`
   Entry-point behavior and bootstrap handoff.
3. `index.php`
   Minimal example of routing and template dispatch.
4. `.htaccess`
   Rewrite and pass-through assumptions.
5. `asset/bootstrap/index.php`
   Bootstrap load order and required includes.
6. `asset/config/*`
   Settings entry points for application behavior.

If a task changes startup, routing, or rendering, review all of the files above before editing.

## Runtime Flow

Keep this request flow in mind:

1. Apache rewrite or direct entry sends the request to `app.php`.
2. `app.php` sets `APP_ROOT` and loads `asset/bootstrap/index.php` if it exists.
3. Bootstrap loads core and config files, then supporting bootstrap includes.
4. `OP()->Unit()->App()->Auto()` continues the application lifecycle.
5. Templates are eventually chosen and rendered through framework routing and template APIs.

Important constraints:

- Do not remove the fallback logic at the top of `index.php`.
- Do not change bootstrap include order unless the startup contract truly changes.
- Keep routing decisions and template rendering concerns separated.

## Directory Responsibilities

Use these directories according to their intended roles:

- `asset/bootstrap/`
  Application bootstrap and startup include chain. This area is expected to stay self-contained before OP-CORE is fully available, and its templates are not intended as reusable application templates for other layers.
- `asset/config/`
  Application and environment configuration.
- `asset/layout/`
  Shared layout structure and common page framing.
- `asset/template/`
  Page templates and view-level output. Mainly framework-default templates, not automatically the best place for every user-owned application template.
- `asset/unit/`
  UNIT-based application features and behavior.
- `asset/module/`
  Shared modules, tooling, and framework-related packages.
- `asset/init/`
  Project initialization, submodule setup, and update helpers.

When deciding where to edit:

- Page output changes should start in `asset/template/` or `asset/layout/`.
- Behavior controlled by configuration should start in `asset/config/`.
- Startup changes should stay isolated to bootstrap-related files.
- Treat `asset/bootstrap/` as a pre-core execution area: do not assume OP-CORE features are available there unless the bootstrap itself has already made them available.

## Editing Policy

Follow these rules when making changes:

- Prefer solving tasks in the skeleton layer before changing framework-like internals.
- Keep the HTML pass-through design intact.
- Avoid unrelated refactors while handling a focused task.
- Treat `app.php`, `.htaccess`, and bootstrap files as high-impact files.
- Keep changes local to the smallest responsible layer.
- When documenting a specification, do not stop at behavior alone. If the intent, purpose, expected exceptions, or the reason an exception exists are not already clear from the code or existing docs, ask the user and record the answer.

Practical defaults:

- UI or page changes: start with `asset/template/` and `asset/layout/`.
- The normal framework operating pattern is to create a new site-specific layout under `asset/layout/`.
- Shared templates such as headers, footers, and menus should usually be placed in `asset/layout/<layout-name>/template/`, not in `asset/template/`.
- Current template search order checks `asset/layout/<layout-name>/template/` before `asset/template/`, so layout-specific shared templates can override skeleton-level defaults cleanly.
- Be careful with `asset/template/`: it mainly holds framework-default templates, so putting developer-specific or end-user-specific templates there can make commit ownership and version-control handling awkward unless that tradeoff is intentional.
- Config changes: start with `asset/config/`.
- Routing changes: inspect `index.php`, rewrite assumptions, and router behavior together.
- Bootstrap changes: edit only when startup requirements actually need to change.

Documentation tagging defaults:

- When a document describes a current problem, mismatch, risk, limitation, or future fix direction, add a searchable tag.
- Use these tags consistently: `[DOC-ISSUE]`, `[DOC-RISK]`, `[DOC-GAP]`, `[DOC-FUTURE]`, `[DOC-PRIORITY1]`.
- Use `[DOC-PRIORITY1]` when the specification is already clear but the current implementation is clearly different. Treat these as the highest-priority gaps to be fixed and later re-checked.
- Prefer putting the tag at the start of the relevant heading or paragraph so it can be collected later with text search.
- Do not use `asset/docs/ja/`, any `docs/ja/` directory under `asset/core`, `asset/unit`, or `asset/module`, or `asset/docs/spec/` as the default location for new Japanese translations.
- Japanese translations should live next to the English file and use the suffix `.ja.md`.
- Use `asset/docs/httpd/` for web-server-related documents.
- Use `asset/docs/cicd/` for framework-level CI/CD philosophy, history, background, and operating-model documents.
- Use `asset/docs/unit/` for philosophy, history, and background of the Unit system itself.
- Use `asset/docs/module/` for philosophy, history, and background of the Module system itself.
- Use `asset/docs/new-world/` for NEW WORLD philosophy, background, and historical documents.
- Use `asset/docs/core/` for op-core philosophy, background, and high-level core feature documents.
- Use `asset/docs/op/` for framework-wide philosophy, design intent, and background of the ONEPIECE Framework.
- Use `asset/docs/skeleton/` for skeleton-specific framework documents.
- If a framework-level document does not fit any of the categories above, store it directly under `asset/docs/`.
- Do not put local absolute file links such as `/System/Volumes/...` into repository documents.
- Reason: those paths are specific to the current machine and environment, so they are not stable or portable across other developers' environments.
- In repository documents, prefer plain repository-relative paths written as text instead of clickable local-environment file links.
- Use each unit's own `docs/` directory for As-Is behavior, current implementation details, technical documents, unit-internal flows, encapsulated black-box behavior, and unit-specific implementation problems.
- Use each module's own `docs/` directory in the same way for module-specific As-Is behavior, implementation details, and internal technical documents.
- If a behavior can be used without Core-side or user-side knowledge of the internal implementation, prefer documenting that technical detail inside the responsible unit's `docs/`.
- For CI/CD, keep framework-level philosophy in `asset/docs/cicd/`, but keep concrete current specs and local implementation details in the responsible unit or module `docs/`.
- For NEW WORLD, keep framework-level philosophy in `asset/docs/new-world/`, but keep unit-specific history, background, and current behavior in each responsible unit `docs/`.
- These documents are primarily for AI agents, not for general human-facing product documentation.
- The main purpose of the documents is to give AI a reliable basis for judging what is correct when modifying, refactoring, or adding code.
- English documents remain the canonical working documents for AI consumption.
- Japanese translations are also required because the user reviews document correctness in Japanese and uses the translation to validate whether the English document is accurate.
- Because many documents are written or expanded by AI, assume that mistakes, gaps, or wording mismatches may still exist until reviewed.
- The long-term goal is that humans will not need to learn the entire ONEPIECE Framework exhaustively before using it effectively.
- Instead, a human should be able to ask AI how to use the framework or describe what they want to do, and the AI should be able to give an appropriate answer based on these documents.
- Because of that goal, documents should help AI judge not only how the code currently works, but also what kinds of answers, changes, and implementation decisions are correct within the framework.

## Safe Change Patterns

For the boundary between application-owned customization and framework-managed packages, read
`CUSTOMIZATION_MAP.md` before editing submodule-backed areas such as `asset/core/`,
`asset/bootstrap/`, `asset/unit/`, `asset/module/`, `asset/layout/`,
`asset/template/`, or `asset/webpack/`.

### Adding a Page

- Create or update the relevant template in `asset/template/`.
- Confirm how the route resolves to that template.
- Recheck layout integration if the page uses common framing.
- Verify the 404 path is still correct for unknown routes.

### Updating an Existing Page

- Identify the template actually used by the route.
- Decide whether the change belongs in the page template or shared layout.
- Avoid duplicating common markup that should live in layout files.

### Changing Layout

- Start in `asset/layout/`.
- Prefer shared fixes over copying markup into individual templates.
- Recheck header, footer, and shared head output after changes.

### Changing Configuration

- Use `asset/config/` as the first place to change behavior.
- Prefer the built-in local override pattern when a change is environment-specific:
  use `asset/config/name.php` for shared defaults and `asset/config/_name.php` for local-only overrides.
- Remember that `Config::Get()` loads `name.php` and then `_name.php`, so underscore-prefixed config files can override shared values without editing the shared config directly.
- Use underscore-prefixed config files for machine-local settings such as local admin IPs, local database credentials, or local PHP runtime overrides.
- This pattern also aligns with `.gitignore` rules for `.*` and `_*`, which helps prevent accidental inclusion of local-only config files in a normal `git add .` flow.
- Do not hardcode environment-specific values in templates.
- Keep configuration concerns separate from rendering concerns.

### Changing Routing

- Review `index.php`, router usage, and `.htaccess` together.
- Preserve existing URL behavior unless the task explicitly changes it.
- Re-test both expected routes and unknown routes.

## Constraints And Assumptions

Assume the following project constraints are active:

- UTF-8 is the only officially supported encoding.
- Microsoft Windows is not officially supported.
- WSL may work, but should not be treated as the primary platform contract.
- Git submodules are part of the normal setup.
- Rewrite behavior depends on `.htaccess` and Apache `mod_rewrite`.
- The project targets PHP 8.x.

Editing implications:

- Do not silently change file encodings or line-ending conventions.
- Do not introduce environment assumptions without stating them.
- Prefer changes that remain valid across supported PHP 8.x setups.

## Verification Checklist

After making changes, verify the relevant items:

- `php asset/init/submodules.php` has been run if dependencies are missing.
- `php -S localhost:2030 app.php` starts successfully.
- The top page loads without PHP errors or warnings.
- The intended template renders as expected.
- Unknown routes still resolve to the intended 404 behavior.
- Layout changes do not break shared framing.
- Routing changes do not break existing paths unintentionally.
- Bootstrap changes do not block startup.

## Prohibited Changes

Do not do the following without explicit reason in the task:

- Remove or bypass the startup flow in `app.php`.
- Remove the fallback bootstrap logic at the top of `index.php`.
- Change `.htaccess` rewrite behavior casually.
- Ignore the submodule-based setup requirement.
- Move template-level changes into core behavior without need.
- Mix broad formatting or rename-only edits into functional work.

## Troubleshooting

### Application does not start

- Check whether submodules were initialized.
- Read `app.php` and `asset/bootstrap/index.php`.
- Look for missing files in the bootstrap include chain.

### Request routing is wrong

- Check `.htaccess` rewrite rules and `RewriteBase`.
- Confirm the request reaches `app.php`.
- Recheck router args and template selection.

### Template does not render

- Verify the expected template path exists.
- Confirm route-to-template resolution.
- Check whether a shared layout or config change overrides output.

### Unexpected 404

- Confirm the route is intended to exist.
- Recheck rewrite behavior and router args.
- Compare with the `index.php` pattern for template dispatch.

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
