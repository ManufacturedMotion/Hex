# Assembly Instructions

This section walks through building a Hex3 from parts to a walking hexapod.

## Tools and Components

- A full BOM of parts needed can be found [here](https://docs.google.com/spreadsheets/d/1jHR1j3dNIgRCcadYxqtaftRhhn8tEC1XY8og_EQwXEU/edit?usp=sharing).
- Tools required:
    - 2.00mm hex key (allen-key):
        - For use on Raspberry Pi and LiDAR.
    - A 2.5mm hex key (allen-key)
        - We recommend also using a 2.5mm drill bit, as manually tightening all the screws is a herculean task.
        - All the hardware, with the exception of the Raspberry Pi and LiDAR connections use 2.5mm hex.
    - Soldering iron and soldering accessories.
    - Heat set insert bit for soldering iron.
    - Wire cutter
    - Wire stripper for the gauges listed [here](electronics-setup.md#wire-lengths).
    - A clamp
        - Not required but highly recommended for press fits.
    - JST crimp tool
    - Terminal Connector crimp tool
        - Highly recommended but not required as you could directly solder these connections.
    - Misc. pliers, tweezers, etc., for removing supports and other general uses.

- Additional materials required:
    - Multipurpose synthetic grease for gears

## Stages

The build is split into five stages, meant to be followed roughly in order:

1. [3D Printing](3d-printing.md) — print recommendations for the body and legs.
    - Get these prints kicked off and have them running in the background while working on other assembly steps.
2. [Electronics Setup](electronics-setup.md) — power wiring, Raspberry Pi wiring, and wire harness lengths.
    - Once completed, you should have at least:
        - 1x power assembly
        - 1x mainboard
        - 6x leg boards
        - 18x encoders
        - 6x toe time-of-flight sensors (optional; not currently implemented)
3. [Mechanical Assembly](mechanical-assembly.md) — assembling the printed parts and hardware into the hexapod body and legs.
    - Once completed, you should have the hexapod fully assembled, with all 6 legs ready for calibration.
4. [Real-Time Firmware](firmware-setup.md) — building, flashing, and calibrating the per-leg Xiao RP2350 firmware.
5. [Raspberry Pi Configuration](raspberry-pi.md) — flashing the SD card and getting Raspberry Pi OS, Docker, and ROS2 all configured.

Once all five stages are complete, head to [Common Commands](common-commands.md) to get the hexapod moving.
