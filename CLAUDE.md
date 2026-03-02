# Claude Configuration

Shared configuration for the embedded development workspace. Symlinked into `.claude/` via:
- `.claude/agents/` → `claude-config/agents/`
- `.claude/commands/` → `claude-config/commands/`

## Agents (11)

| Agent | Model | Purpose |
|-------|-------|---------|
| `advocate` | sonnet | Proposes concrete solutions during `/deliberate` debates |
| `build-toolchain-expert` | sonnet | Build systems, toolchains, Docker, MCP server debugging |
| `code-review-expert` | sonnet | Code review for correctness, efficiency, domain-appropriate style |
| `critic` | sonnet | Finds flaws, risks, missing assumptions during `/deliberate` debates |
| `embedded-specialist` | sonnet | Embedded firmware: drivers, RTOS patterns, memory, peripherals |
| `hardware-test` | sonnet | Hardware-in-the-loop testing and verification |
| `kernel-reviewer` | sonnet | Linux kernel, driver, device tree, bootloader code review |
| `lab-engineer` | sonnet | Physical lab operations: probes, analyzers, serial consoles |
| `platform-engineer` | sonnet | Board bring-up, BSP, flash layouts, boot sequences |
| `retrospective-analyst` | sonnet | Root cause analysis, post-incident review, process improvement |
| `synthesizer` | opus | Integrates debate into final recommendation for `/deliberate` |

**CRITICAL:** Agents do NOT see the workspace `CLAUDE.md`. Domain knowledge, MCP-first policy, and project conventions must be embedded directly in agent system prompts or accessed via `knowledge.for_context()`.

## Skills (9)

| Skill | Command | Purpose |
|-------|---------|---------|
| `start` | `/start` | Session bootstrap: recent knowledge, git status, hardware check |
| `wrap-up` | `/wrap-up` | Session end: capture learnings, commit, regenerate rules |
| `learn` | `/learn` | Capture a knowledge item with metadata |
| `recall` | `/recall` | Search knowledge by topic, tag, or keyword |
| `deliberate` | `/deliberate <question>` | Multi-agent debate: advocate, critic, synthesizer |
| `embedded` | `/embedded` | Load full embedded development guidelines |
| `bft` | `/bft <app> <board>` | Build, flash, validate boot, read output |
| `hw-verify` | `/hw-verify <app> <board>` | Hardware verification checklist |
| `review` | `/review` | Code review against knowledge base |

## Agent MCP Access

Each agent's `mcpServers` frontmatter field controls which MCP tools it can use. When adding or modifying agents, ensure the MCP list matches what the agent body references. Common pattern:

```yaml
mcpServers: [knowledge, embedded-probe, zephyr-build]
```

## Hooks

- `hooks/enforce-mcp-first.sh` — Blocks CLI commands that have MCP equivalents (JLinkExe, nrfjprog, west build/flash, idf.py, esptool, openocd)
