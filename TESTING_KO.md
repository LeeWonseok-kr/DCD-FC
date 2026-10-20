# DCD FC 시험 기록

[English](TESTING.md) · [제품 안내](README_KO.md) · [보드 지원 PR](https://github.com/ArduPilot/ardupilot/pull/34796)

## 최신 리뷰 펌웨어

2026-10-21 사용자가 ArduCopter 4.8.0-dev `d2416fe2`에서 확인한 지상 시험입니다.

| 항목 | 결과 |
| --- | --- |
| 부팅 | `ArduPilot Ready` |
| 기압계 | `Barometer 1 calibration complete`; 압력 변화에 따라 표시값이 변함을 사용자 확인 |
| S.B 수신기 입력 | `RCInput: decoding SBUS (IOMCU)`; 조종기 스틱 조작 시 Mission Planner 입력 막대 변화 |
| 조종기 텔레메트리 | TLM2/SERIAL2에서 `SERIAL2_PROTOCOL=10`(FrSky S.Port Passthrough)으로 Yaapu 데이터 수신 |

SBUS는 조종 입력이며, Yaapu 텔레메트리는 별도 연결로 시험했습니다. 제공된 부팅 메시지와 Mission Planner 화면에서 펌웨어 버전을 확인했습니다. 기압값 변화는 센서 반응 확인이며 정밀도 보정 결과는 아닙니다.

시험 설정에서는 `Frame: UNSUPPORTED`와 `PreArm: Motors: Check frame class and type`이 표시됐습니다. 이번 시험을 시동 허용·모터 구동·DShot 동작·비행 성능 검증으로 기록하지 않습니다.

제공한 APJ의 SHA-256은 `e58d15af3e1c11e48f405fc94d2a30237daf9835b1320957b182759ce1fbaf9a`입니다. 파일의 해시이며 장치 플래시를 별도로 읽어 계산한 값은 아닙니다.

검토 소스 `c8a883f406c4c6810935e80e6424161a31b301a1`은 시험 버전에서 README의 IO 양방향 DShot 지원 범위를 I1–I4로 정정한 것만 다릅니다. 런타임 코드·hwdef·커밋된 부트로더 바이트는 같습니다.

## 이전 개발 시험

아래는 이전 DCD-FC ArduCopter 4.8.0-dev 개발 펌웨어의 결과이며 최신 이미지에서 반복한 시험은 아닙니다.

| 항목 | 확인 자료 |
| --- | --- |
| USB/MAVLink·파라미터 | 연결과 파라미터 조회 기록 |
| GPS·나침반·IMU | 센서 데이터 및 IMU별 Roll/Pitch 반응 기록 |
| RC·시동 허용 | SBUS 입력 변화 및 강제 명령 없는 시동 허용 기록 |
| 출력·UART | 16개 출력, 외부 UART, CTS/RTS, 해당되는 비 IOMCU ESC 패스스루 동작을 사용자 확인 |
| FC 전류 | 단일 지점 비교: 기준 전원장치 0.322 A, FC 표시 0.32 A |

후속 출력·UART 시험 이미지의 정확한 해시는 장치에서 별도로 읽지 않았습니다. 별도 고전압 사진에는 Rover가 표시되어 Copter 시험으로 합산하지 않습니다. 내장 전류 센서는 ESC 구동 전류를 제외하며, 단일 지점 비교로 전체 범위의 정확도를 주장하지 않습니다.

## 소프트웨어 검사

- `f34c44fc2d415ec5563d88bae02d7d4c7a2ada4c`에서 Tracker·Blimp·Copter·Heli·Plane·Rover·Sub 및 FMU 부트로더 신규 보드 빌드 시험 통과
- `d2416fe2`에서 Copter·FMU 부트로더를 `--Werror`로 재빌드
- 구성·DMA 배치·APJ 식별 정보·이미지 배치·순정 IO 페이로드 검사 통과
- 문서 정정 후 브랜치 규칙 및 README Markdown 검사 통과

[리뷰 요약과 빌드 상세](https://github.com/ArduPilot/ardupilot/pull/34796) · [전달한 실물 시험 결과](https://github.com/ArduPilot/ardupilot/pull/34796#issuecomment-6295209974)

비행 시험 완료를 주장하지 않습니다. 장치 식별 번호, 위치가 포함된 화면과 전체 텔레메트리 로그는 공개 자료에 포함하지 않았습니다.
