# Hex3 PCB Electronics

## Overview

This folder contains the Hex3 PCB resources, including the latest leg and main PCB revisions used for this project.

## Leg Board Versions

- `V2.0` - earlier leg board revision.
- `V3.2` - improved leg board revision.

(Both of these boards were used on the hexapods during OpenSauce 2026)

## Key Differences

- `V2.0`:
  - This board added some new features such as:
    - added split termination to our canbus circuit
      - NOTE: our testing showed this feature actually introduced issues. If using the V2.0 design, you will need to cut the traces to remove connection to the split termination circuit. See our GitHub Pages for HEX3 PCB assembly for instructions
      > TODO - add link to page once written
    - added a diode to prevent power issues when connecting to the Xiao RP2350 via USB
    - 470uF capacitor to stabilize power supply to the Xiao RP2350
    - swaps the polarized DRV capacitors from SMT to through hole for easier assembly
      - for some reason, for the original V1.0 boards, we decided to solder the SMT capacitors by hand instead of having it done by a machine...
    - added TJA1050 chip as we swapped the communication protocol for main PCB <-> leg PCB from I2C to canbus
    - added LTC4311 chip to improve I2C transmission speed and reliability (I2C is still used for encoder reads)

- `V3.2`:
  - This board resolved a few design issues from V2.0, but also is more expensive as we swapped to 2 oz copper pours for all layers
  - Changes from V2.0 include:
    - removed split termination from the canbus circuit
    - added via stitching to the board for better heat dissipation
    - added an option to use header/pogo pins for the Xiao RP2350 connectivity 
    - silk screen changes to 0 index the encoder labels
    - swap the placements of the encoder JST connections, so that S0 is the closest to the top of the board
    - swap the through hole diode for an SMT version

> Please see the GitHub Pages for HEX3 PCB assembly for additional notes about assembly and required code changes for either version of the leg board!
> TODO - link page once written

## Main Board Versions

- `V2.1`:
  - The main board is more stable than our leg boards, as V2.1 was the only version we used for OpenSauce 2026
  > Our only note is for the addition of a 470uF polarized capactior across the input terminals of one of the buck converters on the board

## Ordering

PCBs were ordered from JLCPCB, but any PCB vendor that supports ASM should work. Both the main and leg PCBs are designed as 4 layer PCBs

> For convenience and as an example, the BOM and CPL files from our last order have been added only to the `leg_board/V3.2` board folder.
>> Our order was a bulk order for 25 boards. If using these files as a reference, please adjust the quantities accordingly.




