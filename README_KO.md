# DCD FC

[English](README.md) · [구매 및 기술지원](https://www.d-class.co.kr)

DCD FC는 Corp.DCLASS의 전원 분배 일체형 비행제어기입니다. 비행 제어, 주변기기 연결, 외부 ESC 4개의 배터리 전원 연결을 한 보드에 통합했습니다.

![DCD FC](DCD_FC_Product.png)

## 주요 특징

- STM32H743 메인 프로세서와 STM32F103 IO 프로세서
- BMI088·ICM-42688-P IMU, MS5611 기압계, IST8310 나침반
- PWM 16채널, GPS 2포트, 텔레메트리 3포트, I2C·SBUS·USB
- FRAM 설정 저장과 microSD 로그 기록
- 입력 전압: 3–6셀 LiPo, 최대 25.2 V
- 케이스 외형: 약 126 × 84 × 13 mm / 케이스 포함 103 g

## 매뉴얼

- [한글 매뉴얼 — REV3](DCD_FC_Manual_KO_REV3.pdf)
- [영문 매뉴얼 — REV3](DCD_FC_Manual_EN_REV3.pdf)

포트 핀 배열, 배선도, 치수, 장착 방향과 전원 사양을 수록했습니다. BAT·ESC 전원 패드 쪽이 Front이며, FC는 양면테이프로 기체에 고정합니다.

내장 전류 센서는 배터리에서 FC·주변기기·PWM 전원으로 공급되는 전류를 측정합니다. ESC 구동 전류는 포함하지 않습니다. 기체 전체 배터리 소모량 측정에는 외부 전류 센서가 필요합니다.

최신 [DCD-FC 보드 지원](https://github.com/ArduPilot/ardupilot/pull/34796)은 `BATT_MONITOR=3`으로 전압만 측정합니다. FC 전류가 필요하면 `BATT_MONITOR=4`, 전압·전류 핀 `8/4`, `BATT_VOLT_MULT=8.987575`, `BATT_AMP_PERVLT=0.25`, `BATT_AMP_OFFSET=0`으로 설정하고 재부팅하세요. 전압·전류 배율은 기준 계측기와 비교하여 보정하세요.

아래 통신·출력 설정은 최신 DCD-FC 보드 지원 기준입니다. 하단의 이전 개발 펌웨어는 기본값·기능이 다를 수 있습니다.

## 리셋 버튼과 상태 표시

| 표시 | 기능 |
|---|---|
| FURE | FMU 메인 프로세서 리셋 |
| IORE | IO 프로세서 리셋 |
| FU LED | FMU 3.3 V 전원, 시동 허용·사전 점검·보정 상태, GPS 상태 표시 |
| IO LED | IO 3.3 V 전원, 순정 IO 펌웨어에서 켜져 있는 링 LED, FMU 통신 두절·페일세이프 표시 |

FURE·IORE는 DFU 진입 버튼이 아닙니다. 비행 중에는 누르지 마세요.

## UART와 USB

| 파라미터 | 연결 | 기본 프로토콜 |
|---|---|---|
| SERIAL0 | USB 첫 번째 가상 포트 | MAVLink |
| SERIAL1 | TLM1 · UART7 · CTS/RTS | MAVLink2 |
| SERIAL2 | TLM2 · UART5 · CTS/RTS | MAVLink2 |
| SERIAL3 | GPS1 · USART1 | GPS |
| SERIAL4 | GPS2 · UART8 | GPS |
| SERIAL5 | TLM3 · USART2 | 미사용 |
| SERIAL6 | USART3 · 외부로 나오지 않음 | 미사용 |
| SERIAL7 | 같은 USB의 두 번째 가상 포트 | MAVLink2 |

모든 UART는 송신·수신 DMA를 사용합니다. USART6는 FMU와 IO 사이의 내부 통신용입니다.

## 펌웨어 설치

ArduPilot 호환 부트로더가 기록된 상태로 출고됩니다. USB를 연결하고 Mission Planner 등 호환 지상국에서 DCD-FC용 .apj 파일을 업로드하세요. 정상 업데이트에는 DFU 진입이나 ST-Link가 필요하지 않습니다.

공식 보드 지원 병합·빌드 공개 후에는 [ArduPilot 공식 펌웨어 서버](https://firmware.ardupilot.org)의 DCD-FC 타깃을 사용합니다.

## PWM과 DShot

16개 출력 모두 PWM과 DShot을 지원합니다. I1~I8의 양방향 DShot은 BRD_IO_DSHOT=1로 설정하고 재부팅하여 사용합니다.

| 보드 출력 | ArduPilot 출력 | 타이머 그룹 |
|---|---|---|
| I1~I2 | SERVO1~2 | IO TIM2 |
| I3~I4 | SERVO3~4 | IO TIM4 |
| I5~I8 | SERVO5~8 | IO TIM3 |
| F1~F4 | SERVO9~12 | FMU TIM1 |
| F5~F6 | SERVO13~14 | FMU TIM4 |
| F7~F8 | SERVO15~16 | FMU TIM5 |

같은 그룹의 채널은 동일한 출력 주기를 사용해야 합니다. 그룹 안에서 한 채널을 DShot으로 사용하면 같은 그룹 전체를 DShot으로 설정하세요.

## 수신기와 RSSI

SBUS 수신기는 S.B의 4번 핀에 연결합니다. PPM은 지원하지 않습니다. CRSF/ELRS 수신기는 TLM3의 TX·RX에 연결하고 SERIAL5_PROTOCOL=23으로 설정합니다.

아날로그 RSSI는 S.B의 3번 핀을 사용하며 RSSI_TYPE=1, RSSI_ANA_PIN=103으로 설정합니다. S.OUT은 IO의 SBUS 출력이며 BRD_SBUS_OUT으로 활성화합니다. SBUS 출력과 아날로그 RSSI는 동시에 사용할 수 없습니다.

## GPIO

| 보드 출력 | GPIO 번호 |
|---|---|
| F1~F8 | 50~57 |
| I1~I8 | 101~108 |

출력 핀을 GPIO로 설정할 때 위 번호를 사용합니다. GPIO는 신호용이며 전원 공급용이 아닙니다.

## 나침반

IST8310 나침반이 내장되어 있습니다. 장착 환경의 자기장 간섭을 고려하여 GPS와 결합된 외부 I2C 나침반 사용을 권장합니다.

## 이전 개발 펌웨어 자료

[Release downloads / 릴리스 다운로드](https://github.com/LeeWonseok-kr/DCD-FC/releases/tag/test-2026-10-13)

아래 다운로드는 기존 ArduCopter 4.8.0-dev 시험용 개발 빌드이며, 당시 기본값은 `BATT_MONITOR=4`입니다. 최신 PR 수정사항은 포함하지 않습니다. 보드 ID **1413**은 [#34787](https://github.com/ArduPilot/ardupilot/pull/34787)에서 예약됐습니다. 전체 실물 시험은 완료되지 않았습니다.

보드 지원이 병합되고 빌드가 공개되면 [ArduPilot 공식 펌웨어 서버](https://firmware.ardupilot.org)에서 `DCD-FC` 타깃을 이용할 수 있습니다. 수정된 포트는 공용으로 관리되는 순정 F103 일반·DShot IO 응용 펌웨어를 사용하며, 호환되는 순정 IO 부트로더는 유지합니다.

| 파일 | 용도 |
|---|---|
| [펌웨어 APJ](DCD_FC_ArduCopter_4.8.0-dev.apj) | 같은 보드 ID의 부트로더에서 Mission Planner로 갱신 |
| [통합 HEX](DCD_FC_ArduCopter_with_Bootloader.hex) | FMU 최초 설치: 부트로더와 펌웨어 |
| [부트로더 HEX](DCD_FC_Bootloader.hex) / [BIN](DCD_FC_Bootloader.bin) | FMU 부트로더 |
| [IO 부트로더 HEX](DCD_FC_IO_Bootloader.hex) / [BIN](DCD_FC_IO_Bootloader.bin) | STM32F103용 순정 IO 부트로더 · IOUP 전용 |
| [IO 부트로더 패키지](DCD_FC_IO_Bootloader_Package.zip) | IO BIN·HEX, 대응 소스와 빌드 안내 |
| [펌웨어 BIN](DCD_FC_ArduCopter_4.8.0-dev.bin) | FMU 응용 펌웨어 원시 이미지 |

[설치 안내](INSTALL_KO.md) · [파일 해시](SHA256SUMS.txt) · [소스 및 빌드 방법](SOURCE.md)

펌웨어는 ArduPilot 기반 GPL-3.0-or-later 소프트웨어이며, 해당 빌드의 소스와 원저작권·라이선스를 함께 제공합니다.

## 구매 및 기술지원

[Corp.DCLASS — www.d-class.co.kr](https://www.d-class.co.kr)
