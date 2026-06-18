# Commit Message Prefixes

Use the prefix that describes the purpose of the commit.

- `New:` creates a new file or component.
- `Add:` adds functionality or configuration to an existing target.
- `Chg:` intentionally changes a specification, configuration, or behavior.
- `Fix:` corrects a defect or unintended behavior.
- `Doc:` adds or changes documentation.
- `Del:` removes a file, function, configuration, or other existing target.
- `Mov:` moves a file, process, responsibility, or other existing target.

Repository-, branch-, and year-specific prefixes are separate from these change-type prefixes. Their availability is determined by repository configuration and hook behavior.

For CI/CD-related prefix validation during rebase and push, see `asset/docs/cicd/git/rules.md`.
