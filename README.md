# TinyUF2 Bootloader (Flexi-HAL Version)

[![Build Status](https://github.com/adafruit/tinyuf2/workflows/Build/badge.svg)](https://github.com/adafruit/tinyuf2/actions)[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](https://opensource.org/licenses/MIT)

This is a fork of the TinyUF2 repo developed specifically for the [Flexi-HAL](https://github.com/Expatria-Technologies/Flexi-HAL) project which uses the stm32f446re microcontroller. The TinyUF2 project is a cross-platform UF2 Bootloader project for MCUs based on [TinyUSB](https://github.com/hathach/tinyusb). 

```
.
├── apps              # Useful applications such as self-update, erase firmware
├── lib               # Sources from 3rd party such as tinyusb, mcu drivers ...
├── ports             # Port/family specific sources
│   ├── espressif
│   │   └── boards/   # Board specific sources
│   │   └── Makefile  # Makefile for this port
│   └── mimxrt10xx         
├── src               # Cross-platform bootloader sources files
```

## Features

TODO more docs later

- Support ESP32-S2, ESP32-S3, iMXRT10xx, LPC55xx, STM32F3, STM32F4
- Self update with update file in uf2 format
- Indicator: LED, RGB
- Debug log with uart/swd
- Double tap to enter DFU, reboot to DFU and quick reboot from application

## Build and Flash

Following is generic compiling information. Each port may require extra set-up and slight different process e.g esp32s2 require setup IDF.

### Compile

To build this for a specific board, we need to change the current directory to its port folder

```
$ cd ports/stm32f4
```

Then compile with `make BOARD=[board_name] all`, for example

```
make BOARD=flexi_hal_stm32f446re all
```

The required mcu driver submodules will be cloned automatically if needed.

### Flash

`flash` target will use the default on-board debugger (jlink/cmsisdap/stlink/dfu) to flash the binary, please install those support software in advance. Some board use bootloader/DFU via serial which is required to pass to make command

```
$ make BOARD=flexi_hal_stm32f446re flash
```

If you use an external debugger, there is `flash-jlink`, `flash-stlink`, `flash-pyocd` which are mostly like to work out of the box for most of the supported board.

### Debug

To compile for debugging add `DEBUG=1`, this will mostly change the compiler optimization

```
$ make BOARD=flexi_hal_stm32f446re DEBUG=1 all
```

#### Log

Should you have an issue running example and/or submitting an bug report. You could enable TinyUSB built-in debug logging with optional `LOG=`. 
- **LOG=1** will print message from bootloader and error if any from TinyUSB stack.
- **LOG=2** and **LOG=3** will print more information with TinyUSB stack events

```
$ make BOARD=flexi_hal_stm32f446re LOG=1 all
```

#### Logger

By default log message is printed via on-board UART which is slow and take lots of CPU time comparing to USB speed. If your board support on-board/external debugger, it would be more efficient to use it for logging. There are 2 protocols: 

- `LOGGER=rtt`: use [Segger RTT protocol](https://www.segger.com/products/debug-probes/j-link/technology/about-real-time-transfer/)
  - Cons: requires jlink as the debugger.
  - Pros: work with most if not all MCUs
  - Software viewer is JLink RTT Viewer/Client/Logger which is bundled with JLink driver package.
- `LOGGER=swo`: Use dedicated SWO pin of ARM Cortex SWD debug header.
  - Cons: only work with ARM Cortex MCUs minus M0
  - Pros: should be compatible with more debugger that support SWO.
  - Software viewer should be provided along with your debugger driver.

```
$ make BOARD=flexi_hal_stm32f446re LOG=2 LOGGER=rtt all
$ make BOARD=flexi_hal_stm32f446re LOG=2 LOGGER=swo all
```
