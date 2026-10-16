# Stock IO bootloader

Unmodified PX4 `px4io_bl` target for the STM32F103 IO processor.
BIN SHA-256: `e9ba66410e14dd486f8dc1aff66f059dc5590a21fb4e1608deee08382aacb342`. This build is byte-identical to the existing stock IO binary.

Program `DCD_FC_IO_Bootloader.hex` through IOUP (IO SWD), using the embedded addresses.
For BIN, use `0x08000000`. IO application starts at `0x08001000`.
Keep a working IO bootloader. FMU firmware contains the IO application and can update it with `BRD_IO_ENABLE=1`.
Do not program FMU images through IOUP.

Build and image checks passed; physical-board installation and operation have not been tested for this package.

## Source and rebuild

- PX4/PX4-Bootloader: 6b1f48b69743165929deacfe443acdb2383d0f5a
- PX4/libopencm3: a5b4001c8aee17e230ea077574b27158537d630d
- Compiler: GNU Arm Embedded 10.2.1 (10-2020-q4-major)
- Build tools: GNU Make, Python 3 available as `python` on PATH

The `source/` directory contains the tracked upstream files and the matching libopencm3 dependency. Other MCU-family submodules are not required for this target. No source changes are applied.

```sh
cd source
GIT_SUBMODULES_ARE_EVIL=1 make px4io_bl
```

Outputs: `build/px4io_bl/px4io_bl.bin` and `.hex`.
The environment variable skips Git submodule checks because this archive already contains the pinned dependency source and no Git history.
Flash reservation: 4 KiB; application capacity: 60 KiB; USART2 PA2/PA3; bootloader board type: 10.
The IO bootloader uses its upstream board type, independent of the FMU board ID.

## License

Retain all upstream notices. PX4 bootloader core files carry BSD-3-Clause notices;
STM32 USART support carries GPL-3.0-or-later notices; libopencm3 retains its own notices.
See `IO_BOOTLOADER_LICENSE.md`, `source/LICENSE.md`, and `source/libopencm3/COPYING*`.
