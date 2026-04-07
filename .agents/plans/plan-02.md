# Plan 02: Add skills component field to code-flow marketplace entry

## Background

When installing `code-flow` from the pramodb-plugins marketplace, users see:

> "Component summary not available for remote plugin"

Claude Code reads component configuration fields from the marketplace entry to show a pre-install summary. Without a `skills` field, it cannot enumerate what will be installed. The code-flow plugin has 1 skill at `skills/code-flow/SKILL.md` in its repo.

## Permanent backlog feature issue

[PB-189 — Add skills component field to code-flow marketplace entry](https://linear.app/pb-default/issue/PB-189/add-skills-component-field-to-code-flow-marketplace-entry)

## Change

**File:** `.claude-plugin/marketplace.json`

Add `"skills": "./skills/"` to the `code-flow` plugin object (after `"description"`):

```json
{
  "name": "code-flow",
  "description": "...",
  "skills": "./skills/",
  "author": { ... },
  ...
}
```

## Tasks

### Task 1: Add `skills` field to marketplace.json

- Edit `.claude-plugin/marketplace.json`, insert `"skills": "./skills/"` after the `"description"` field
- Verify JSON is valid: `python3 -m json.tool .claude-plugin/marketplace.json`
- Commit: `task(02): add skills component field to code-flow marketplace entry`

## Notes

- No tests required (pure config update)
- Single-file, single-field addition — no architecture decisions needed

## Verification

1. After commit and push:
2. Run `/plugin marketplace update pramodb-plugins`
3. Uninstall: `claude plugin uninstall code-flow`
4. Reinstall: `claude plugin install code-flow@pramodb-plugins`
5. The install preview should now show the skill count instead of "Component summary not available"
