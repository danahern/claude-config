# Session Wrap-Up

End-of-session checklist. Do ALL of the following:

## 1. Review Changes

Run `git diff --stat` and `git status` in the workspace root and each submodule (`zephyr-apps/`, `claude-mcps/`, `claude-config/`). Summarize what was accomplished this session.

## 2. Capture Learnings

Review the session for any:
- Surprising behavior or bugs that took multiple attempts to fix
- Non-obvious configuration requirements
- Workarounds for tool/hardware quirks
- Patterns that should be reused

If any exist, append them to `LEARNINGS.md` in the appropriate section. Use the same format as existing entries: a bold heading + 2-3 sentence explanation with the "why" and the fix.

## 3. Update Documentation

If new apps, libraries, tools, or significant features were created:
- Update the relevant `CLAUDE.md` files (workspace, submodule, or library level)
- Keep workspace CLAUDE.md authoritative for cross-cutting concerns
- Keep submodule/library CLAUDE.md files specialized

## 4. Archive Completed Plans

Check `~/.claude/plans/` for plan files from this session. If any plans were completed, move them to `~/.claude/plans/archive/`. Leave plans from other projects untouched.

## 5. Commit Prompt

Show the user a summary of uncommitted changes across all repos and ask if they want to commit and push. If yes, commit each repo with descriptive messages.

## 6. Session Summary

Present a brief wrap-up:

```
Session Summary
- Accomplished: [2-3 bullet points]
- Learnings added: [count, or "none"]
- Docs updated: [files, or "none"]
- Uncommitted: [files, or "all committed"]
```
