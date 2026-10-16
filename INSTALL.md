# DCD FC firmware installation

ArduCopter 4.8.0-dev · local test board ID 1413.

1. Save the existing parameters and remove propellers.
2. For initial installation, connect an ST-Link to the FMU SWD port (FUUP). In STM32CubeProgrammer, program `DCD_FC_ArduCopter_with_Bootloader.hex` using its embedded addresses, then verify. Refer to the manual for the FUUP pinout. The 3.3 V REF pin is the debugger voltage reference.
3. After installing a matching bootloader, use Mission Planner's **Load custom firmware** with `DCD_FC_ArduCopter_4.8.0-dev.apj`. Do not bypass board-ID checks.
4. The FMU firmware contains standard and DShot IO application images. Use `BRD_IO_ENABLE=1` and reboot to allow IO firmware checking and updates. Retain the compatible existing IO bootloader.
5. Check USB communication, sensors, receiver input and outputs on the ground before operating the aircraft.

The combined image and FMU bootloader are for **FUUP, not IOUP**. For raw BIN programming, the FMU bootloader starts at `0x08000000` and the application at `0x08020000`. HEX files already contain their addresses.

Saved parameters take precedence over firmware defaults. Battery voltage multiplier: `8.987575`; current scale: `0.25 A/V`; analog monitor: `4`; voltage/current pins: `8/4`. The current scale is retained from the existing setup and requires calibration. Onboard current excludes ESC propulsion current.

These development images have passed build and image checks; full physical-board validation is not complete. Board ID 1413 is not an official allocation.
