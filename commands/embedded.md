# Embedded Development Guidelines

You are assisting with embedded firmware development. Follow these principles strictly.

## Core Philosophy

**Simplicity over cleverness.** Embedded code runs on constrained devices where predictability matters more than elegance. Write code that a tired engineer can debug at 2am.

## Memory Management

### Static Allocation Only
- **Never use malloc/free/new/delete at runtime**
- Use compile-time sized buffers and Zephyr memory pools
- Define sizes as Kconfig options or constants

```c
// GOOD
static uint8_t rx_buffer[CONFIG_RX_BUF_SIZE];
static K_SEM_DEFINE(data_ready, 0, 1);
K_MSGQ_DEFINE(cmd_queue, sizeof(struct cmd), 8, 4);

// BAD - never do this
uint8_t *buf = malloc(size);
```

### Stack Sizing
- Measure actual usage with `CONFIG_THREAD_ANALYZER`
- Add 25% margin, not 200%
- Document why non-default sizes are needed

## Code Style

### Keep Functions Short
- One function, one purpose
- If it doesn't fit on a screen, split it
- Avoid deep nesting (max 3 levels)

### Use Static Liberally
```c
// File-local functions and variables should be static
static int parse_packet(const uint8_t *data, size_t len);
static uint32_t packet_count;
```

### Error Handling
- **Always check return codes**
- Log errors with context (error code, address, size)
- Fail gracefully - don't crash on recoverable errors

```c
int ret = bt_enable(NULL);
if (ret) {
    LOG_ERR("BT init failed: %d", ret);
    return ret;
}
```

### Logging
- `LOG_INF` - Normal operation milestones
- `LOG_WRN` - Recoverable issues
- `LOG_ERR` - Failures
- `LOG_DBG` - Development only (disable in production)
- Include context: `LOG_ERR("TX failed: addr=0x%08x len=%d err=%d", addr, len, ret);`

## Zephyr Patterns

### Configuration
- Use Kconfig for compile-time options
- Use devicetree for hardware configuration
- Group related configs with comments in prj.conf

### Concurrency
- Prefer work queues over raw threads
- Use `k_sem`, `k_mutex`, `k_msgq` - not POSIX equivalents
- Keep ISRs short - defer to work queue

### Naming
- Follow Zephyr style: `module_action_thing()`
- Examples: `ble_nus_send()`, `wifi_manager_connect()`

## What NOT To Do

- Don't over-abstract - avoid "driver frameworks" for single-use code
- Don't add features "for later" - YAGNI applies doubly to embedded
- Don't ignore compiler warnings
- Don't use blocking calls in callbacks/ISRs
- Don't assume buffer sizes - always bounds check

## Build Practices

- Check memory usage after every build
- Test on real hardware early and often
- Use `--pristine` when changing Kconfig
- Keep build times short - avoid unnecessary dependencies

## MCP Tool Usage

### Building (zephyr-build MCP)
ALWAYS use zephyr-build MCP for builds - saves conversation context:

```
# List what's available
zephyr-build.list_apps()
zephyr-build.list_boards(filter="nrf")

# Build
zephyr-build.build(app="ble_wifi_bridge", board="nrf52840dk/nrf52840", pristine=true)

# Background build for long compiles
zephyr-build.build(app="ble_wifi_bridge", board="esp32s3_eye/esp32s3/procpu", background=true)
zephyr-build.build_status(build_id="...")

# Clean
zephyr-build.clean(app="ble_wifi_bridge")
```

### Flashing & Debugging (embedded-probe MCP)
ALWAYS use embedded-probe MCP for hardware:

```
# Find and connect to probe
embedded-probe.list_probes()
embedded-probe.connect(probe_selector="auto", target_chip="nRF52840_xxAA")

# Flash with boot validation (preferred method)
embedded-probe.validate_boot(
    session_id="...",
    file_path="zephyr-apps/apps/ble_wifi_bridge/build/zephyr/zephyr.elf",
    success_pattern="Booting Zephyr"
)

# Monitor RTT output
embedded-probe.rtt_read(session_id="...")

# Direct flash (without boot validation)
embedded-probe.flash_program(session_id="...", file_path="...zephyr.elf")
embedded-probe.reset(session_id="...", halt_after_reset=false)
```

### Common Chip Mappings
| Board | target_chip |
|-------|-------------|
| nrf52840dk/nrf52840 | nRF52840_xxAA |
| nrf5340dk/nrf5340/cpuapp | nRF5340_xxAA |
| esp32_devkitc/esp32/procpu | ESP32 |
| esp32s3_eye/esp32s3/procpu | ESP32-S3 |

### Typical Workflow
1. `zephyr-build.list_apps()` - find the app
2. `zephyr-build.build(app, board, pristine=true)` - compile
3. `embedded-probe.connect(probe_selector="auto", target_chip="...")` - attach probe
4. `embedded-probe.validate_boot(session_id, file_path, success_pattern="Booting Zephyr")` - flash + verify
5. `embedded-probe.rtt_read(session_id)` - monitor output

## When Reviewing Code

Ask:
1. Is there dynamic allocation? (should be none)
2. Are all return codes checked?
3. Could this block unexpectedly?
4. Is the memory usage reasonable?
5. Can a junior engineer understand this?
