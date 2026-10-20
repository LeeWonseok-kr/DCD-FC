# DCD FC firmware installation

ArduCopter 4.8.0-dev · reserved board ID 1413.

DCD FC ships with an ArduPilot-compatible bootloader. For normal updates, upload a DCD-FC `.apj` over USB; DFU mode and ST-Link are not required. FURE and IORE reset the FMU and IO processors respectively.

The files below are earlier development builds. See the [REV6 manual](DCD_FC_Manual_EN_REV6.pdf) for current board instructions. The ST-Link procedure is for initial programming of a board without a bootloader or for recovery.

1. Save the existing parameters and remove propellers.
2. For initial bootloader programming or recovery, connect an ST-Link to the FMU SWD port (FUUP). In STM32CubeProgrammer, program `DCD_FC_ArduCopter_with_Bootloader.hex` using its embedded addresses, then verify. Refer to the manual for the FUUP pinout. The 3.3 V REF pin is the debugger voltage reference.
3. After installing a matching bootloader, use Mission Planner's **Load custom firmware** with `DCD_FC_ArduCopter_4.8.0-dev.apj`. Do not bypass board-ID checks.
4. The FMU firmware contains standard and DShot IO application images. Use `BRD_IO_ENABLE=1` and reboot to allow IO firmware checking and updates. Retain the compatible existing IO bootloader.
5. Check USB communication, sensors, receiver input and outputs on the ground before operating the aircraft.

The combined image and FMU bootloader are for **FUUP, not IOUP**. For raw BIN programming, the FMU bootloader starts at `0x08000000` and the application at `0x08020000`. HEX files already contain their addresses.

For an IO processor without a bootloader, connect ST-Link to **IOUP** and program `DCD_FC_IO_Bootloader.hex`, then verify. The BIN address is `0x08000000`; the IO application starts at `0x08001000`. This is the unmodified stock PX4 IO bootloader, byte-identical to the existing stock binary. A working IO bootloader does not need replacement. [IO package and matching source](DCD_FC_IO_Bootloader_Package.zip).

For voltage-only monitoring, select `BATT_MONITOR=3`, `BATT_VOLT_PIN=8` and `BATT_VOLT_MULT=8.987575`, then reboot. This is the default in the updated board-support PR; the older downloads here originally default to `4`. Saved parameters take precedence over firmware defaults.

To optionally view FC supply current, select `BATT_MONITOR=4`, `BATT_CURR_PIN=4`, `BATT_AMP_PERVLT=0.25` and `BATT_AMP_OFFSET=0`, then reboot and calibrate against a reference meter. ESC propulsion current is excluded. Do not use this monitor for total aircraft consumption, remaining capacity or capacity-based failsafes. Use a separate sensor for total battery current.

Board ID 1413 was reserved in ArduPilot PR #34787. See the [test record](TESTING.md) for current and historical firmware results.
