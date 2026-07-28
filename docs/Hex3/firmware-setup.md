# Real-Time Firmware (Xiao RP2350)

Each of Hex3's six legs carries its own [Seeed Xiao RP2350](https://www.seeedstudio.com/Seeed-Studio-XIAO-RP2350-p-5944.html), running the firmware in `real_time/xiao_code`. It drives that leg's three axis motors and encoders, reads the toe (foot contact) sensor, and talks to the Raspberry Pi over CAN.

This page covers building and flashing that firmware. For wiring and physically calibrating a leg, see [Electronics Setup](electronics-setup.md#leg-calibration).

## Requirements

- [VS Code](https://code.visualstudio.com/) with the [PlatformIO IDE extension](https://platformio.org/platformio-ide) (this is the recommended extension listed in the project's `.vscode/extensions.json`)
- A Seeed Xiao RP2350, connected via USB

Library dependencies (`RP2040_PWM`, `PID`, `RP2040PIO_CAN`, and the Adafruit `VL6180X` toe-sensor driver) are declared in `platformio.ini` and installed automatically by PlatformIO.

## 1. Open the Project

1. Open VS Code and install the PlatformIO IDE extension if you haven't already.
2. Use **Open Folder** and select `real_time/xiao_code`. PlatformIO will pick up `platformio.ini`, which targets the `seeed_xiao_rp2350` environment.

## 2. Create Your `user_config.hpp`

`user_config.hpp` is intentionally left out of git, since it holds per-board settings, so you need to create your own copy before the firmware will build.

1. Copy `lib/HexapodController/TEMPLATE_user_config.hpp` to `lib/HexapodController/src/user_config.hpp`.
2. Set `LEG_NUMBER` to the leg (`0`-`5`) this board is mounted on. This must be unique per leg — it also determines the board's CAN node ID (`0x100 + LEG_NUMBER` for commands from the Pi, `0x180 + LEG_NUMBER` for replies), which is how the Pi's `can` ROS2 node addresses each leg individually.
3. Set `LOG_LEVEL` and `TELEMETRY_LOGGING_SPACE` for how much serial telemetry you want while debugging.
4. Add or select a `#ifdef <YOUR_NAME> ... #endif` calibration block for this leg in `lib/HexapodController/src/config.hpp`, following the [Leg Calibration](electronics-setup.md#leg-calibration) steps to generate the `ZERO_POINTS` values.

## 3. Build & Upload

With the `seeed_xiao_rp2350` environment selected in PlatformIO:

1. **Build** to compile the firmware.
2. **Upload** while the Xiao is connected over USB.
3. Open the PlatformIO **Serial Monitor** at 115200 baud to confirm the board boots and prints its CAN tx/rx node IDs, along with joint position/velocity/acceleration telemetry.

## Ad-Hoc Hardware Tests

`test/axis_test.cpp` and `test/can_test.cpp` are standalone bring-up sketches (not PlatformIO unit tests) for exercising a single axis or the CAN transceiver in isolation, independent of the full leg controller — useful when debugging one subsystem at a time.
