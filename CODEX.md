# CODEX.md

This file contains Codex CLI-specific notes for working on this repository.

Document roles: boundary details belong in `CUSTOMIZATION_MAP.md`; `AGENTS.md` is the main entry point and general working guide for AI agents; `CODEX.md` is intentionally limited to Codex CLI-specific instructions.

For repository architecture, safe customization boundaries, framework rules, documentation rules, and verification expectations, read `AGENTS.md` first. Do not duplicate those rules here.

## Scope

- `AGENTS.md` is the main guide for all AI coding agents.
- `CUSTOMIZATION_MAP.md` is the source of truth for safe customization points versus framework-managed core zones.
- `CODEX.md` only records Codex CLI workflow details that are not general AI-agent policy.

## Codex CLI Workflow

- Start by reading `AGENTS.md`, then `CUSTOMIZATION_MAP.md` when a change may touch framework-managed packages.
- Use fast local search tools such as `rg` and `rg --files` when inspecting the repository.
- Before editing, check the relevant file and nearby context instead of assuming the target layer.
- Use `apply_patch` for manual edits.
- Do not overwrite or revert user changes in a dirty worktree unless the user explicitly asks.
- Keep Codex changes narrow and easy to review.
- Prefer repository-relative paths in committed documentation.
- After documentation-only changes, a diff review is usually enough; runtime checks are only needed when behavior changed.

## Codex CLI Reporting

When reporting work back to the user:

- Mention files changed.
- Mention checks run, or clearly say that no runtime checks were needed for docs-only changes.
- Call out any pre-existing dirty files that were intentionally left untouched.

## Codex CLI Safety Notes

- Treat CORE and existing framework packages as read-mostly unless the task explicitly targets framework development.
- Do not solve application-specific requests by editing CORE or existing framework UNIT packages.
- If a framework bug is found, prepare the change as a package-level fix or pull request instead of hiding it in application customization.
