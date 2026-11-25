# DCD FC 펌웨어 설치

[제품 소개](README_KO.md) · [English](INSTALL.md)

## USB 업데이트

DCD FC는 ArduPilot 호환 부트로더를 기록하여 출고합니다. 일반 업데이트에는 DCD-FC용 `.apj` 파일을 사용하며 DFU 진입이나 ST-Link는 필요하지 않습니다. FURE·IORE는 각각 FMU·IO 리셋 버튼입니다.

1. 기존 파라미터를 저장하고 프로펠러를 제거합니다.
2. [개발 펌웨어 APJ · f283ff97](firmware/DCD_FC_ArduCopter_f283ff97.apj)를 다운로드합니다.
3. USB를 연결하고 Mission Planner의 **Load custom firmware**에서 APJ를 선택합니다. 보드 ID 검사를 강제로 우회하지 마세요.
4. 재부팅한 뒤 통신·센서·수신기 입력·출력 설정을 확인합니다.

현재 다운로드는 ArduCopter 4.8.0-dev, 보드 ID 1413입니다. 보드 지원은 병합됐으며, 2026년 11월 25일 확인 시 공식 다운로드 빌드는 아직 게시되지 않았습니다. 이후 배포는 [공식 펌웨어 서버](https://firmware.ardupilot.org)에서 확인할 수 있습니다. 저장된 파라미터는 펌웨어 기본값보다 우선합니다.

호환되는 순정 IO 부트로더는 유지합니다. 순정 일반·DShot IO 응용 펌웨어는 FMU 펌웨어에 포함되어 있으며, `BRD_IO_ENABLE=1`로 재부팅하면 IO 펌웨어 확인·갱신을 허용합니다.

## DCD-FC 펌웨어 직접 빌드

공식 DCD-FC 다운로드가 게시되기 전에도 ArduCopter를 직접 빌드할 수 있습니다. 아래 절차는 공식 ArduPilot 저장소의 [DCD-FC 병합 커밋](https://github.com/ArduPilot/ardupilot/commit/68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff)을 사용합니다. 결과물은 공식 소스를 직접 빌드한 개발판이며 공식 정식판이 아닙니다. 기존 `f283ff97` 다운로드와도 소스 버전이 다릅니다.

### 1. Ubuntu 준비와 소스 다운로드

x86-64 PC의 Ubuntu 22.04 또는 24.04를 사용합니다. Windows에서는 먼저 [공식 Windows 환경 설정 안내](https://ardupilot.org/dev/docs/building-setup-windows11.html)에 따라 WSL2에 Ubuntu를 준비하세요. 아래 명령은 PowerShell이 아닌 **Ubuntu 터미널**에서 일반 사용자로 실행합니다. 소스는 Linux 홈 폴더에 받으세요. `dcd-fc-ardupilot`은 새 폴더로 사용하며 기존 작업 폴더를 덮어쓰지 마세요.

```bash
sudo apt-get update
sudo apt-get install -y git
cd ~
git clone https://github.com/ArduPilot/ardupilot.git dcd-fc-ardupilot
cd dcd-fc-ardupilot
git checkout --detach 68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff
git submodule update --init --recursive
```

지정한 커밋에는 `DCD-FC`가 포함되어 있으므로 보드 파일을 따로 복사하거나 보드 ID를 수정할 필요가 없습니다. 각 단계가 오류 없이 완료된 뒤 다음 단계로 진행하세요.

### 2. 빌드 도구 설치

`~/dcd-fc-ardupilot`에서 공식 설치 스크립트를 실행합니다. 필요한 패키지와 Arm 컴파일러를 설치하며 Ubuntu 비밀번호를 요청할 수 있습니다. 설치 스크립트와 `waf` 앞에 `sudo`를 붙이지 마세요.

```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
. ~/.profile
arm-none-eabi-gcc --version
```

이 소스 버전의 x86-64용 설치 스크립트는 GNU Arm Embedded 10.2.1 (10-2020-q4-major)을 설치합니다. 컴파일러를 찾지 못하면 Ubuntu 터미널을 다시 열고 `~/dcd-fc-ardupilot`으로 이동한 뒤 진행하세요. [공식 Ubuntu 환경 설정 안내](https://ardupilot.org/dev/docs/building-setup-linux.html).

### 3. ArduCopter 빌드

같은 저장소 폴더에서 실행합니다.

```bash
./waf configure --board DCD-FC
./waf copter
ls -lh build/DCD-FC/bin/arducopter.apj
sha256sum build/DCD-FC/bin/arducopter.apj
```

업로드할 파일은 **`build/DCD-FC/bin/arducopter.apj`**입니다. 나중에 빌드 파일을 식별하려면 출력된 SHA-256을 함께 기록하세요. 보드 설정에 순정 IO 응용 펌웨어가 포함되어 있으므로 이 절차에서는 IO를 별도로 빌드하거나 IO 부트로더를 교체할 필요가 없습니다.

### 4. 생성된 APJ 설치

Windows/WSL에서는 다음 명령으로 결과 폴더를 엽니다.

```bash
explorer.exe build/DCD-FC/bin
```

`arducopter.apj`를 Windows 폴더로 복사한 뒤 위 USB 업데이트 절차에 따라 Mission Planner의 **Load custom firmware**로 선택하세요. Linux에서는 호환 지상국에서 같은 APJ를 선택합니다. 설치 전 파라미터를 백업하고 프로펠러를 제거하며, 설치 후 지상에서 보드 동작을 확인하세요.

기존 `f283ff97` 다운로드와 같은 소스 버전을 다시 빌드하려면 [소스·빌드 안내](docs/SOURCE.md)의 해당 커밋과 컴파일러를 사용하세요. 일반 빌드 옵션은 공식 [BUILD.md](https://github.com/ArduPilot/ardupilot/blob/68b1a73ef2bd0356c1d3b399e5b2d2eafffe73ff/BUILD.md)를 참고하세요.

## 전압·전류 측정

전압만 측정하려면 `BATT_MONITOR=3`, `BATT_VOLT_PIN=8`, `BATT_VOLT_MULT=8.987575`로 설정하고 재부팅하세요. 병합된 보드 지원의 기본값이며, 보관된 이전 개발 펌웨어는 당시 기본값 `4`를 사용합니다. 저장된 파라미터는 펌웨어 기본값보다 우선합니다.

FC 전류도 확인하려는 사용자는 `BATT_MONITOR=4`, `BATT_CURR_PIN=4`, `BATT_AMP_PERVLT=0.25`, `BATT_AMP_OFFSET=0`으로 설정하고 재부팅한 뒤 계측기로 보정하세요. ESC 구동 전류는 포함되지 않습니다. 기체 전체 소비량·잔량이나 용량 기준 경고에는 사용하지 말고, 전체 전류 측정에는 별도 센서를 사용하세요.

보드 ID 1413은 ArduPilot PR #34787에서 예약됐습니다. 최신·이전 펌웨어의 시험 결과는 [시험 기록](docs/TESTING_KO.md)을 참고하세요.

## 최초 기록 및 복구

아래 절차는 [보관된 이전 개발 펌웨어](archive/firmware/)를 사용한 최초 기록·복구 안내입니다. 일반 USB 업데이트 절차와 구분하세요. 포트 핀 배열은 [REV8 매뉴얼](DCD_FC_Manual_KO_REV8.pdf)을 참고하세요.

1. 기존 파라미터를 저장하고 프로펠러를 제거합니다.
2. 부트로더 최초 기록·복구 시에는 FMU SWD 포트(FUUP)에 ST-Link를 연결합니다. STM32CubeProgrammer에서 `DCD_FC_ArduCopter_with_Bootloader.hex`를 기록하고 Verify로 확인합니다. HEX에 포함된 주소를 사용하며, FUUP 핀 배열은 매뉴얼을 따릅니다. 3.3 V REF는 디버거 전압 기준입니다.
3. 같은 보드 ID의 부트로더가 설치된 뒤에는 Mission Planner의 **Load custom firmware**에서 `DCD_FC_ArduCopter_4.8.0-dev.apj`를 선택합니다. 보드 ID 검사를 강제로 우회하지 마세요.
4. 일반·DShot IO 응용 펌웨어는 FMU 펌웨어에 포함되어 있습니다. `BRD_IO_ENABLE=1`로 재부팅하면 IO 펌웨어 확인·갱신을 허용합니다. 호환되는 기존 IO 부트로더는 유지합니다.
5. 기체 운용 전 지상에서 USB 통신·센서·수신기 입력·출력을 확인합니다.

통합 HEX와 FMU 부트로더의 대상은 **FUUP이며 IOUP가 아닙니다.** 원시 BIN을 기록할 때 FMU 부트로더 주소는 `0x08000000`, 응용 펌웨어 주소는 `0x08020000`입니다. HEX 파일에는 주소가 포함되어 있습니다.

IO 부트로더가 없는 보드는 ST-Link를 **IOUP**에 연결하고 `DCD_FC_IO_Bootloader.hex`를 기록한 뒤 Verify로 확인합니다. BIN 기록 주소는 `0x08000000`, IO 응용 펌웨어 시작 주소는 `0x08001000`입니다. 제공 파일은 기존 순정 바이너리와 완전히 동일한 무수정 PX4 IO 부트로더입니다. 정상 동작하는 IO 부트로더는 교체할 필요가 없습니다. [IO 파일과 대응 소스](firmware/DCD_FC_IO_Bootloader_Package.zip).
