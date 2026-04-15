# Plan: Point marketplace to bitkentech/devostat

## Context

The devostat plugin source repo has moved from `github.com/pramodbiligiri/devostat` to `github.com/bitkentech/devostat`. The bitkentech `releases` branch already holds v0.1.5 at SHA `ec08e946`.

The marketplace file `/opt/workspace/claude-plugins/.claude-plugin/marketplace.json` still references `pramodbiligiri` in three places on the devostat entry, and also in the top-level `owner.url`, even though the marketplace repo itself already lives at `github.com/bitkentech/claude-plugins`. Without this update, the marketplace will keep cloning from a URL that will eventually break or diverge.

Outcome: every `pramodbiligiri/*` reference in `marketplace.json` is replaced with `bitkentech/*`, the devostat source stays on `ref: releases`, and a user installing the plugin via this marketplace ends up pulling from `bitkentech/devostat`.

## Out of scope

- Pinning to a SHA — user chose to keep tracking the `releases` branch (matches current behavior after commit `9767258`).
- Fixing the stale `homepage` / `repository` fields inside devostat's own `plugin.json` on the `releases` branch (still point to `pramodbiligiri/devostat`). That is a devostat-repo concern, not a marketplace concern. Flag only.
- Renaming the marketplace itself (`name: "pramodb-plugins"`) or any directory/branch renames. No evidence requested.

## Backlog reference

`[Linear]` Confirm / create a permanent backlog feature issue for the bitkentech rebrand in the `pramodb-plugins — Backlog & Roadmap` project (or equivalent). The `[agent]` project for this plan must link to it. Candidate title: *"Marketplace: migrate devostat plugin source to bitkentech org"*.

## Files to modify

| File | Change |
|---|---|
| `/opt/workspace/claude-plugins/.claude-plugin/marketplace.json` | Four string replacements (see below) |
| `/opt/workspace/claude-plugins/.agents/plans/plan-04.md` | New — this plan file, committed with the change |
| `/opt/workspace/claude-plugins/.agents/plans/plan-04-tasks.xml` | New — generated via devostat init script |

## Exact edits in marketplace.json

Four distinct strings change, all `pramodbiligiri` → `bitkentech`:

1. `owner.url` — `https://github.com/pramodbiligiri/claude-plugins` → `https://github.com/bitkentech/claude-plugins`
2. `plugins[0].author.url` — `https://github.com/pramodbiligiri/devostat` → `https://github.com/bitkentech/devostat`
3. `plugins[0].source.url` — `https://github.com/pramodbiligiri/devostat.git` → `https://github.com/bitkentech/devostat.git`
4. `plugins[0].homepage` — `https://github.com/pramodbiligiri/devostat` → `https://github.com/bitkentech/devostat`

No other fields (including `ref: "releases"`, `path: "dist"`, `source: "git-subdir"`) change.

## Task breakdown (risk-sorted)

One substantive task — the change is a narrow, reversible config edit with a fast verify loop. Risk ordering is trivial here but we still calibrate.

| # | Task | Actual Risk | Justification |
|---|------|-------|---------------|
| 1 | Update all four `pramodbiligiri` URLs to `bitkentech` in `marketplace.json` | **Medium** (human override; default was Low) | Human calibration. A wrong URL silently breaks downstream plugin installs for every marketplace user, and the only real-world verification is an end-to-end install in a fresh Claude Code session — so treat this via the De-risk & Harden cycle (Step A: prove the releases branch + dist path resolves; Step B: harden verification and commit). |

Risk calibrated with the human: **Medium**. Proceed to generate the task XML.

## Verification

1. **Schema/JSON validity:**
   ```bash
   python3 -m json.tool /opt/workspace/claude-plugins/.claude-plugin/marketplace.json > /dev/null
   ```
   Must exit 0.

2. **No stale references remain:**
   ```bash
   grep -n pramodbiligiri /opt/workspace/claude-plugins/.claude-plugin/marketplace.json
   ```
   Must print nothing.

3. **Target URL resolves on GitHub:**
   ```bash
   git ls-remote https://github.com/bitkentech/devostat.git releases
   ```
   Must return the SHA `ec08e94611c7aaf5156af202737161ec38599bbe` (current v0.1.5 head).

4. **End-to-end install smoke test** — in a scratch Claude Code session, add this marketplace and install `devostat`; confirm the skill files under `skills/devostat` load and the plugin version reads `0.1.5`. If a scratch session isn't available, at minimum run:
   ```bash
   mkdir -p /tmp/devostat-verify && cd /tmp/devostat-verify \
     && git clone --depth 1 --branch releases https://github.com/bitkentech/devostat.git \
     && cat devostat/dist/.claude-plugin/plugin.json | python3 -m json.tool
   ```
   and confirm `"name": "devostat"` and `"version": "0.1.5"`.

5. **Flag (not block):** Note that `dist/.claude-plugin/plugin.json` on the devostat `releases` branch still shows `homepage` and `repository` pointing to `pramodbiligiri/devostat`. File a separate backlog issue against the devostat repo; do not attempt to fix from here.

## Git workflow (per devostat skill)

- Branch: `t/{issue-id}-marketplace-bitkentech-rebrand` (issue id from `[Linear]` step).
- Commit messages follow `task(04): ...` / `plan(04): ...` conventions visible in recent git log.
- Lefthook is not installed in this repo (no `lefthook.yml` in root); **manually tag** each plan push: `plan-04-v1`, `plan-04-complete` on closeout.

## Open decisions recorded

- Task tracking mode: **Linear** for the backlog feature issue, **Local XML** for task state (saved as a memory in `~/.claude/projects/.../memory/devostat_task_mode.md`).
- Ref strategy: **stay on `releases` branch** (no SHA pin).
- URL scope: **devostat entry + top-level `owner.url`** — every `pramodbiligiri` → `bitkentech`.
