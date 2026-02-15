# Session Start

Bootstrap context for an embedded development session. Do ALL of the following:

## 1. Refresh Recent Learnings

The workspace uses a three-tier learnings system:
- **Tier 1** (Key Gotchas in CLAUDE.md) — always in context, no action needed
- **Tier 2** (`.claude/rules/` topic files) — auto-injected when you edit matching files, no action needed
- **Tier 3** (`/recall` skill) — on-demand search, used as needed during the session

To refresh awareness of recent discoveries, list files in `learnings/` for the current year (`learnings/YYYY/`). Read the titles and tags to build a mental index. If there are more than 50 files, read only the last 3 months (by filename date prefix).

## 2. Recent Activity

Run `git log --oneline -10` in each submodule (`zephyr-apps/`, `claude-mcps/`, `claude-config/`) and the workspace root. Summarize what changed recently in 2-3 bullet points total.

## 3. Check Hardware

Run `embedded-probe.list_probes()` to see what's connected. If probes are found, note the probe type and serial. If none, mention it so we know to plug in hardware before debugging.

## 4. Workspace State

Run `git status` in the workspace root and each submodule. Flag any uncommitted changes or stashed work.

## 5. Summary

Present a brief status block:

```
Session Start
- Hardware: [probe type or "none connected"]
- Recent work: [1-2 line summary]
- Uncommitted: [files or "clean"]
- Key gotcha reminder: [most relevant gotcha for likely work]
```

Keep it short. The goal is awareness, not a wall of text.
