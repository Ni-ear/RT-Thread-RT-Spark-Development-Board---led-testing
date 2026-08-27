# RT-Thread RT-Spark Development Board — LED Testing

Short description
A small example project that runs LED tests on the RT‑Spark development board. It demonstrates basic GPIO usage (LED blink), RT-Thread/Arduino-style setup, and how to verify board wiring.

Badges
- Build: ![build-status](https://img.shields.io/badge/build-none-lightgrey)
- License: ![license](https://img.shields.io/badge/license-MIT-blue)

## Table of contents
- [Overview](#overview)
- [Supported hardware](#supported-hardware)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Wiring](#wiring)
- [Quick start (build & run)](#quick-start-build--run)
- [Example code](#example-code)
- [Expected output](#expected-output)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Overview
This repository contains a minimal LED test used to verify the RT‑Spark development board's GPIO and RT‑Thread (or Arduino-compatible) runtime. The test toggles two LEDs on PF11 and PF12 and prints basic status to the serial console if available.

## Supported hardware
- Board: RT‑Spark Development Board
- MCU: (replace with your MCU, e.g. STM32F4 series)
- LEDs used in this example: PF11, PF12 (on many STM32-based Spark boards these are connected to on-board LEDs)
- Tested with: RT‑Thread X.Y or Arduino core for STM32 (replace with the version you used)

## Features
- Simple alternating blink between two LEDs (PF11/PF12)
- 500 ms default blink interval

## Prerequisites
- Toolchain: appropriate cross-compiler (e.g., arm-none-eabi-gcc) or RT‑Thread Studio
- Flashing tool: ST‑Link / J‑Link / vendor-specific tool
- Serial terminal (optional): minicom, picocom, PuTTY

## Wiring
- Onboard LEDs: no wiring required if using the board's on-board LEDs on PF11 and PF12.
- External LED (if needed): LED anode → MCU pin (with current-limiting resistor), LED cathode → GND.

## Quick start (build & run)
1. Clone repo:
   ```bash
   git clone https://github.com/Ni-ear/RT-Thread-RT-Spark-Development-Board---led-testing.git
   cd RT-Thread-RT-Spark-Development-Board---led-testing
   ```
2. Build using your preferred method (RT‑Thread Studio or command line). Example placeholders:
   ```bash
   # RT-Thread Studio: open the project and build
   # Command line (example):
   scons --target=release
   # or
   make all
   ```
3. Flash the firmware using your programmer:
   ```bash
   # Example using st-flash (adjust file path and address):
   st-flash write build/firmware.bin 0x08000000
   ```
4. Open serial console (if implemented): 115200 8N1

## Example code
The repository previously included the following example that configures PF11 and PF12 as outputs and alternates them every 500 ms. Keep or adapt this code depending on your build framework.

```cpp
#include <Arduino.h>

void setup() {
  // 1. Enable Clock for GPIO Port F (Bit 5 in AHB1ENR register)
  RCC->AHB1ENR |= RCC_AHB1ENR_GPIOFEN;

  // 2. Configure Pin 11 & Pin 12 as Output Mode (01)
  // Clear bits for PF11 and PF12
  GPIOF->MODER &= ~((3UL << (11 * 2)) | (3UL << (12 * 2)));
  // Set mode to General Purpose Output
  GPIOF->MODER |=  ((1UL << (11 * 2)) | (1UL << (12 * 2)));
}

void loop() {
  // Set PF11 HIGH, PF12 LOW
  GPIOF->BSRR = (1UL << 11) | (1UL << (12 + 16));
  delay(500);

  // Set PF11 LOW, PF12 HIGH
  GPIOF->BSRR = (1UL << (11 + 16)) | (1UL << 12);
  delay(500);
}
```

(If your project uses RT‑Thread native APIs instead of Arduino-style code, convert the GPIO init and toggling to the appropriate RT‑Thread HAL calls.)

Below is the image that was previously included in the README:

![LED photo](https://github.com/user-attachments/assets/d2a107ed-31f9-47da-826b-f460961e693c)

## Expected output
- LEDs alternate every 500 ms.
- If serial logging is enabled, you may see startup messages from RT‑Thread or the Arduino core.

## Troubleshooting
- LEDs do not blink: verify PF11/PF12 mapping for your board and the MCU's datasheet; check wiring and solder joints.
- Build or flash fails: verify toolchain version and correct flash address.
- No serial output: ensure the correct serial port and baud rate; check USB-UART drivers.

## Contributing
Feel free to open issues or submit PRs. Please include the MCU model, RT‑Thread or core version, and steps to reproduce.

## License
This project defaults to MIT. Add a LICENSE file to make it explicit.
