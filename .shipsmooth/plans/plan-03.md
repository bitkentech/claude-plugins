# Plan 03: Update marketplace entry — rename code-flow to devostat, pin to v0.1.3

## Background

The upstream plugin and skill were renamed from `code-flow` to `devostat` (plan-15 in the devostat repo, squash merged at commit `42748b1`, tracked in PB-245). The GitHub repo was also renamed from `code-flow` to `devostat` (PB-244). This repo's marketplace entry in `.claude-plugin/marketplace.json` still references the old name, old URLs, and an outdated SHA.

## Permanent backlog feature issue

[PB-253 — Update marketplace entry: rename code-flow to devostat, update SHA to v0.1.3](https://linear.app/pb-default/issue/PB-253/update-marketplace-entry-rename-code-flow-to-devostat-update-sha-to)

## Risk analysis

| Task | Risk | Justification |
|---|---|---|
| Task 1: Update marketplace.json | Low | Single-file config change; no logic, no tests needed |

## Tasks (risk-sorted)

### Task 1 (Low): Update all devostat fields in marketplace.json

**File:** `.claude-plugin/marketplace.json`

| Field | Old value | New value |
|---|---|---|
| `plugins[0].name` | `"code-flow"` | `"devostat"` |
| `plugins[0].skills[0]` | `"./skills/code-flow"` | `"./skills/devostat"` |
| `plugins[0].author.url` | `"https://github.com/pramodbiligiri/code-flow"` | `"https://github.com/pramodbiligiri/devostat"` |
| `plugins[0].source.url` | `"https://github.com/pramodbiligiri/code-flow.git"` | `"https://github.com/pramodbiligiri/devostat.git"` |
| `plugins[0].source.sha` | `adcbe581794f96d23b4c41326b9d772d70468ab7` | `42748b148d46562345bc5e468a78bbbea3929903` |
| `plugins[0].homepage` | `"https://github.com/pramodbiligiri/code-flow/"` | `"https://github.com/pramodbiligiri/devostat/"` |

Steps:
- Edit `.claude-plugin/marketplace.json` with all field changes above
- Verify JSON is valid: `python3 -m json.tool .claude-plugin/marketplace.json`
- Commit: `task(03): rename code-flow to devostat in marketplace, pin SHA to v0.1.3`
- Push and tag per code-flow workflow

## Notes

- No tests required (pure config update)
- `.agents/plans/plan-01.md` and `plan-02.md` reference `code-flow` — those are historical records and must not be modified
