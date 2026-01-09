# Copilot / AI agent instructions for this repository ✅

This file contains concise, actionable guidance to help code-writing agents be immediately productive in this Zephyr/Nordic (NCS) firmware repository.

## Big picture 🔧
- This is a Zephyr-based firmware project (Nordic/NCS additions). The app lives in the repository root; board support files are under `boards/sw/ninab306/`.
- A bootloader (mcuboot) and several Zephyr modules from the Nordic Connect SDK (NCS) are integrated (see `build/sysbuild_modules.txt` and `build/zephyr_modules.txt`).
- The build output goes to `build/` and includes `build/firmware/zephyr/zephyr.bin` and `.hex`.

## Key files and what to edit 📂
- `CMakeLists.txt` — project / target sources. Add files to `src/` and reference them here for non-Zephyr-managed sources.
- `src/main.c` — example app using Zephyr APIs (`device`, `devicetree`, drivers).
- `prj.conf` — primary place to enable Kconfig options (drivers/features). Prefer toggling features here instead of scattering #ifdefs in code.
- `boards/sw/ninab306/` — board definition, device tree sources, and `board.cmake` (runner configuration: jlink, nrfjprog, openocd).
- `ninab306_nrf52840.overlay` — project device-tree overlay for the ninab306/nrf52840 board.
- `build/build_info.yml` — contains the exact `west` command used to build (useful as an example). Do not edit `build/` artifacts — they are generated.

## Build / flash / debug workflows (exact examples) ⚙️
- Typical build (local Zephyr/NCS environment):

  ```bash
  # example from build/build_info.yml
  west build --build-dir build . --pristine --board ninab306/nrf52840 -- -DBOARD_ROOT=/path/to/board/root
  ```

- Flash using configured runner (board.cmake contains runner options):
  - Use `west flash` (defaults to runner in `boards/.../board.cmake`)
  - Or explicitly: `west flash --runner jlink` or `west flash --runner nrfjprog`
  - For debugging: `west debug` (JLink or other configured runner)

- Notes: The repo expects an NCS/Zephyr toolchain and proper `ZEPHYR_BASE`; check `build/build_info.yml` for the environment used to produce the `build/` artifacts.

## Project-specific conventions & patterns 🧭
- Feature toggles are centralized in `prj.conf` (Kconfig). Searching for `CONFIG_` in code reveals feature uses. Add features there and rebuild.
- Use DeviceTree and overlay files (e.g., `*.overlay` and `boards/sw/.../*.dts`) to attach/change hardware bindings. Access peripherals via `device_get_binding()` and devicetree macros, not hard-coded pins.
- Board-specific runners and flashing options are placed in `boards/.../board.cmake` (examples: `nrfjprog`, `jlink`, `openocd`). Use the runner set there unless you have a specific reason to override.
- mcuboot is included as a module for bootloading — check `build/` module lists and `boards/.../ninab306.dts` (labels). If adding OTA/bootloader workflows, be mindful of partition layout (`build/partitions.yml`) and mcuboot expectations.

## Tests & CI notes 🔍
- There are platform tests and TF-M/PSA test modules in the NCS tree; this repository itself does not have dedicated unit tests in `src/` by default. To run module tests, use Zephyr/NCS test tooling (e.g., `west build` with appropriate test targets / runners).
- No `.github/workflows` were found; CI configuration is likely outside this repo or handled by upstream NCS workflows.

## What to avoid / gotchas ⚠️
- Do not edit generated `build/` artifacts directly. Rebuild instead.
- Board-specific changes should go into `boards/sw/ninab306/` (DTS/YAML/CMake) not ad-hoc overlays spread around.
- The repo relies on the NCS environment (paths to modules/toolchains in `build/`). Make sure `ZEPHYR_BASE` and NCS variables are set when running builds.

## Useful grep/search hints for agents 🔎
- To find platform/driver usage: search for `#include <zephyr/drivers/` and `device_get_binding(` and `DT_` macros in `src/` and `boards/`.
- To inspect last build command used by the maintainer: open `build/build_info.yml`.

---

If anything is unclear or you want more detail (e.g., how to produce mcuboot-signed images, how OTA is expected to work for this board, or a sample `west` workflow for CI), tell me which area to expand and I will iterate. ✅
