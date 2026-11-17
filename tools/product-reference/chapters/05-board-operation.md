## **5 Board Operation**

The procedures below define the board workflow for the Arduino IDE and the
Espressif Arduino core.

### **5.1 Getting Started with Arduino IDE** {.section-page}

1. Install Arduino IDE 2.x.
2. Add the Espressif package index to **Additional Boards Manager URLs**:
   `https://espressif.github.io/arduino-esp32/package_esp32_index.json`
3. Install the **esp32 by Espressif Systems** package, version 3.3 or later
   (ESP32-C5 support).
4. Select **ESP32C5 Dev Module** or an installed PULSAR-specific board
   definition.
5. Set **Flash Size** to 8 MB, enable **PSRAM**, and enable **USB CDC On
   Boot** to use the USB-C serial port.
6. Connect an antenna to J2 if the sketch uses the radio.
7. Connect a USB-C data cable and select the enumerated serial port.
8. Open a basic Blink sketch and upload it.

Compile success confirms API and library compatibility; electrical operation
also depends on the connected hardware and selected configuration.

### **5.2 First Power-up and Inspection**

Use USB-C for initial power. Inspect component orientation, connector seating,
and solder bridges before energizing the board. If available, use a
current-limited source and monitor the 3.3 V rail.

Do not attach a battery, external `VIN`, microSD card, QWIIC peripheral, or
J20 device for the first power check. Confirm the power indicator and absence
of unexpected heating, then establish USB enumeration and upload behavior.

### **5.3 BOOT and Reset Workflow**

For a normal update, the Arduino core resets the board into download mode
automatically through USB Serial/JTAG. If automatic upload is unavailable:

1. Disconnect external driven signals.
2. Hold BOOT.
3. Press and release RESET, or connect USB while BOOT is held.
4. Release BOOT after the download port appears.
5. Upload the firmware using the toolchain.
6. Press RESET to run the application from external flash.

### **5.4 Recommended Bring-up Sequence** {.section-page}

| Order | Subsystem | What the step establishes |
|---:|---|---|
| 1 | User LED | Core execution, GPIO27, and upload path |
| 2 | USB serial | Host communication and diagnostic output |
| 3 | ADC | Basic analog input path |
| 4 | RGB LED | GPIO0 timing |
| 5 | Sensor I2C | GPIO2/GPIO3, solder jumpers, and sensor addresses |
| 6 | Fuel gauge | MAX17048 voltage and state of charge |
| 7 | QWIIC I2C | GPIO1/GPIO6 and external expansion |
| 8 | PSRAM | Capacity detection and allocation |
| 9 | microSD | SPI bus, GPIO23 chip select, and filesystem |
| 10 | Wi-Fi / BLE / 802.15.4 | Antenna, RF path, and network connection |
| 11 | Combined application | Memory ownership and subsystem coexistence |

Testing one subsystem at a time makes pin conflicts and library configuration
errors easier to isolate.

### **5.5 C++ Example Collection** {.section-page}

The `software/cpp_examples/` directory contains 10 Arduino sketches that follow
the bring-up sequence in Section 5.4:

| Directory | Sketch | Hardware | Coverage |
|---|---|---|---|
| `01_basics/` | `chip_info_rgb` | Onboard | Chip model, flash, and PSRAM size; WS2812 color wheel |
| `01_basics/` | `blink_boot_button` | Onboard | User LED on GPIO27; BOOT button on GPIO28 |
| `02_adc/` | `adc_read` | Potentiometer on `A2` | Raw counts and calibrated millivolts on GPIO4 |
| `03_ws2812/` | `rgb_mixer` | Onboard | WS2812 color and brightness from the serial monitor |
| `04_i2c/` | `sensor_bus_scan` | Onboard | Sensor-bus scan on GPIO2/GPIO3 with device names |
| `04_i2c/` | `qwiic_bmi270` | BMI270 on QWIIC | QWIIC scan on GPIO1/GPIO6 and BMI270 chip ID |
| `05_microsd/` | `read_write` | microSD card | Mount, append, and read back a file |
| `06_uart/` | `uart_loopback` | Jumper `TX0`–`RX0` | UART0 loopback on GPIO11/GPIO12 |
| `07_wireless/` | `wifi_http` | DHT11, Wi-Fi network | 2.4/5 GHz Wi-Fi station and HTTP upload |
| `07_wireless/` | `ble_touch` | TTP223B, phone | BLE GATT server with notifications |

Compile the examples with **ESP32C5 Dev Module**, **USB CDC On Boot**
enabled, 8 MB flash, and PSRAM enabled:

```sh
FQBN=esp32:esp32:esp32c5:CDCOnBoot=cdc,PSRAM=enabled,FlashSize=8M,PartitionScheme=default_8MB
arduino-cli compile -u -p <port> --fqbn "$FQBN" software/cpp_examples/01_basics/blink_boot_button
arduino-cli monitor -p <port> -c baudrate=115200
```

#### **Hello World: Blink and BOOT Button**

The following sketch blinks the pink user LED (`BUILTIN2`, GPIO27) and
increases the blink rate while the BOOT button (GPIO28) is held:

```cpp
#include <Arduino.h>

constexpr uint8_t LED_PIN = 27;
constexpr uint8_t BOOT_BUTTON = 28;

uint32_t interval = 500;

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);
  pinMode(BOOT_BUTTON, INPUT_PULLUP);
  Serial.println("P1 ready: hold BOOT to blink faster");
}

void loop() {
  const bool pressed = digitalRead(BOOT_BUTTON) == LOW;
  const uint32_t wanted = pressed ? 100 : 500;
  if (wanted != interval) {
    interval = wanted;
    Serial.printf("button %s, interval %lu ms\n",
                  pressed ? "pressed" : "released", (unsigned long)interval);
  }
  digitalWrite(LED_PIN, !digitalRead(LED_PIN));
  delay(interval);
}
```

#### **Sensor Bus Scan**

`sensor_bus_scan` lists the onboard I2C devices. With JP2 and JP3 closed, the
expected serial output is:

```text
Scanning 0x08–0x77…
  0x30  MMC5603 magnetometer
  0x36  MAX17048 fuel gauge
  0x44  FHT40 temperature & humidity
  0x5C  LPS22 pressure
  0x60  VCNL4040 proximity & light
5 device(s)
```

### **5.6 Memory Planning** {.section-page}

Keep stacks, interrupt state, frequently accessed variables, and DMA
descriptors in internal SRAM. Place large network buffers, logging queues, and
historical data in PSRAM. Check every allocation and track free internal and
PSRAM heap separately.

Wi-Fi, Bluetooth LE, and 802.15.4 stacks use internal memory; avoid allocating
every maximum-size buffer at startup without a documented memory budget.

### **5.7 Storage Operation** {.section-page}

Configure SPI on GPIO27 (SCK), GPIO7 (MOSI), and GPIO26 (MISO) with GPIO23 as
the microSD chip select. Initialize the card, verify filesystem information,
open only the files needed, call `flush()` after important writes, and close
handles before removal or reset.

For data logging, buffer short records in RAM and write them in bounded batches
rather than performing filesystem operations from an interrupt handler.

### **5.8 Radio, Sensor, and Storage Coexistence** {.section-page}

The sensor bus (GPIO2/GPIO3), QWIIC bus (GPIO1/GPIO6), and SPI bus
(GPIO7/GPIO26/GPIO27) do not overlap. The microSD card and J20 devices share
the SPI bus and must use separate chip selects. All subsystems compete for
processor time, memory, and power; radio transmission adds current peaks to
the 3.3 V rail.

Use bounded update rates, buffered logging, and nonblocking network tasks. A
combined application should expose health information over USB serial or RGB
status rather than failing silently.

### **5.9 Troubleshooting Guide** {.section-page}

| Symptom | Checks |
|---|---|
| No USB device | Data-capable cable, BOOT/RESET sequence, host port, 3.3 V rail |
| Board does not boot | Strapping pins held low by external circuits, BOOT button |
| Blink does not run | GPIO27 mapping, selected board, successful upload |
| PSRAM unavailable | PSRAM option enabled, allocation result |
| Sensors not found | Sensor-bus solder jumpers, GPIO2/GPIO3, addresses |
| microSD fails | FAT32, GPIO23 chip select, SPI pins, card seating |
| Weak or no wireless link | Antenna on J2, band selection, matching network |
| Unstable combined app | Internal heap, PSRAM use, blocking loops, pin conflicts |

Record the exact board revision, core version, library versions, compiler
output, power source, and reproduction steps for every bring-up issue.

### **5.10 Example System: Sensor Logger with Wireless Upload** {.section-page}

This example combines board resources that are electrically separate but
compete for memory, processor time, and supply current. It describes an
integration order rather than promising a fixed sampling or upload rate.

| Stage | PULSAR resource | Observable result | Failure response |
|---|---|---|---|
| Identify | USB Serial/JTAG, flash, PSRAM | Log chip identity, firmware revision, flash size, and available memory | Stop if the selected image does not match the intended memory configuration |
| Acquire | Sensor I2C on GPIO2/GPIO3 | Read each enabled device and timestamp the sample | Mark a missing or invalid reading; retry without blocking the whole application |
| Buffer | Internal RAM or PSRAM | Queue a bounded record with sequence number | Reject or summarize old records when the queue reaches its documented limit |
| Persist | microSD on GPIO27/7/26, CS GPIO23 | Append a complete record and close or flush at a defined interval | Keep an error counter and preserve unsent records if storage is unavailable |
| Transmit | Wi-Fi through J2 antenna | Publish a record and record acknowledgement or timeout | Retry with a bounded delay; do not block sensing indefinitely |
| Report | USB serial and RGB LED | Expose storage, network, and sensor status | Distinguish a bad card from a network outage in the diagnostic output |

First validate the sensor bus with `sensor_bus_scan`, then the card with
`read_write`, then the radio with `wifi_http`. Only after each path works alone
should they run together. With microSD and J20 on the same SPI bus, hold each
device's chip select inactive while accessing the other. Keep `ALERT` jumper
EN_PAD2 open when GPIO23 is the microSD chip select. GPIO27 also drives the
pink user LED, so SPI clock activity can be visible on that indicator.

A record format should include at least a sequence number, timestamp or uptime,
sensor identity, measurement units, and an error/status field. The application
should define how long it may buffer data without a card or network, when to
flush storage, and what happens after an interrupted write. Those decisions
depend on the card and workload and are not fixed board characteristics.

### **5.11 Example System: Battery-Powered Sensor Node** {.section-page}

For a battery application, begin with one correctly polarized single-cell
Li-ion/LiPo battery on either JP1 or J3. The MAX17048 on the sensor I2C bus
reports cell voltage and state of charge, but a fuel-gauge reading is not a
substitute for a tested undervoltage policy in the application. The charger,
regulator, sensors, indicators, and attached modules remain part of the board
power budget even while the ESP32-C5 sleeps.

An application cycle can be organized as follows:

1. Wake and identify the reset reason; report it during bring-up over USB.
2. Read the fuel gauge and required sensors. Mark any failed reading explicitly.
3. Store or transmit the result, then verify that file handles and network
   transactions have completed or timed out.
4. Turn off application-controlled outputs and put external modules into their
   appropriate low-power state where supported.
5. Enter the selected SoC sleep mode and measure supply current from the
   battery with the final jumper settings, antenna, and attached modules.
6. Repeat across the intended battery-voltage range and after a USB-to-battery
   transition.

The ESP32-C5 datasheet's deep-sleep current is for the SoC under defined test
conditions. Section 2.7 explains why it cannot be used as the current of this
assembled board. Low-battery regulation, charger behavior under simultaneous
load, and battery run time require measurements on the target assembly.

### **5.12 Repeatable Board Validation Matrix** {.section-page}

Use one board revision, a recorded firmware build, and a recorded power source
for each test run. A successful compile is not evidence that a peripheral is
electrically functional. The acceptance result should include the observation
or measurement named below, not only a pass/fail label.

| Area | Test condition | Evidence to record |
|---|---|---|
| Power | USB-C only, VIN only, and battery only where applicable | Source voltage, 3.3 V rail, input current, unexpected heating |
| Transfer | Normal USB upload and BOOT/RESET recovery | Enumerated port, tool output, and firmware start message |
| Memory | Selected flash and PSRAM options | Detected capacities and a successful bounded PSRAM allocation |
| GPIO | User LED, BOOT button, and RGB LED | Expected state changes without changing boot mode |
| Sensor bus | JP2/JP3 in default state | Detected addresses and one plausible measurement per fitted device |
| QWIIC | Known 3.3 V I2C module with pull-ups | Device address and one successful transaction on GPIO1/GPIO6 |
| ADC | Known DC input on a free analog pad | Raw value, calibrated value, input voltage, and attenuation setting |
| microSD | Known card with the chosen filesystem | Mount, write, close, reopen, and byte-for-byte readback |
| J20 SPI | One external device with its own chip select | Correct transaction while microSD CS remains inactive |
| Radio | Suitable antenna installed; selected Wi-Fi band or other protocol | Association or exchange result, band, configuration, and observed stability |
| Combined load | Sensors, storage, and radio active together | 3.3 V minimum observed voltage, peak input current, errors, and resets |
| Battery | Charge, discharge, and source transition | Cell voltage, fuel-gauge reading, charge indication, and continued operation |

The values recorded by this matrix characterize the tested assembly and
conditions. They do not become guaranteed limits for every manufactured
PULSAR board without a controlled qualification plan. When a test fails,
return to the corresponding single-subsystem example before changing several
hardware or firmware variables at once.
