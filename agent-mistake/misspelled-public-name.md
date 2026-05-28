# Agent Mistake: Misspelled Public Name

## Summary

An AI agent created a public module file using a likely misspelled name without first confirming the intended spelling.

The agent noticed the spelling issue only after implementation, when the naming had already become part of the module surface.

## Root Cause

The agent treated the user's spelling as an exact implementation requirement instead of recognizing it as a likely human typo.

That was the wrong default.

Human users can misspell words, especially in short file names or API names.
When a requested name looks like a typo and the name will become a public or durable interface, the agent must confirm the intended spelling before creating it.

## Why This Is Dangerous

File names, method names, class names, config keys, URLs, template names, and documented APIs become durable contracts.

If an agent turns a typo into the primary implementation name, later fixes can require compatibility shims, redirects, documentation cleanup, or breaking changes.

The cost is much higher than asking one short confirmation question before implementation.

## Correct Rule

[DOC-RISK] Do not blindly preserve a likely spelling mistake in a durable name.

Before creating a public or durable name, check whether it is standard English or an established project term.

If it looks wrong, ask the user to confirm the intended spelling before implementation.

Use this rule especially for:

- file names
- class names
- method names
- function names
- config keys
- template names
- route names
- URL parameters
- documentation headings

## Correct Handling

When a requested durable name appears to be misspelled, the agent should reply before editing:

```text
The requested name may contain a spelling mistake.
Should I use the standard spelling, or is the requested spelling intentional?
```

If backward compatibility with the misspelled name is required, keep that explicit:

- primary implementation: standard or confirmed spelling
- compatibility wrapper: misspelled legacy name
- documentation: explain that the legacy name exists only for compatibility

## Prevention

Before implementing a new durable name:

1. Check for obvious typos.
2. Compare against existing project naming.
3. Ask for confirmation when the requested name conflicts with standard spelling.
4. Do not use an unconfirmed spelling as the primary name.
5. If a typo must be supported, make it a documented compatibility alias.
