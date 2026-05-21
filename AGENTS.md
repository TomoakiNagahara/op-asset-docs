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

Keep this request flow in mind:

1. Apache rewrite or direct entry sends the request to `app.php`.
2. `app.php` sets `APP_ROOT` and loads `asset/bootstrap/index.php` if it exists.
3. Bootstrap loads core and config files, then supporting bootstrap includes.
4. `OP()->Unit()->App()->Auto()` continues the application lifecycle.
5. Templates are eventually chosen and rendered through framework routing and template APIs.

## ONEPIECE Framework Rules

- HTML files may be passed through the framework.
- PHP code inside `.html` files may be executed.
- Layout rendering may still be applied to `.html` files.
- Directory-level `index.php` files work as controllers.
- Use `.phtml` for templates.
- Keep routing decisions and template rendering concerns separated.
- Preserve the fallback startup logic in `index.php`.

## Coding Rules

- Write code comments in English.
- Use comments to clarify non-obvious behavior, intent, assumptions, constraints, or risks; avoid comments that only restate self-evident code.
- Keep documentation clear and concise.
- Keep configuration files short and immediately readable. Do not hide long procedural logic, external service data, or large hardcoded lists in config files; move that behavior to an owned function, class, unit, module, or web-server/deployment setting.
- Do not silently hardcode externally maintained data such as CDN, proxy, cloud, or vendor IP ranges. Even if the data is public, it can change over time and creates update burden and operational risk; ask the user before adding such logic, or use an existing trusted source maintained outside application code.
- Before adding or changing JavaScript or CSS, follow `asset/docs/op/frontend-asset-authoring.md`; WebPack-managed JavaScript files should keep file-local code inside a closure.
- Prefer framework APIs over raw PHP superglobals.
- Do not use raw `$_GET`, `$_POST`, `$_REQUEST`, `$_COOKIE`, `$_SESSION`, or `$_SERVER` unless explicitly necessary.
- Use `OP()->Request()` where appropriate.
- Do not use `var_dump()` or `print_r()` for debugging.
- Use `D()` for framework-aware debug output.

## Documentation Rules

- README.md is for humans.
- AGENTS.md is for AI coding agents and is exposed at the repository root through a symlink to `asset/docs/AGENTS.md`.
- CUSTOMIZATION_MAP.md describes safe customization points and framework ownership boundaries; maintain it as `asset/docs/CUSTOMIZATION_MAP.md`.
- CODEX.md is only for Codex CLI-specific workflow notes; maintain it as `asset/docs/CODEX.md`.
- English documents remain the canonical working documents for AI consumption.
- Japanese translations are required because the user reviews document correctness in Japanese and uses the translation to validate whether the English document is accurate.
- When adding a Japanese translation, place it beside the English file and use the `.ja.md` suffix.
- Do not use `asset/docs/ja/`, any `docs/ja/` directory under `asset/core`, `asset/unit`, or `asset/module`, or `asset/docs/spec/` as the default location for new translations.
- OP separates features into individual files such as classes, traits, and functions, and Git commits are usually made per file rather than per multi-file feature bundle.
- This file-by-file commit style works because OP is highly loosely coupled; keep documentation aligned with that model.
- When documentation depends on one separated file, create a document named for that file instead of merging those details into a shared document.
- Keep separated-file documentation split by file name because file-level commits greatly reduce rebase and cherry-pick conflicts.
- When adding, changing, or deleting a feature file, the related CI test and same-named documentation are the other files most likely to be committed with it; matching documentation file names to class or function file names keeps that commit unit clear.
- When documenting current As-Is behavior, place the authoritative details beside the code owner that determines the behavior, such as the responsible class, trait, function, unit, or module docs.
- Do not duplicate the same As-Is implementation detail across documents with different responsibility scopes; documents outside the owner should link or refer to the owner document instead.
- Choose documentation paths from the code owner and responsibility boundary, not only from the page where the topic was first noticed.
- Keep intended specification, current As-Is behavior, and curated gap indexes separated; gap indexes should point to the As-Is owner rather than becoming the detailed owner themselves.
- Use `asset/docs/httpd/` for web-server-related documents.
- Use `asset/docs/cicd/` for framework-level CI/CD philosophy, history, background, and operating-model documents.
- Use `asset/docs/unit/` for philosophy, history, and background of the Unit system itself.
- Use `asset/docs/module/` for philosophy, history, and background of the Module system itself.
- Use `asset/docs/new-world/` for NEW WORLD philosophy, background, and historical documents.
- Use `asset/docs/core/` for op-core philosophy, background, and high-level core feature documents.
- Use `asset/docs/op/` for framework-wide philosophy, design intent, and background of the ONEPIECE Framework.
- Use `asset/docs/skeleton/` for skeleton-specific framework documents.
- If a framework-level document does not fit any of the categories above, store it directly under `asset/docs/`.
- In public repository documents, abstract deployment-specific proper nouns such as real site names, hostnames, subdomains, layout names, user names, and local project names. Use placeholders such as `<site-name>`, `<subdomain>`, `<layout-name>`, or `<project-name>` unless the proper noun is the subject of the document.
- Do not put local absolute file links such as `/System/Volumes/...` into repository documents.
- In repository documents, prefer plain repository-relative paths instead of clickable local-environment file links.
- When a document describes a current problem, mismatch, risk, limitation, or future fix direction, add a searchable tag such as `[DOC-ISSUE]`, `[DOC-RISK]`, `[DOC-GAP]`, `[DOC-FUTURE]`, or `[DOC-PRIORITY1]`.
- Use `[DOC-PRIORITY1]` when the specification is already clear but the current implementation is clearly different.

## Git / CI Rules

- Run the project CI before pushing changes.
- Prefer the `cicd` command when available.
- Do not bypass Git hooks unless explicitly requested.
- For UNIT and MODULE class CI files, follow the split CI file layout in `asset/docs/cicd/ci-file-layout.md`.
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
