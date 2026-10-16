# DCD FC

[한국어](README_KO.md) · [Purchase and technical support](https://www.d-class.co.kr)

DCD FC is a flight controller with integrated power distribution from Corp.DCLASS. It combines flight control, peripheral connections and battery power pads for four external ESCs.

![DCD FC](DCD_FC_Product.png)

## Features

- STM32H743 main processor with a dedicated STM32F103 IO processor.
- BMI088 and ICM-42688-P IMUs, MS5611 barometer and IST8310 compass.
- 16 PWM outputs, two GPS ports, three telemetry ports, I2C, SBUS and USB.
- FRAM parameter storage and microSD logging.
- 3–6S LiPo input, maximum 25.2 V.
- Enclosure: approximately 126 × 84 × 13 mm; 103 g with enclosure.

## Manuals

- [English manual — REV1](DCD_FC_Manual_EN_REV1.pdf)
- [Korean manual — REV1](DCD_FC_Manual_KO_REV1.pdf)

The manuals include connector pinouts, wiring, dimensions, installation orientation and power specifications. Front points toward the BAT/ESC pads. Attach the FC to the aircraft with double-sided tape.

The onboard current sensor measures battery current supplied to the FC, peripherals and PWM power rail. It does not measure ESC propulsion current. An external sensor is needed to measure total aircraft battery consumption.

## Firmware downloads

[Release downloads / 릴리스 다운로드](https://github.com/LeeWonseok-kr/DCD-FC/releases/tag/test-2026-10-13)

These are development test builds of ArduCopter 4.8.0-dev. Board ID 1413 is a local test candidate, not an official ArduPilot allocation. Full hardware validation is not complete.

| File | Use |
|---|---|
| [Firmware APJ](DCD_FC_ArduCopter_4.8.0-dev.apj) | Mission Planner custom firmware update with a matching bootloader |
| [Combined HEX](DCD_FC_ArduCopter_with_Bootloader.hex) | Initial FMU installation: bootloader and firmware |
| [Bootloader HEX](DCD_FC_Bootloader.hex) / [BIN](DCD_FC_Bootloader.bin) | FMU bootloader |
| [Firmware BIN](DCD_FC_ArduCopter_4.8.0-dev.bin) | Raw FMU application image |

[Installation instructions](INSTALL.md) · [설치 안내](INSTALL_KO.md) · [SHA-256 checksums](SHA256SUMS.txt)

## Software source and license

The firmware is based on ArduPilot and distributed under GPL-3.0-or-later. [Source and build instructions](SOURCE.md) provide the matching software source, dependencies and DCD FC board definitions. Original upstream license notices are retained. See [licensing](LICENSES.md).

## Purchase and technical support

[Corp.DCLASS — www.d-class.co.kr](https://www.d-class.co.kr)
