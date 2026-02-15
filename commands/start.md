# Session Start

Bootstrap context for an embedded development session. Do ALL of the following:

## 1. Read Learnings

Read all files in the `learnings/` directory (`learnings/*.md`) in the workspace root. Internalize the gotchas — don't repeat past mistakes.

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
