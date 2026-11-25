# DCD FC firmware installation

[Product overview](README.md) · [한국어](INSTALL_KO.md)

## Normal USB update

DCD FC ships with an ArduPilot-compatible bootloader. A normal update uses a DCD-FC `.apj` file; DFU and ST-Link are not required. FURE and IORE reset the FMU and IO processors.

1. Save your parameters and remove propellers.
2. Download the [development APJ · f283ff97](firmware/DCD_FC_ArduCopter_f283ff97.apj).
3. Connect USB and use Mission Planner's **Load custom firmware** to select the APJ. Do not bypass board-ID checks.
4. Reboot and check communication, sensors, receiver input and output configuration before use.

The current download is ArduCopter 4.8.0-dev, board ID 1413. DCD-FC support is merged, but official downloadable builds were not available when checked on 25 November 2026. See the [official firmware server](https://firmware.ardupilot.org) for later releases. Saved parameters take precedence over firmware defaults.

Keep the compatible stock IO bootloader. The FMU firmware contains the stock standard/DShot IO applications; `BRD_IO_ENABLE=1` allows IO firmware checking and updates after reboot.

## Battery monitoring

For voltage-only monitoring, select `BATT_MONITOR=3`, `BATT_VOLT_PIN=8` and `BATT_VOLT_MULT=8.987575`, then reboot. This is the default in the merged board support; the archived development downloads originally default to `4`. Saved parameters take precedence over firmware defaults.

To optionally view FC supply current, select `BATT_MONITOR=4`, `BATT_CURR_PIN=4`, `BATT_AMP_PERVLT=0.25` and `BATT_AMP_OFFSET=0`, then reboot and calibrate against a reference meter. ESC propulsion current is excluded. Do not use this monitor for total aircraft consumption, remaining capacity or capacity-based failsafes. Use a separate sensor for total battery current.

Board ID 1413 was reserved in ArduPilot PR #34787. See the [test record](docs/TESTING.md) for current and historical firmware results.

## Initial programming and recovery

The procedure below refers to the [archived development firmware](archive/firmware/). It is for a board without a bootloader or for recovery, not a routine USB update. See the [REV8 manual](DCD_FC_Manual_EN_REV8.pdf) for connector pinouts.

1. Save the existing parameters and remove propellers.
2. For initial bootloader programming or recovery, connect an ST-Link to the FMU SWD port (FUUP). In STM32CubeProgrammer, program `DCD_FC_ArduCopter_with_Bootloader.hex` using its embedded addresses, then verify. Refer to the manual for the FUUP pinout. The 3.3 V REF pin is the debugger voltage reference.
3. After installing a matching bootloader, use Mission Planner's **Load custom firmware** with `DCD_FC_ArduCopter_4.8.0-dev.apj`. Do not bypass board-ID checks.
4. The FMU firmware contains standard and DShot IO application images. Use `BRD_IO_ENABLE=1` and reboot to allow IO firmware checking and updates. Retain the compatible existing IO bootloader.
5. Check USB communication, sensors, receiver input and outputs on the ground before operating the aircraft.

The combined image and FMU bootloader are for **FUUP, not IOUP**. For raw BIN programming, the FMU bootloader starts at `0x08000000` and the application at `0x08020000`. HEX files already contain their addresses.

For an IO processor without a bootloader, connect ST-Link to **IOUP** and program `DCD_FC_IO_Bootloader.hex`, then verify. The BIN address is `0x08000000`; the IO application starts at `0x08001000`. This is the unmodified stock PX4 IO bootloader, byte-identical to the existing stock binary. A working IO bootloader does not need replacement. [IO package and matching source](firmware/DCD_FC_IO_Bootloader_Package.zip).
