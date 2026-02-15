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

If any exist, create individual learning files in `learnings/YYYY/YYYY-MM-DD-kebab-slug.md` with YAML frontmatter (title, date, author, tags). Then update any relevant `.claude/rules/` topic files with a one-liner summary. Follow the same workflow as the `/learn` skill.

## 3. Update Documentation

If new apps, libraries, tools, or significant features were created:
- Update the relevant `CLAUDE.md` files (workspace, submodule, or library level)
- Keep workspace CLAUDE.md authoritative for cross-cutting concerns
- Keep submodule/library CLAUDE.md files specialized

## 4. Update Plans

Check `plans/` for any plans that were worked on this session. Update their status if progress was made (e.g., `In-Progress` → `Complete`). Completed plans stay in `plans/` — don't move or archive them.

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
