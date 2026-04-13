# Firmware Development Guidelines for AI Agents

## Project Overview
This is a **Zephyr RTOS smartwatch firmware** targeting the **nRF52840-based ninab306 custom board**. The firmware integrates multiple sensors, power management, display, and shell interfaces using Zephyr's modular driver and event-based architecture.

## Architecture & Key Components

### Board Definition (`boards/sw/ninab306/`)
- **Target MCU**: nRF52840 (ARM Cortex-M4, 256KB RAM, 1MB Flash)
- **DTS Files**: Define hardware layout (pins, devices, interrupts)
  - `ninab306.dts`: Base device tree (memory partitions, flash layout, console UART)
  - `ninab306.overlay`: Peripheral bindings (display, sensors, buttons, I2C/SPI)
  - `ninab306-pinctrl.dtsi`: Pin control configurations
- **Kconfig Files**: Board-specific configuration options
  - `Kconfig.ninab306`: Select `SOC_NRF52840_QIAA`
  - `Kconfig.defconfig`: LVGL display settings

### Configuration System (`prj.conf`)
**Zephyr Kconfig pattern**: All module features enabled via `CONFIG_*` constants. Key subsystems:
- **Sensors**: `LSM6DSO` (IMU), `MAX32664C` (heart rate), `RV8263` (RTC)
- **Power**: `MFD_NPM13XX` (multi-function device with charging/regulation)
- **Display**: `GC9X01X` (240×240 TFT via MIPI DBI/SPI2)
- **Shell**: `APP_EVENT_MANAGER` + shell for debugging via CDC-ACM UART

### Core Application (`src/main.c`)
- Minimal hello-world template using Zephyr kernel headers
- Includes: sensor, GPIO, I2C, SPI, app_event_manager
- Console output via CDC-ACM virtual UART

## Critical Developer Workflows

### Build System
- **CMake-based** (Zephyr standard): `CMakeLists.txt` delegates to `find_package(Zephyr)`
- **Incremental builds**: Use `Build` terminal task in VS Code
- **Board selection**: Implied from `prj.conf` + DTS files (currently ninab306)
- **Overlay mechanism**: Commented example in `CMakeLists.txt` shows DTC overlay usage

### Flashing & Debugging
Board supports multiple tools (defined in `board.cmake`):
- **J-Link**: `--device=nRF52840_xxAA --speed=4000`
- **PyOCD**: `--target=nrf52840 --frequency=4000000`
- **nRF Util**, OpenOCD available as fallbacks

### Memory Layout
Flash partitions (from `ninab306.dts`):
- `mcuboot`: 48 KB (bootloader)
- `image-0`: 472 KB (active firmware slot)
- `image-1`: 472 KB (backup/DFU slot)
- `storage`: 32 KB (NVRAM for settings)

## Zephyr-Specific Patterns

### Devicetree (DTS) & Pin Configuration
- Pin mappings in `ninab306-pinctrl.dtsi` referenced by peripherals
- Buttons aliased: `navigation=gpio0.16`, `bcklight=gpio0.15`, `clkoe=gpio0.31`
- Display on SPI2 with GPIO reset/DC pins
- I2C0 for sensors (RTC, charger, NPM1300 regulators)

### Peripheral Initialization
- **I2C/SPI**: Enabled via DTS `status = "okay"` + `pinctrl-*` definitions
- **Clocking**: Set via `clock-frequency` in overlay (e.g., I2C_BITRATE_FAST)
- **Power domains**: NPM1300 regulator configured with charging parameters

### App Event Manager
- Zephyr async event dispatch system (enabled in `prj.conf`)
- Used for non-blocking sensor/button handling
- Shell module available for runtime debugging (`APP_EVENT_MANAGER_SHELL`)

## Project Conventions

1. **Configuration over Code**: Hardware behavior controlled via `prj.conf` (Kconfig), not hardcoded
2. **DTS as Source of Truth**: Pin numbers, peripheral addresses, device names defined in `.dts/.overlay`
3. **Minimal Application Logic**: Focus on sensor drivers and event handling; core Zephyr subsystems handle most functionality
4. **CDC-ACM Console**: Debug output via virtual UART (chosen in DTS)

## Common Development Tasks

- **Add a sensor**: Enable CONFIG in `prj.conf`, bind in `.overlay`, include driver header in `.c`
- **Configure GPIO**: Define in overlay `buttons {}` section, alias in `aliases {}`
- **Adjust display**: Modify `mipi_dbi` and `gc9a01` nodes in `.overlay` (width, height, frequency)
- **Change pin mapping**: Edit `ninab306-pinctrl.dtsi` and update `gpios` properties in overlay

## Build Output
- `build/` directory contains cmake artifacts, compile commands, and per-image subdirectories
- Binary locations: `build/firmware/zephyr/zephyr.elf` (main), `zephyr.bin` (flashing)
