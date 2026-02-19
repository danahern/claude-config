# Build-Flash-Test

Automated inner loop: build an app, flash it to hardware, validate boot, and read output — all in one command.

**Usage:** `/bft <app_name> <board>` (e.g., `/bft wifi_provision nrf54l15dk/nrf54l15/cpuapp`)

## Arguments

Parse the app name and board from the arguments. If not provided, ask the user.

## Board Detection

Determine the platform from the board name to select the right flash/monitor tools:

| Board prefix | Platform | Flash method | Monitor method |
|-------------|----------|-------------|----------------|
| `nrf*` | Zephyr + nRF | `.hex` via `nrfutil_program` | RTT via `rtt_attach` + `rtt_read` |
| `esp32*` | ESP-IDF | `esptool_flash` or `esp-idf-build.flash` | `esp-idf-build.monitor` |
| `stm32*` | Linux/Zephyr | `deploy` via SSH | `ssh_command` |
| `alif*` | Linux | `deploy` via SSH/ADB | `ssh_command` or `adb_shell` |
| `qemu*` | QEMU | (no flash) | Build + run_tests only |
| `mps2*` | QEMU | (no flash) | Build + run_tests only |

## Execution Steps

### Step 1: Build
```
zephyr-build.build(app=<app>, board=<board>, pristine=true)
```
- If build fails, **STOP** and report the error. Do not continue.
- Note flash/RAM usage from build output.

### Step 2: Flash (skip for QEMU/emulated boards)

**nRF boards:**
```
embedded-probe.nrfutil_program(file_path=<build_dir>/zephyr/zephyr.hex)
```
The build output will contain the path. The hex file is at `apps/<app>/build/<board_sanitized>/zephyr/zephyr.hex`.

**ESP32 boards:**
```
esp-idf-build.flash(project=<app>, port=<port>)
```
Use `esp-idf-build.detect_device()` if port is unknown.

**Linux boards (STM32MP1, Alif E7):**
```
linux-build.deploy(file_path=<artifact>, board_ip=<ip>)
```

- If flash fails on nRF, try recovery: `embedded-probe.nrfutil_recover()` then retry.
- If flash fails, **STOP** and report.

### Step 3: Validate Boot (skip for QEMU)

**nRF boards:**
```
embedded-probe.connect(probe_selector="auto", target_chip=<chip>)
embedded-probe.validate_boot(session_id, file_path=<elf_path>, success_pattern="Booting Zephyr")
```

**ESP32 boards:**
```
esp-idf-build.monitor(project=<app>, port=<port>, duration_seconds=5)
```
Check output for boot message.

- If boot validation fails, read RTT/serial for crash info.

### Step 4: Read Output

**nRF boards:**
```
embedded-probe.rtt_attach(session_id)
embedded-probe.rtt_read(session_id)
```
Read for 5-10 seconds. Concatenate chunks. Look for errors/warnings.

**ESP32 boards:**
Monitor output already captured in Step 3.

### Step 5: Report

Present a compact summary:

```
BFT: <app> on <board>
━━━━━━━━━━━━━━━━━━━━━
Build:  PASS (ROM: XX KB, RAM: XX KB)
Flash:  PASS
Boot:   PASS ("Booting Zephyr" seen)
Output: CLEAN (no errors in 5s of RTT)
```

If any step failed, stop at that step and report:

```
BFT: <app> on <board>
━━━━━━━━━━━━━━━━━━━━━
Build:  PASS (ROM: 142 KB, RAM: 38 KB)
Flash:  FAIL — nrfutil_program returned error: "APPROTECT enabled"
        Suggestion: run embedded-probe.nrfutil_recover() then retry
```

## Notes

- For QEMU boards, just build and run tests: `zephyr-build.run_tests(board=<board>, path=<app>)`
- Always disconnect the probe session when done if one was created
- The board sanitized name uses `_` instead of `/` (e.g., `nrf54l15dk_nrf54l15_cpuapp`)
