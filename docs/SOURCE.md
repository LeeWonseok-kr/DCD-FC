# Corresponding software source

## Official board support

DCD-FC was merged into ArduPilot on 24 November 2026 in [PR #34796](https://github.com/ArduPilot/ardupilot/pull/34796). [Official board definitions](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_HAL_ChibiOS/hwdef/DCD-FC). Merge commit: `68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff`.

## Source for the downloadable development APJ

The final PR revision is [`65c8fa87b3`](https://github.com/LeeWonseok-kr/ardupilot/tree/65c8fa87b3bc72a9f405e52630557c1798a160a3). It changes only the I2C README text relative to the tested `f283ff97` revision; firmware sources, hwdefs and bootloader files are identical. The downloadable APJ remains the tested `f283ff97` build.

[PR #34796](https://github.com/ArduPilot/ardupilot/pull/34796) contains the DCD-FC port. The source used to build the downloadable APJ is [`f283ff97a8`](https://github.com/LeeWonseok-kr/ardupilot/tree/f283ff97a815396f89ef2f4c918b0e65ceedcd0f). The [current APJ](../firmware/DCD_FC_ArduCopter_f283ff97.apj) was built from this commit with GNU Arm Embedded 10.2.1 (10-2020-q4-major).

Clone `https://github.com/LeeWonseok-kr/ardupilot.git`, check out `f283ff97a815396f89ef2f4c918b0e65ceedcd0f`, then run `git submodule update --init --recursive`. Install the ArduPilot build prerequisites and use `./waf configure --board DCD-FC --no-submodule-update --Werror` followed by `./waf copter`. Retain upstream license notices.

The contributor reported F1-F8 motor rotation and RPM reception/change on this image with a Foxeer Reaper 65A ESC on 2026-11-05. See the [test record](TESTING.md) for limits and historical evidence. The current image adds FMU bidirectional DShot; earlier downloads below do not include this update. The committed bootloader BIN and stock F103 IO images are unchanged.

## Earlier development downloads

ArduPilot base commit: `663fc09bb25b84a756d8585e4f6d9266a0ab53ae`.

The [DCD_FC_Corresponding_Source.tar.gz](https://github.com/LeeWonseok-kr/DCD-FC/releases/download/test-2026-10-13/DCD_FC_Corresponding_Source.tar.gz) attachment in this repository's firmware release contains the matching ArduPilot source, initialized submodule sources, DCD FC board definitions and local board-ID entry. No Git history, private development logs or hardware CAD files are included.

The source archive includes `BUILD_DCD_FC.md`. Use GNU Arm Embedded **10.2.1 (10-2020-q4-major)** and the ArduPilot build prerequisites. The build sequence regenerates the regular IO image, DShot IO image, FMU bootloader and ArduCopter application. Retain upstream license notices.

[DCD FC source overlay and build instructions](../archive/source/DCD_FC_Source_Overlay.zip) are also provided for review against the pinned upstream commit. The full archive is the complete download for the matching firmware source.

Board ID **1413** was reserved in [ArduPilot PR #34787](https://github.com/ArduPilot/ardupilot/pull/34787).

## IO bootloader

The [IO bootloader package](../firmware/DCD_FC_IO_Bootloader_Package.zip) contains the separate stock IO bootloader and its matching PX4 source and libopencm3 dependency. [Build instructions](BUILD_IO.md). The unmodified `px4io_bl` build from PX4/PX4-Bootloader commit `6b1f48b69743165929deacfe443acdb2383d0f5a` with GNU Arm Embedded 10.2.1 reproduces the existing 3,732-byte stock binary exactly (SHA-256 `e9ba66410e14dd486f8dc1aff66f059dc5590a21fb4e1608deee08382aacb342`).
