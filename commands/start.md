# Session Start

Bootstrap context for an embedded development session. Do ALL of the following:

## 1. Refresh Recent Learnings

The workspace uses a knowledge MCP server with three-tier retrieval:
- **Tier 1** (Key Gotchas in CLAUDE.md) — always in context, auto-generated from severity=critical items
- **Tier 2** (`.claude/rules/` topic files) — auto-injected when you edit matching files, auto-generated from knowledge store
- **Tier 3** (`/recall` skill) — on-demand search via `knowledge.search()`

Call `knowledge.recent()` to see what knowledge items were added or updated recently. Summarize any new items briefly.

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
- New knowledge: [count of recent items, or "none"]
```

Keep it short. The goal is awareness, not a wall of text.
