# DCD FC connection and configuration reference

[Product overview](../README.md) · [한국어](REFERENCE_KO.md)

The onboard current sensor measures battery current supplied to the FC, peripherals and PWM power rail. It does not measure ESC propulsion current. An external sensor is needed to measure total aircraft battery consumption.

[DCD-FC board support](https://github.com/ArduPilot/ardupilot/pull/34796) defaults to `BATT_MONITOR=3` (voltage only). For optional FC current monitoring, set `BATT_MONITOR=4`, voltage/current pins `8/4`, `BATT_VOLT_MULT=8.987575`, `BATT_AMP_PERVLT=0.25` and `BATT_AMP_OFFSET=0`, then reboot. Adjust voltage/current scales against a reference meter.

The settings below describe the current DCD-FC board support. Archived development firmware may have different defaults or features.

## Reset buttons and individual LEDs

FURE resets the FMU; IORE resets the IO processor. These are not DFU buttons. Do not press them during flight.

Top view of the enclosure with BAT/ESC power pads at the top. Numbers identify the LEDs in this guide.

![LED positions](../assets/DCD_FC_LED_Positions.png)

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

## Loading firmware

The board ships with an ArduPilot-compatible bootloader. Connect USB and upload a DCD-FC .apj file using Mission Planner or another compatible ground station. Normal updates do not require DFU mode or ST-Link.

See [firmware downloads and installation](../INSTALL.md) for the current download status.

## I2C buses and IMU heater

GPS1 uses bus 1; GPS2 uses bus 2. TLM3 and the I2C port share internal bus 0 with the onboard sensors; do not add devices at 0x0C or 0x77. All three buses use 3.3 V signals and onboard 4.7 kOhm pull-ups. Connector power is 5 V.

Connect GPS/compass modules to GPS1 or GPS2. Bus 0 is not scanned as an external compass bus; QMC5883L/P and IIS2MDC compasses are not detected there by default. For an I2C airspeed sensor on TLM3/I2C, set ARSPD_BUS=0 and reboot. MS4525 can also auto-detect on bus 0 when ARSPD_BUS has not been configured.

Set the IMU heater target with BRD_HEAT_TARG. The default is 45 degrees Celsius.

## PWM and DShot

All 16 outputs support PWM and DShot. F1-F8 support Bi-Directional DShot. For I1-I4, set BRD_IO_DSHOT=1 and reboot. I5-I8 do not support Bi-Directional DShot.

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
