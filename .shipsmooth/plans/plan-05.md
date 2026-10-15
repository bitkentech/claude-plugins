# Plan: Add shipsmooth-beta to the bitkentech marketplace

## Context

shipsmooth now has a Rust CLI preview published as a separate plugin, `shipsmooth-beta`, on the `releases-beta` branch of `github.com/bitkentech/shipsmooth` (payload under `dist/`). It is a single ~2 MB native binary (no JRE), POSIX only, and installs alongside the stable `shipsmooth` plugin via its own `/shipsmooth-beta:start` command.

The marketplace (`.claude-plugin/marketplace.json`) lists only `shipsmooth` today. Outcome: `/plugin install shipsmooth-beta@bitkentech` works, with no change to the existing `shipsmooth` entry.

Verified on the remote before planning (2026-10-15):
- `releases-beta` exists at `f51e1d2e8372a70e201b6ea1836d56eae206d1d9`.
- `dist/.claude-plugin/plugin.json` on that branch has `"name": "shipsmooth-beta"` (version `0.1.0-beta`).
- `dist/skills/start/SKILL.md` exists on that branch.
- `EXPERIMENTAL.md` exists on `main` (homepage target).

## Out of scope

- Any change to the existing `shipsmooth` entry, including re-wrapping its `"skills"` line (the prepared scratch copy did this; we paste only the new block to keep a one-hunk diff).
- Changing `author.name` in the beta's own `plugin.json` to "Bitken Technologies" (it says "Pramod Biligiri" today; the marketplace entry already says "Bitken Technologies"). The human wants this, but it is generated in the `bitkentech/shipsmooth` repo and ships via `releases-beta`, so it is a follow-up there, not in this repo. Source manifests that carry the old name: `.claude-plugin/plugin.json`, `harness/claude/src/main/resources/claude-plugin/plugin.json`, `harness/codex/.../plugin.json`, `harness/opencode/.../package.json`.

## Backlog reference

None. The human chose local task tracking only for this plan (no Linear issue).

## Files to modify

| File | Change |
|---|---|
| `.claude-plugin/marketplace.json` | Append one plugin object after `shipsmooth` |

## Exact edit

Add a comma after the `shipsmooth` object's closing `}` and append:

```json
{
  "name": "shipsmooth-beta",
  "description": "[BETA] Rust CLI preview of shipsmooth, the same workflow on a single ~2 MB native binary (no JRE). Separate plugin with its own /shipsmooth-beta:start command; can be installed alongside shipsmooth. POSIX only (Linux x64, macOS x64/arm64). Expect rough edges.",
  "skills": ["./skills/start"],
  "author": { "name": "Bitken Technologies", "url": "https://github.com/bitkentech/shipsmooth" },
  "category": "development",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/bitkentech/shipsmooth.git",
    "path": "dist",
    "ref": "releases-beta"
  },
  "homepage": "https://github.com/bitkentech/shipsmooth/blob/main/EXPERIMENTAL.md"
}
```

(Formatted to match the existing entry's multi-line style.)

## Task breakdown (risk-sorted)

### Task 1: Add shipsmooth-beta entry to marketplace.json [Low]

Append the block above. Risk Low (default, not overridden by the human): purely additive, reversible config edit with a fast verify loop; the existing entry is untouched and the remote targets are already verified.

## Verification

1. JSON validity: `python3 -m json.tool .claude-plugin/marketplace.json > /dev/null` exits 0.
2. Diff is a single additive hunk: `git diff .claude-plugin/marketplace.json` shows only the comma and the new object.
3. Both entries present with distinct names/refs:
   `jq -r '.plugins[] | "\(.name) \(.source.ref)"' .claude-plugin/marketplace.json` → `shipsmooth releases`, `shipsmooth-beta releases-beta`.
4. Smoke test (human, after push): `/plugin marketplace update bitkentech` then `/plugin install shipsmooth-beta@bitkentech`; confirm `/shipsmooth-beta:start` is available.
