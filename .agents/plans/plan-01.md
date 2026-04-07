# Plan 01: Update code-flow plugin SHA to v0.1.0

## Background

The `code-flow` plugin is listed in `.claude-plugin/marketplace.json` pinned to a specific git commit SHA. The current SHA predates the v0.1.0 release. This plan updates it to the commit tagged `v0.1.0` in the upstream repo.

## Permanent backlog feature issue

[PB-187 — Update code-flow plugin to v0.1.0](https://linear.app/pb-default/issue/PB-187/update-code-flow-plugin-to-v010)

## Change

**File:** `.claude-plugin/marketplace.json`
**Field:** `plugins[0].source.sha` (line 21)

| | Value |
|---|---|
| Current SHA | `fae90358425faa395c052d2ab551a03f1a2c7a3f` |
| Target SHA (v0.1.0) | `069433d09bc59cca50bc0a9907191e85997d1f86` |

The v0.1.0 tag in https://github.com/pramodbiligiri/code-flow points to commit `069433d09bc59cca50bc0a9907191e85997d1f86` (also tagged `plan-08-complete`).

## Tasks

### Task 1: Update SHA in marketplace.json

- Edit `.claude-plugin/marketplace.json`, replace the `sha` value
- Verify JSON is valid: `python3 -m json.tool .claude-plugin/marketplace.json`
- Commit and push

## Notes

- No tests required for this change (pure config update)
- Single-file, single-field change — no architecture decisions needed
