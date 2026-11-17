# UNIT PULSAR ESP32-C5 SENSE C++ Examples

Arduino C++ sketches for the UNIT PULSAR ESP32-C5 SENSE (hardware V1.2.3). Pin
numbers follow the Product Reference.

Each sketch is stored in a directory with the same name as its `.ino` file so
it can be opened directly with Arduino IDE or compiled with Arduino CLI.

## Toolchain Setup

1. Install Arduino IDE 2.x or Arduino CLI.
2. Add the Espressif board-package index:
   `https://espressif.github.io/arduino-esp32/package_esp32_index.json`
3. Install **esp32 by Espressif Systems** version 3.3 or later.
4. Select **ESP32C5 Dev Module**.
5. Enable **USB CDC On Boot**, set **Flash Size** to 8 MB, and enable
   **PSRAM**.
6. Compile and upload one sketch at a time.

```sh
FQBN=esp32:esp32:esp32c5:CDCOnBoot=cdc,PSRAM=enabled,FlashSize=8M,PartitionScheme=default_8MB
arduino-cli compile -u -p <port> --fqbn "$FQBN" software/cpp_examples/01_basics/blink_boot_button
arduino-cli monitor -p <port> -c baudrate=115200
```

## Required Libraries

| Examples | Dependency |
|---|---|
| `07_wireless/wifi_http` | DHT sensor library (Adafruit) |

`Wire`, `SPI`, `SD`, `WiFi`, `HTTPClient`, BLE, and `rgbLedWrite()` are
supplied by the Espressif Arduino core.

## Example Index

| Directory | Sketch | Hardware | Purpose |
|---|---|---|---|
| `01_basics/` | `chip_info_rgb` | Onboard | Print chip model, flash, and PSRAM size; cycle the WS2812 |
| `01_basics/` | `blink_boot_button` | Onboard | Blink the user LED (GPIO27); BOOT (GPIO28) changes the rate |
| `02_adc/` | `adc_read` | 10 kΩ potentiometer on `A2` | Raw counts and calibrated millivolts on GPIO4 |
| `03_ws2812/` | `rgb_mixer` | Onboard | Set the WS2812 color and brightness from the serial monitor |
| `04_i2c/` | `sensor_bus_scan` | Onboard | Scan the sensor bus (GPIO2/GPIO3) and name each device |
| `04_i2c/` | `qwiic_bmi270` | BMI270 on QWIIC | Scan QWIIC (GPIO1/GPIO6) and read the BMI270 chip ID |
| `05_microsd/` | `read_write` | microSD card (FAT32) | Mount the card, append a line, read the file back |
| `06_uart/` | `uart_loopback` | Jumper `TX0`–`RX0` | UART0 loopback on GPIO11/GPIO12 |
| `07_wireless/` | `wifi_http` | DHT11 on `A2`, Wi-Fi network | Join 2.4/5 GHz Wi-Fi and post readings over HTTP |
| `07_wireless/` | `ble_touch` | TTP223B on `A3`, phone | BLE GATT server notifying touch state |

## Board Connections Used

| Subsystem | GPIO mapping |
|---|---|
| User LED (`BUILTIN2`) | GPIO27 (shared with SPI SCK) |
| BOOT button | GPIO28 |
| WS2812 RGB LED | GPIO0 |
| Sensor I2C | SDA=GPIO2, SCL=GPIO3 |
| QWIIC I2C | SDA=GPIO1, SCL=GPIO6 |
| microSD SPI | SCK=GPIO27, MOSI=GPIO7, MISO=GPIO26, CS=GPIO23 |
| UART0 | TX=GPIO11, RX=GPIO12 |
| ADC | `A2`=GPIO4, `A3`=GPIO5 |

Connect a dual-band antenna to J2 before running the wireless examples.

## Source

These sketches are taken from the
[Pulsar C5 Labs](https://github.com/UNIT-Electronics-MX/unit_fork_esp_c5_labs_web)
project (`sketches/pulsar-c5/`), MIT License, Copyright (c) 2026 Abderrehmen
Gharsallah.
