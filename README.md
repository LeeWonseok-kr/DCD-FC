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

- [English manual — REV6](DCD_FC_Manual_EN_REV6.pdf)
- [Korean manual — REV6](DCD_FC_Manual_KO_REV6.pdf)

The manuals include connector pinouts, wiring, dimensions, installation orientation and power specifications. Front points toward the BAT/ESC pads. Attach the FC to the aircraft with double-sided tape.

The onboard current sensor measures battery current supplied to the FC, peripherals and PWM power rail. It does not measure ESC propulsion current. An external sensor is needed to measure total aircraft battery consumption.

Current [DCD-FC board support](https://github.com/ArduPilot/ardupilot/pull/34796) defaults to `BATT_MONITOR=3` (voltage only). For optional FC current monitoring, set `BATT_MONITOR=4`, voltage/current pins `8/4`, `BATT_VOLT_MULT=8.987575`, `BATT_AMP_PERVLT=0.25` and `BATT_AMP_OFFSET=0`, then reboot. Adjust voltage/current scales against a reference meter.

The settings below describe the current DCD-FC board support. Earlier development firmware listed below may have different defaults or features.

## Reset buttons and individual LEDs

FURE resets the FMU; IORE resets the IO processor. These are not DFU buttons. Do not press them during flight.

Top view of the enclosure with BAT/ESC power pads at the top. Numbers identify the LEDs in this guide.

![LED positions](DCD_FC_LED_Positions.png)

| Position | Function | Indication |
| --- | --- | --- |
| 1 · FU left | FMU status | Arming, pre-arm checks and calibration. See the patterns below. |
| 2 · FU centre | GPS status | Off with no GPS or no fix. After a fix, repeated groups of flashes reflect the satellite count. |
| 3 · FU right | FMU power | On when the FMU 3.3 V supply is present. |
| 4 · IO left | FMU link warning | On if no FMU control data is received for over 200 ms. Off while data is being received normally. |
| 5 · IO centre | IO steady indicator | Held on while stock IO firmware runs. Use LED 4 to check FMU communication. |
| 6 · IO right | IO power | On when the IO 3.3 V supply is present. |

### LED 1 FMU status patterns

| Pattern | State |
| --- | --- |
| Rapid flashing just after startup | Initialising |
| Slow single flashes while disarmed | Pre-arm checks passed |
| Repeated double flashes while disarmed | Pre-arm checks failed; check ground-station messages |
| Steady on | Armed, with no battery, RC or GCS failsafe |
| Flashing while armed | Battery failsafe; faster flashing for RC or GCS failsafe |

LEDs 1 and 2 alternate during trim saving or ESC calibration. Both flash briefly together during compass or IMU temperature calibration.

These are the normal indications while ArduPilot runs. Bootloader, firmware-update and AutoTune notifications use other patterns. Power LEDs alone do not confirm correct operation.

## UART and USB

| Parameter | Connection | Default protocol |
| --- | --- | --- |
| SERIAL0 | First USB virtual port | MAVLink |
| SERIAL1 | TLM1 / UART7 / CTS/RTS | MAVLink2 |
| SERIAL2 | TLM2 / UART5 / CTS/RTS | MAVLink2 |
| SERIAL3 | GPS1 / USART1 | GPS |
| SERIAL4 | GPS2 / UART8 | GPS |
| SERIAL5 | TLM3 / USART2 | None |
| SERIAL6 | USART3 / not pinned out | None |
| SERIAL7 | Second virtual port on the same USB | MAVLink2 |

All UARTs have RX and TX DMA. USART6 is the internal link between the FMU and IO processors.

## Yaapu telemetry

For TLM2 (SERIAL2), set SERIAL2_PROTOCOL=10 (FrSky S.Port Passthrough) and reboot. Follow the receiver or adapter instructions for its S.Port wiring. This telemetry path is separate from the SBUS control input on S.B.

## Loading firmware

The board ships with an ArduPilot-compatible bootloader. Connect USB and upload a DCD-FC .apj file using Mission Planner or another compatible ground station. Normal updates do not require DFU mode or ST-Link.

After board support is merged and builds are published, use the DCD-FC target on the [ArduPilot firmware server](https://firmware.ardupilot.org).

## PWM and DShot

All 16 outputs support PWM and DShot. To use Bi-Directional DShot on I1-I4, set BRD_IO_DSHOT=1 and reboot.

| Board outputs | ArduPilot outputs | Timer group |
| --- | --- | --- |
| I1-I2 | SERVO1-2 | IO TIM2 |
| I3-I4 | SERVO3-4 | IO TIM4 |
| I5-I8 | SERVO5-8 | IO TIM3 |
| F1-F4 | SERVO9-12 | FMU TIM1 |
| F5-F6 | SERVO13-14 | FMU TIM4 |
| F7-F8 | SERVO15-16 | FMU TIM5 |

Channels within the same group must use the same output rate. If any channel uses DShot, all channels in that group must use DShot.

## Receiver and RSSI

Connect SBUS to S.B pin 4. PPM is not supported. Connect a CRSF/ELRS receiver to the TX/RX pins of TLM3 and set SERIAL5_PROTOCOL=23.

For analog RSSI on S.B pin 3, set RSSI_TYPE=1 and RSSI_ANA_PIN=103. S.OUT is the IO SBUS output, enabled with BRD_SBUS_OUT. SBUS output and analog RSSI cannot be used simultaneously.

## GPIO

| Board outputs | GPIO numbers |
| --- | --- |
| F1-F8 | 50-57 |
| I1-I8 | 101-108 |

Use these numbers when configuring output pins as GPIOs. GPIO pins carry signals; they are not power supplies.

## Compass

An IST8310 compass is fitted. Due to potential magnetic interference, an external I2C compass in a GPS/compass module is recommended.

## Board support and testing

[ArduPilot board-support PR #34796](https://github.com/ArduPilot/ardupilot/pull/34796) · [Test record](TESTING.md) · [Source and build instructions](SOURCE.md)

On 2026-10-21, ArduCopter `d2416fe2` passed contributor-reported boot, barometer response, S.B SBUS input and TLM2 Yaapu reception checks. That test image differs from the earlier downloads below. The test record separates current and historical evidence.

## Earlier development firmware

[Release downloads / 릴리스 다운로드](https://github.com/LeeWonseok-kr/DCD-FC/releases/tag/test-2026-10-13)

These downloads are the earlier ArduCopter 4.8.0-dev development test builds, with `BATT_MONITOR=4` as their original default. They do not contain the latest PR changes. Board ID **1413** was reserved in [#34787](https://github.com/ArduPilot/ardupilot/pull/34787). See the [test record](TESTING.md) for the scope of the historical tests.

After board support is merged and builds are published, look for target `DCD-FC` on the [official ArduPilot firmware server](https://firmware.ardupilot.org). The updated port uses maintained stock F103 standard/DShot IO applications; the compatible stock IO bootloader is retained.

| File | Use |
| --- | --- |
| [Firmware APJ](DCD_FC_ArduCopter_4.8.0-dev.apj) | Mission Planner custom firmware update with a matching bootloader |
| [Combined HEX](DCD_FC_ArduCopter_with_Bootloader.hex) | Initial FMU installation: bootloader and firmware |
| [Bootloader HEX](DCD_FC_Bootloader.hex) / [BIN](DCD_FC_Bootloader.bin) | FMU bootloader |
| [IO bootloader HEX](DCD_FC_IO_Bootloader.hex) / [BIN](DCD_FC_IO_Bootloader.bin) | Stock STM32F103 IO bootloader; IOUP only |
| [IO bootloader package](DCD_FC_IO_Bootloader_Package.zip) | IO BIN, HEX, matching source and build instructions |
| [Firmware BIN](DCD_FC_ArduCopter_4.8.0-dev.bin) | Raw FMU application image |

[Installation instructions](INSTALL.md) · [설치 안내](INSTALL_KO.md) · [SHA-256 checksums](SHA256SUMS.txt)

## Software source and license

The firmware is based on ArduPilot and distributed under GPL-3.0-or-later. [Source and build instructions](SOURCE.md) provide the matching software source, dependencies and DCD FC board definitions. Original upstream license notices are retained. See [licensing](LICENSES.md).

## Purchase and technical support

[Corp.DCLASS — www.d-class.co.kr](https://www.d-class.co.kr)
