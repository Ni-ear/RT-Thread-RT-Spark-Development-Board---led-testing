# RT-Thread-RT-Spark-Development-Board---led-testing

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

<img width="1920" height="1041" alt="image" src="https://github.com/user-attachments/assets/d2a107ed-31f9-47da-826b-f460961e693c" />
