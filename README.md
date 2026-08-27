# RT-Spark Development Board — Arduino LED Testing

Short description
A simple Arduino example project that demonstrates LED testing on the RT‑Spark development board. It showcases basic GPIO usage (LED blink) with Arduino-style setup and how to verify board wiring.

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
This repository contains a minimal LED test used to verify the RT‑Spark development board's GPIO functionality using the Arduino framework. The test toggles two LEDs on PF11 and PF12 with a configurable blink interval to demonstrate basic board functionality and GPIO control.

## Supported hardware
- Board: RT‑Spark Development Board
- MCU: STM32 (e.g. STM32F4 series)
- LEDs used in this example: PF11, PF12 (on-board LEDs)
- Framework: Arduino core for STM32

## Features
- Simple alternating blink between two LEDs (PF11/PF12)
- 500 ms default blink interval
- Arduino-compatible code structure (setup/loop)

## Prerequisites
- Arduino IDE or PlatformIO
- Arduino core for STM32 installed
- USB cable for programming and serial communication
- Serial terminal (optional): Arduino Serial Monitor, minicom, picocom, or PuTTY

## Wiring
- **Onboard LEDs**: No wiring required if using the board's on-board LEDs on PF11 and PF12.
- **External LED (if needed)**: LED anode → MCU pin (with current-limiting resistor ~220Ω), LED cathode → GND.

## Quick start (build & run)
1. Clone the repository:
   ```bash
   git clone https://github.com/Ni-ear/RT-Thread-RT-Spark-Development-Board---led-testing.git
   cd RT-Thread-RT-Spark-Development-Board---led-testing
   ```

2. Open the sketch in Arduino IDE or configure in PlatformIO:
   - **Arduino IDE**: File → Open, select the sketch file
   - **PlatformIO**: Open the project folder

3. Select board and port:
   - Board: RT-Spark (or appropriate STM32 board)
   - Port: Your USB programmer port

4. Upload the sketch:
   - Click Upload (or `platformio run --target upload`)

5. Open Serial Monitor (Arduino IDE) or serial console:
   - Baud rate: 115200, 8N1

## Example code
This example configures PF11 and PF12 as outputs and alternates them every 500 ms using Arduino APIs:

```cpp
#include <Arduino.h>

// Define LED pins
const int LED1 = PF11;
const int LED2 = PF12;

void setup() {
  // Initialize serial communication (optional)
  Serial.begin(115200);
  
  // Configure LED pins as outputs
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);
  
  Serial.println("LED Test Started");
}

void loop() {
  // Turn on LED1, turn off LED2
  digitalWrite(LED1, HIGH);
  digitalWrite(LED2, LOW);
  delay(500);

  // Turn off LED1, turn on LED2
  digitalWrite(LED1, LOW);
  digitalWrite(LED2, HIGH);
  delay(500);
}
```

**Alternative: Direct Register Access**
If you prefer direct hardware control:

```cpp
#include <Arduino.h>

void setup() {
  // Enable Clock for GPIO Port F (Bit 5 in AHB1ENR register)
  RCC->AHB1ENR |= RCC_AHB1ENR_GPIOFEN;

  // Configure Pin 11 & Pin 12 as Output Mode (01)
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

**Included Demo Image:**

![LED photo](https://github.com/user-attachments/assets/d2a107ed-31f9-47da-826b-f460961e693c)

## Expected output
- LEDs alternate blinking every 500 ms
- If serial logging is enabled, startup message appears: "LED Test Started"
- Both LEDs should never be on or off simultaneously (perfect alternation)

## Troubleshooting
- **LEDs do not blink**: 
  - Verify PF11/PF12 mapping for your board using the MCU datasheet
  - Check physical wiring and solder joints
  - Confirm the sketch uploaded successfully

- **Upload fails**: 
  - Verify the correct board and COM port are selected
  - Check USB cable and driver installation
  - Ensure Arduino core for STM32 is installed and up-to-date

- **No serial output**: 
  - Verify baud rate is set to 115200
  - Check USB-UART drivers are installed
  - Confirm the correct COM port is selected

## Contributing
Feel free to open issues or submit PRs. Please include:
- MCU model and board variant
- Arduino IDE or PlatformIO version
- Steps to reproduce any issues

## License
This project is licensed under the MIT License. See LICENSE file for details.
