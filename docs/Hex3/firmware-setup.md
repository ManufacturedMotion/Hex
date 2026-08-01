# Real-Time Firmware (Xiao RP2350)

Each of Hex3's six legs carries its own [Seeed Xiao RP2350](https://www.seeedstudio.com/Seeed-Studio-XIAO-RP2350-p-5944.html), running the firmware in `real_time/xiao_code`. It drives that leg's three axis motors and encoders, reads the toe (foot contact) sensor, and talks to the Raspberry Pi over CAN.

This page covers building, flashing, and calibrating that firmware. For wiring, see [Electronics Setup](electronics-setup.md).

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
4. Add or select a `#ifdef <YOUR_NAME> ... #endif` calibration block for this leg in `lib/HexapodController/src/config.hpp`, following the [Leg Calibration](#4-leg-calibration) steps to generate the `ZERO_POINTS` values.

## 3. Build & Upload

With the `seeed_xiao_rp2350` environment selected in PlatformIO:

1. **Build** to compile the firmware.
2. **Upload** while the Xiao is connected over USB.
3. Open the PlatformIO **Serial Monitor** at 115200 baud to confirm the board boots and prints its CAN tx/rx node IDs, along with joint position/velocity/acceleration telemetry.

## 4. Leg Calibration

1. Create a user profile in the `config.cpp` file. It should look something like:

    ```cpp
    #ifdef USER
    #define STEP_THRESHOLD 40
    #define FIFO_IDLE_THRESHOLD 100
    #define ZERO_POINTS {                    \
        {0.547631 + CALIBRATION_OFFSET_A0,   \
        -0.836020 + CALIBRATION_OFFSET_A1,  \
        2.787243 + CALIBRATION_OFFSET_A2},  \
        {0.0 + CALIBRATION_OFFSET_A0,   \
        0.0 + CALIBRATION_OFFSET_A1,  \
        0.0 + CALIBRATION_OFFSET_A2}, \
        {0.0 + CALIBRATION_OFFSET_A0,   \
        0.0 + CALIBRATION_OFFSET_A1,   \
        0.0 + CALIBRATION_OFFSET_A2},  \
        {0.0 + CALIBRATION_OFFSET_A0,  \
        0.0 + CALIBRATION_OFFSET_A1,   \
        0.0 + CALIBRATION_OFFSET_A2},  \
        {0.0 + CALIBRATION_OFFSET_A0,   \
        0.0 + CALIBRATION_OFFSET_A1,  \
        0.0 + CALIBRATION_OFFSET_A2},  \
        {0.0 + CALIBRATION_OFFSET_A0,   \
        0.0 + CALIBRATION_OFFSET_A1,  \
        0.0 + CALIBRATION_OFFSET_A2}, \
    }

    ...

    #endif
    ```

2. To calibrate your legs, set both the config values and calibration offsets to 0.0:

    ```cpp
    #define ZERO_POINTS {                    \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0}, \
        {0.0 + 0.0,   \
        0.0 + 0.0,   \
        0.0 + 0.0},  \
        {0.0 + 0.0,  \
        0.0 + 0.0,   \
        0.0 + 0.0},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0}, \
    }
    ```

3. Turn off the hexapod and move the leg to the calibration position and plug in the Xiao RP2350 on the mainboard.

    ![calibration instructions](images/calibration_instructions.png)

    1. S1 should be parallel with the side panel of the hexapod.
    2. S2 should be as far down as it can go. The 3 wires going into S1 must be laid out flush (the distance between S1 and S2 is exactly 1 wire diameter).
    3. S3 should be as far up as it can go (the distance between S2 and S3 is exactly 1 wire diameter).

4. Upload main.cpp and turn on the serial monitor. You should see a value like this:

    ```bash
    {"Joint": {"pos": [0.547631, -0.836020, 2.787243], "vel": [0.000049, 0.006216, 0.000052], "acc": [-0.001082, 4.619407, -0.000955], "duty": [80.000000, -80.000000, -80.000000]}, "voltage": 0.730000, "toe": 0.000000}
    ```

5. Add the code to the leg you are calibrating. For example, if we were calibrating Leg0:

    ```cpp
     #define ZERO_POINTS {                    \
        {0.547631 + CALIBRATION_OFFSET_A0,   \
        -0.836020 + CALIBRATION_OFFSET_A1,  \
         2.787243 + CALIBRATION_OFFSET_A2},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0}, \
        {0.0 + 0.0,   \
        0.0 + 0.0,   \
        0.0 + 0.0},  \
        {0.0 + 0.0,  \
        0.0 + 0.0,   \
        0.0 + 0.0},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0},  \
        {0.0 + 0.0,   \
        0.0 + 0.0,  \
        0.0 + 0.0}, \
    }
    ```

6. Upload the code to the leg and make sure it is in a standing position. (Confirm in `USER_CONFIG.HPP` that you set the correct `LEG_NUMBER`).

7. Repeat for all 6 legs.

## Ad-Hoc Hardware Tests

`test/axis_test.cpp` and `test/can_test.cpp` are standalone bring-up sketches (not PlatformIO unit tests) for exercising a single axis or the CAN transceiver in isolation, independent of the full leg controller — useful when debugging one subsystem at a time.
