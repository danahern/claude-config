# Hardware Verification

Guided checklist for hardware verification of a Zephyr application. Walk through each step, using the appropriate MCP tools. Report results as you go.

**Usage:** `/hw-verify <app_name> <board>` (e.g., `/hw-verify wifi_provision nrf7002dk/nrf5340/cpuapp`)

## Arguments

Parse the app name and board from the arguments. If not provided, ask the user.

## Checklist

### 1. Build
- `zephyr-build.build(app=<app>, board=<board>, pristine=true)`
- Verify: build succeeds, note flash/RAM usage from output

### 2. Flash
- For nRF boards: `embedded-probe.nrfutil_program(file_path=<hex_path>)` or `embedded-probe.flash_program(session_id, file_path=<hex_path>)`
- For ESP32 boards: `esp-idf-build.flash(project=<app>, port=<port>)` or `embedded-probe.esptool_flash(port, file_path, chip)`
- Verify: flash completes without errors

### 3. Boot Validation
- For nRF: `embedded-probe.connect()`, then `embedded-probe.validate_boot(session_id, file_path, success_pattern="Booting Zephyr")`
- For ESP32: `esp-idf-build.monitor(project, port, duration_seconds=5)`
- Verify: boot message appears, no crash or fault logged

### 4. Log Inspection
- Read RTT or serial output for 5-10 seconds
- Check for: error messages, warnings, unexpected resets, assertion failures
- Report any issues found

### 5. BLE Discovery (if applicable)
- `hw-test-runner.ble_discover(service_uuid=<uuid>)` or `hw-test-runner.ble_discover()`
- Verify: device appears with expected name and services

### 6. Functional Tests
- Run protocol-specific tests based on the app's purpose
- WiFi provisioning: `hw-test-runner.wifi_provision(ssid, psk)`, then `hw-test-runner.wifi_status()`
- BLE throughput: `hw-test-runner.ble_read()`, `hw-test-runner.ble_write()`
- TCP throughput: `hw-test-runner.tcp_throughput(host, mode="upload")`
- Report: what worked, what failed, any unexpected behavior

### 7. Persistence Test (if applicable)
- Reset the device: `embedded-probe.reset(session_id, halt_after_reset=false)`
- Wait 5 seconds, re-check state (e.g., `hw-test-runner.wifi_status()`)
- Verify: settings/connections persist across reboot

### 8. Factory Reset (if applicable)
- `hw-test-runner.wifi_factory_reset()` or equivalent reset command
- Reset device and verify clean state

### 9. Throughput Baseline (if applicable)
- `hw-test-runner.tcp_throughput(host, mode="upload", duration=10)`
- `hw-test-runner.tcp_throughput(host, mode="download", duration=10)`
- Record: throughput_kbps for each direction

## Summary

After completing applicable steps, present a verification summary:

```
Hardware Verification: <app> on <board>
- Build: [PASS/FAIL] (flash: XX KB, RAM: XX KB)
- Flash: [PASS/FAIL]
- Boot: [PASS/FAIL]
- Logs: [CLEAN / N warnings / N errors]
- BLE Discovery: [PASS/FAIL/SKIP]
- Functional: [PASS/FAIL] (details)
- Persistence: [PASS/FAIL/SKIP]
- Factory Reset: [PASS/FAIL/SKIP]
- Throughput: [XX kbps up / XX kbps down / SKIP]
```

Skip steps that don't apply to the app. Ask the user before proceeding if any step fails — they may want to debug before continuing.
