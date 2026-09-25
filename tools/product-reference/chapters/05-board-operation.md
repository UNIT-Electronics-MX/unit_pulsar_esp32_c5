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

C++ examples for the ESP32-C5 revision are planned under
`software/cpp_examples/`. The recommended bring-up sequence in Section 5.4
defines the intended example coverage.

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
