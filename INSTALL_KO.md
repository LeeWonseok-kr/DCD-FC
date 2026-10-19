# DCD FC 펌웨어 설치

ArduCopter 4.8.0-dev · 예약된 보드 ID 1413.

1. 기존 파라미터를 저장하고 프로펠러를 제거합니다.
2. 최초 설치는 FMU SWD 포트(FUUP)에 ST-Link를 연결합니다. STM32CubeProgrammer에서 `DCD_FC_ArduCopter_with_Bootloader.hex`를 기록하고 Verify로 확인합니다. HEX에 포함된 주소를 사용하며, FUUP 핀 배열은 매뉴얼을 따릅니다. 3.3 V REF는 디버거 전압 기준입니다.
3. 같은 보드 ID의 부트로더가 설치된 뒤에는 Mission Planner의 **Load custom firmware**에서 `DCD_FC_ArduCopter_4.8.0-dev.apj`를 선택합니다. 보드 ID 검사를 강제로 우회하지 마세요.
4. 일반·DShot IO 응용 펌웨어는 FMU 펌웨어에 포함되어 있습니다. `BRD_IO_ENABLE=1`로 재부팅하면 IO 펌웨어 확인·갱신을 허용합니다. 호환되는 기존 IO 부트로더는 유지합니다.
5. 기체 운용 전 지상에서 USB 통신·센서·수신기 입력·출력을 확인합니다.

통합 HEX와 FMU 부트로더의 대상은 **FUUP이며 IOUP가 아닙니다.** 원시 BIN을 기록할 때 FMU 부트로더 주소는 `0x08000000`, 응용 펌웨어 주소는 `0x08020000`입니다. HEX 파일에는 주소가 포함되어 있습니다.

IO 부트로더가 없는 보드는 ST-Link를 **IOUP**에 연결하고 `DCD_FC_IO_Bootloader.hex`를 기록한 뒤 Verify로 확인합니다. BIN 기록 주소는 `0x08000000`, IO 응용 펌웨어 시작 주소는 `0x08001000`입니다. 제공 파일은 기존 순정 바이너리와 완전히 동일한 무수정 PX4 IO 부트로더입니다. 정상 동작하는 IO 부트로더는 교체할 필요가 없습니다. [IO 파일과 대응 소스](DCD_FC_IO_Bootloader_Package.zip).

전압만 측정하려면 `BATT_MONITOR=3`, `BATT_VOLT_PIN=8`, `BATT_VOLT_MULT=8.987575`로 설정하고 재부팅하세요. 수정된 보드 지원 PR의 기본값이며, 이곳의 기존 다운로드 파일은 당시 기본값 `4`를 사용합니다. 저장된 파라미터는 펌웨어 기본값보다 우선합니다.

FC 전류도 확인하려는 사용자는 `BATT_MONITOR=4`, `BATT_CURR_PIN=4`, `BATT_AMP_PERVLT=0.25`, `BATT_AMP_OFFSET=0`으로 설정하고 재부팅한 뒤 계측기로 보정하세요. ESC 구동 전류는 포함되지 않습니다. 기체 전체 소비량·잔량이나 용량 기준 경고에는 사용하지 말고, 전체 전류 측정에는 별도 센서를 사용하세요.

개발 이미지의 빌드·파일 검사는 통과했으며 전체 실물 시험은 완료되지 않았습니다. 보드 ID 1413은 ArduPilot PR #34787에서 예약됐습니다.
