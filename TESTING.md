# DCD FC test record

[한국어](TESTING_KO.md) · [Product guide](README.md) · [Board-support PR](https://github.com/ArduPilot/ardupilot/pull/34796)

## Current review firmware

Bench results reported by the contributor on 2026-10-21 using ArduCopter 4.8.0-dev `d2416fe2`.

| Check | Result |
| --- | --- |
| Boot | `ArduPilot Ready` |
| Barometer | `Barometer 1 calibration complete`; the contributor observed a pressure-reading response to a pressure change |
| S.B receiver input | `RCInput: decoding SBUS (IOMCU)`; transmitter stick movements changed Mission Planner input bars |
| Transmitter telemetry | Yaapu received data through TLM2/SERIAL2 with `SERIAL2_PROTOCOL=10` (FrSky S.Port Passthrough) |

SBUS carries the control input; the tested Yaapu telemetry used a separate connection. The supplied boot messages and Mission Planner screenshot identify the firmware revision. Pressure response confirms sensor response, not a precision calibration.

The bench configuration reported `Frame: UNSUPPORTED` and `PreArm: Motors: Check frame class and type`. This session does not establish arming, motor operation, DShot operation or flight performance.

The supplied APJ SHA-256 is `e58d15af3e1c11e48f405fc94d2a30237daf9835b1320957b182759ce1fbaf9a`. This is the file hash, not an independently read-back device flash hash.

The reviewed source `c8a883f406c4c6810935e80e6424161a31b301a1` differs from the tested revision only in the README correction restricting IO bidirectional DShot to I1-I4. Runtime code, hwdefs and committed bootloader bytes are unchanged.

## Earlier development tests

These results belong to earlier DCD-FC ArduCopter 4.8.0-dev development firmware and are not repeated tests of the current image.

| Check | Available evidence |
| --- | --- |
| USB/MAVLink and parameters | Recorded connection and parameter retrieval |
| GPS, compass and IMUs | Recorded sensor data and individual IMU roll/pitch responses |
| RC and arming | Recorded SBUS input changes and non-forced arming |
| Outputs and UARTs | Operator reported all 16 outputs, external UARTs, CTS/RTS and applicable non-IOMCU ESC passthrough working |
| FC current | Single-point comparison: 0.322 A on the reference supply and 0.32 A on the FC |

The exact later output/UART test image was not independently read back. A separate high-voltage photograph shows Rover and is not counted as a Copter test. The current sensor excludes ESC propulsion current; the single-point comparison does not establish full-range accuracy.

## Software checks

- The new-board build test passed for Tracker, Blimp, Copter, Heli, Plane, Rover, Sub and the FMU bootloader at `f34c44fc2d415ec5563d88bae02d7d4c7a2ada4c`.
- Copter and the FMU bootloader were rebuilt with `--Werror` at `d2416fe2`.
- Configuration, DMA allocation, APJ identity, image layout and stock IO payload checks passed.
- Branch conventions and README Markdown checks passed after the documentation correction.

[Review summary and build details](https://github.com/ArduPilot/ardupilot/pull/34796) · [Reported bench results](https://github.com/ArduPilot/ardupilot/pull/34796#issuecomment-6295209974)

No flight test is claimed. Raw device identifiers, location screenshots and full telemetry logs are not included in this public record.
