# Capture Learning

Capture a learning from the current session using the knowledge MCP server. Do ALL of the following:

## 1. Identify the Learning

Review the conversation for surprising behavior, non-obvious configuration, workarounds, or patterns worth remembering. If the user specified what to capture, use that. Otherwise, summarize what was learned.

## 2. Gather Metadata

Determine these fields from context (ask the user only if ambiguous):

- **Title**: Short descriptive title (sentence case)
- **Body**: 2-4 sentence explanation with the "why" and the fix/workaround
- **Category**: `hardware` | `toolchain` | `pattern` | `operational`
- **Severity**: `critical` (always-loaded gotcha) | `important` (frequently needed) | `informational` (reference)
- **Boards**: Board names this applies to (e.g., `["nrf54l15dk"]`), or omit if cross-platform
- **Chips**: Chip names (e.g., `["nrf54l15"]`), or omit if not chip-specific
- **Tools**: Tool names (e.g., `["probe-rs"]`), or omit if not tool-specific
- **Subsystems**: Subsystem names (e.g., `["coredump", "logging"]`)
- **File patterns**: Glob patterns for auto-injection (e.g., `["**/*coredump*", "**/*.conf"]`)
- **Tags**: 3-5 tags from these conventions:

| Category | Examples |
|----------|----------|
| Chips | `nrf52840`, `nrf54l15`, `esp32`, `esp32s3` |
| Subsystems | `zephyr`, `bluetooth`, `coredump`, `shell`, `dts`, `kconfig` |
| Tools | `probe-rs`, `twister`, `west`, `size-report`, `rtt` |
| Concepts | `flashing`, `testing`, `build-system`, `memory`, `overlay` |
| Platforms | `macos`, `linux`, `qemu` |

## 3. Check for Duplicates

Call `knowledge.search(query)` with the key terms from the learning. If similar items exist, ask the user whether to update the existing item or create a new one.

## 4. Create Knowledge Item

Call `knowledge.capture()` with all the metadata gathered above. The MCP server creates the YAML file and updates the search index.

## 5. Regenerate Rules

Call `knowledge.regenerate_rules()` to update `.claude/rules/*.md` files with the new learning included.

## 6. Summary

Show the user:
- The knowledge item that was created (ID, title, category, severity)
- Which rules file(s) were updated
- Don't commit — let the user decide when to commit
