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

- [한글 매뉴얼 — REV2](DCD_FC_Manual_KO_REV2.pdf)
- [영문 매뉴얼 — REV2](DCD_FC_Manual_EN_REV2.pdf)

포트 핀 배열, 배선도, 치수, 장착 방향과 전원 사양을 수록했습니다. BAT·ESC 전원 패드 쪽이 Front이며, FC는 양면테이프로 기체에 고정합니다.

내장 전류 센서는 배터리에서 FC·주변기기·PWM 전원으로 공급되는 전류를 측정합니다. ESC 구동 전류는 포함하지 않습니다. 기체 전체 배터리 소모량 측정에는 외부 전류 센서가 필요합니다.

[PR #34796](https://github.com/ArduPilot/ardupilot/pull/34796)의 수정본은 기본값 `BATT_MONITOR=3`으로 전압만 측정합니다. FC 전류도 확인하려는 사용자는 `BATT_MONITOR=4`, 전압·전류 핀 `8/4`, 전압 배율 `8.987575`, 전류 배율 `0.25 A/V`, 오프셋 `0`으로 설정한 뒤 재부팅하고 계측기로 보정하세요. ESC 전류는 포함하지 않으므로 기체 전체 소비량·잔량이나 용량 기준 경고에 사용하지 마세요. 저장된 설정은 업데이트 후에도 유지되며, 전압만 측정하려면 `3`으로 저장하고 재부팅하세요.

## 부트로더와 펌웨어

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
