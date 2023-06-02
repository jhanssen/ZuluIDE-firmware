ZuluIDE™ Firmware
=================

ZuluIDE™ is a hardware interface between IDE bus and SD cards.
Currently it supports emulating ATAPI CD-ROM drives, by providing read-only access to image files stored on SD card.

Image files
-----------
Currently `.iso` image files are supported. First image alphabetically is used.

Log files and error indications
-------------------------------
Log messages are stored in `zululog.txt`, which is cleared on every boot.
Normally only basic initialization information is stored, but switching the `DBG` DIP switch on will cause every SCSI command to be logged, once the board is power cycled.

The indicator LED will normally report disk access.
It also reports following status conditions:

- 1 fast blink on boot: Image file loaded successfully
- 3 fast blinks: No images found on SD card
- 5 fast blinks: SD card not detected
- Continuous morse pattern: firmware crashed, morse code indicates crash location

In crashes the firmware will also attempt to save information into `zuluerr.txt`.

Hotplugging
-----------
The firmware supports hot-plug removal and reinsertion of SD card.
The status led will blink continuously when card is not present, then blink once when card is reinserted successfully.

When SD card is removed, the CD drive is reported as being empty. (**TODO**: Implement and test)

Programming & bootloader
------------------------
For RP2040-based boards, the USB programming uses `.uf2` format file that can be copied to the virtual USB drive that shows up in USB bootloader mode, which is enabled with DIP switch.

- There is a custom SD-card bootloader that loads new firmware from SD card on boot. (**TODO**: Re-enable and test)
- The firmware file name must be e.g. `ZuluIDE.bin` or `ZuluIDEv1_0_2023-xxxxx.bin`.
- Firmware update takes about 1 second, during which the LED will flash rapidly.
- When successful, the bootloader removes the update file and continues to main firmware.

DIP switches
------------
There are 4 DIP switches on board:

1. **CABLE SEL**: Enable cable selection of IDE drive id. (**TODO**: Implement and test)
2. **PRI/SEC**: When CABLE SEL is off, this DIP switch determined IDE drive id. Set to OFF for primary drive and ON for secondary. (**TODO**: Implement and test)
3. **DEBUG**: Enable debug log messages
4. **BOOTLDR**: Enable USB bootloader. For normal functionality of board this switch must be off.

Project structure
-----------------
- **src/ZuluIDE.cpp**: Main program, initialization.
- **src/ZuluIDE_log.cpp**: Simple logging functionality, uses memory buffering.
- **src/ZuluIDE_config.h**: Some compile-time options, usually no need to change.
- **src/ide_xxxx.cpp**: High-level implementation of IDE and ATAPI protocols.
- **lib/ZuluIDE_platform_RP2040**: Platform-specific code for RP2040. Includes low level IDE bus access code.
- **lib/minIni**: Ini config file access library

Building
--------
This codebase uses [PlatformIO](https://platformio.org/).
To build run the command:

    pio run


Origins and License
-------------------

This firmware is original work developed by Rabbit Hole Computing™.
It is closely related to [ZuluSCSI](https://github.com/ZuluSCSI/ZuluSCSI-firmware), but starts from fresh beginnings.

The firmware code is licensed under the GPL version 3 or any later version.

The RP2040 platform utilizes a separate ICE5LP1K FPGA for IDE bus communication.
Bitstream for the FPGA is provided in binary format and is licensed for free use and distribution on hardware produced by Rabbit Hole Computing™.
Communication between the FPGA and the CPU uses QSPI interface documented in [rp2040_fpga.h](lib/ZuluIDE_platform_RP2040/rp2040_fpga.h).