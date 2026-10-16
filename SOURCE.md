# Corresponding software source

ArduPilot base commit: `663fc09bb25b84a756d8585e4f6d9266a0ab53ae`.

The [DCD_FC_Corresponding_Source.tar.gz](https://github.com/LeeWonseok-kr/DCD-FC/releases/download/test-2026-10-13/DCD_FC_Corresponding_Source.tar.gz) attachment in this repository's firmware release contains the matching ArduPilot source, initialized submodule sources, DCD FC board definitions and local board-ID entry. No Git history, private development logs or hardware CAD files are included.

The source archive includes `BUILD_DCD_FC.md`. Use GNU Arm Embedded **10.2.1 (10-2020-q4-major)** and the ArduPilot build prerequisites. The build sequence regenerates the regular IO image, DShot IO image, FMU bootloader and ArduCopter application. Retain upstream license notices.

[DCD FC source overlay and build instructions](DCD_FC_Source_Overlay.zip) are also provided for review against the pinned upstream commit. The full archive is the complete download for the matching firmware source.

Board ID 1413 is a local test candidate; it is not an official assignment.
