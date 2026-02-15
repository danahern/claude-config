# Capture Learning

Capture a learning from the current session. Do ALL of the following:

## 1. Identify the Learning

Review the conversation for surprising behavior, non-obvious configuration, workarounds, or patterns worth remembering. If the user specified what to capture, use that. Otherwise, summarize what was learned.

## 2. Gather Metadata

- **Title**: Short descriptive title (sentence case)
- **Date**: Today's date (YYYY-MM-DD)
- **Author**: Run `git config user.name` to get the author name
- **Tags**: Suggest 3-5 tags from these conventions, then confirm with the user:

| Category | Examples |
|----------|----------|
| Chips | `nrf52840`, `nrf54l15`, `esp32`, `esp32s3` |
| Subsystems | `zephyr`, `bluetooth`, `coredump`, `shell`, `dts`, `kconfig` |
| Tools | `probe-rs`, `twister`, `west`, `size-report`, `rtt` |
| Concepts | `flashing`, `testing`, `build-system`, `memory`, `overlay` |
| Platforms | `macos`, `linux`, `qemu` |

## 3. Write Learning File

Create the file at `learnings/YYYY/YYYY-MM-DD-kebab-slug.md`:

```markdown
---
title: Short descriptive title
date: YYYY-MM-DD
author: name
tags: [tag1, tag2, tag3]
---

2-4 sentence explanation with the "why" and the fix/workaround.
```

Create the year directory if it doesn't exist.

## 4. Update Rules Files

Check `.claude/rules/*.md` for topic files whose content relates to this learning. If a matching topic file exists, append a one-liner summary bullet to it. If no matching topic file exists, mention it to the user — don't create a new one without asking.

## 5. Summary

Show the user:
- The file that was created (path + content)
- Which rules file(s) were updated (if any)
- Don't commit — let the user decide when to commit
