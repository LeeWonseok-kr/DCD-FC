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

## Build DCD-FC firmware from source

You can build ArduCopter before official DCD-FC downloads are published. This procedure uses the official ArduPilot repository at the [DCD-FC merge commit](https://github.com/ArduPilot/ardupilot/commit/68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff). It produces a locally built development firmware, not an official stable release. Its source revision differs from the existing `f283ff97` download.

### 1. Prepare Ubuntu and get the source

Use Ubuntu 22.04 or 24.04 on an x86-64 PC. On Windows, first set up Ubuntu under WSL2 using the [official Windows setup guide](https://ardupilot.org/dev/docs/building-setup-windows11.html). Run the following commands in the **Ubuntu terminal**, as a normal user, not in PowerShell. Keep the source inside the Linux home directory. Use a new `dcd-fc-ardupilot` directory; do not overwrite an existing checkout.

```bash
sudo apt-get update
sudo apt-get install -y git
cd ~
git clone https://github.com/ArduPilot/ardupilot.git dcd-fc-ardupilot
cd dcd-fc-ardupilot
git checkout --detach 68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff
git submodule update --init --recursive
```

The pinned commit already contains `DCD-FC`; no board-file overlay or board-ID edit is needed. Complete each step successfully before continuing.

### 2. Install build prerequisites

From `~/dcd-fc-ardupilot`, run the upstream installer. It installs packages and the Arm toolchain and may ask for your Ubuntu password. Do not prefix the installer or `waf` with `sudo`.

```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
. ~/.profile
arm-none-eabi-gcc --version
```

The compiler installed by this source revision for x86-64 is GNU Arm Embedded 10.2.1 (10-2020-q4-major). If the compiler is not found, reopen Ubuntu and return to `~/dcd-fc-ardupilot` before continuing. [Official Ubuntu setup](https://ardupilot.org/dev/docs/building-setup-linux.html).

### 3. Build ArduCopter

Run from the same repository directory:

```bash
./waf configure --board DCD-FC
./waf copter
ls -lh build/DCD-FC/bin/arducopter.apj
sha256sum build/DCD-FC/bin/arducopter.apj
```

The upload file is **`build/DCD-FC/bin/arducopter.apj`**. Record its SHA-256 if you need to identify this build later. The stock IO application images are included by the board configuration; a separate IO build or IO bootloader replacement is not needed for this procedure.

### 4. Install the generated APJ

On Windows/WSL, open the output directory with:

```bash
explorer.exe build/DCD-FC/bin
```

Copy `arducopter.apj` to a Windows folder, then use Mission Planner's **Load custom firmware** as described above. On Linux, select the same APJ in a compatible ground station. Save parameters and remove propellers before installation; check the board on the ground afterward.

To rebuild the older `f283ff97` download instead, use its pinned source and compiler in [Source and build instructions](docs/SOURCE.md). General build options are documented in the upstream [BUILD.md](https://github.com/ArduPilot/ardupilot/blob/68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff/BUILD.md).

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
