## **4 Connectors & Pinouts**

Pin labels describe hardware revision V1.2.3. GPIO numbers identify
ESP32-C5HR8 signals according to the Espressif datasheet pin table; framework
aliases depend on the selected board definition.

### **4.1 General Pinout** {.section-page}

| Pos. | Board label | ESP32-C5HR8 | Primary board role | Shared resource |
|---:|---|---:|---|---|
| 1 | `D13` / `SCK` / LED | GPIO27 | SPI clock | microSD CLK, J20, `BUILTIN2` LED; strapping pin |
| 2 | `3V3` | — | 3.3 V rail | SGM6029 output |
| 3 | `EN` | — | 3.3 V regulator enable | Solder jumper to SGM6029 `EN` |
| 4 | `VBAT` | — | Battery rail | Solder jumper to `VBAT` |
| 5 | `A1` / `D15` | GPIO1 / ADC1_CH0 | Analog-capable I/O | QWIIC SDA |
| 6 | `A2` / `D16` | GPIO4 / ADC1_CH3 | Analog-capable I/O | MTCK |
| 7 | `A3` / `D17` | GPIO5 / ADC1_CH4 | Analog-capable I/O | MTDO |
| 8 | `SDA` / `D18` | GPIO2 / ADC1_CH1 | I2C data | Sensor bus; MTMS strapping pin |
| 9 | `SCL` / `D19` | GPIO3 / ADC1_CH2 | I2C clock | Sensor bus; MTDI strapping pin |
| 10 | `A6` / `D21` | GPIO6 / ADC1_CH5 | Analog-capable I/O | QWIIC SCL; optional VCNL4040 `INT` |
| 11 | `NEOPIXEL DOUT` | — | RGB-chain extension output | Solder jumper to LED1 `DO` |
| 12 | `VBUS` | — | USB VBUS | USB-C supply |
| 13 | `GND` | — | Ground | Solder jumper (Nano `RESET` position) |
| 14 | `GND` | — | Ground | Common return |
| 15 | `VIN` | — | Supply input | To `+5V` through D4 |
| 16 | `TX0` / `D1` | GPIO11 | UART0 TX | 499 Ω series resistor R3 |
| 17 | `RX0` / `D0` | GPIO12 | UART0 RX | General-purpose |
| 18 | `RST` | `CHIP_PU` | Reset | RESET button, 10 kΩ pull-up |
| 19 | `GND` | — | Ground | Common return |
| 20 | `D2` / `RGB` | GPIO0 | NeoPixel data | LED1 `DI` |
| 21 | `D3` / `BOOT` | GPIO28 | BOOT input | BOOT button; strapping pin |
| 22 | `D4` | GPIO9 | Digital I/O | General-purpose |
| 23 | `D5` | GPIO23 | Digital I/O | microSD CS; optional MAX17048 `ALERT` |
| 24 | `D6` | GPIO24 | Digital I/O | Optional MAX17048 `QSTRT` |
| 25 | `D7` | GPIO25 | Digital I/O | Optional BQ24074 `PGOOD`; strapping pin |
| 26 | `D8` | USB D- | USB D- | Shared with USB-C; GPIO13 through 22 Ω R9 |
| 27 | `D9` | USB D+ | USB D+ | Shared with USB-C; GPIO14 through 22 Ω R10 |
| 28 | `D10` / `SS` | GPIO10 | SPI chip select | J20 `SS` |
| 29 | `D11` / `MOSI` | GPIO7 | SPI MOSI | microSD CMD, J20; strapping pin |
| 30 | `D12` / `MISO` | GPIO26 | SPI MISO | microSD DAT0, J20; strapping pin |

`EN`, `VBAT`, `NEOPIXEL DOUT`, and the ground at position 13 are connected to
their edge-pad positions through solder jumpers.

### **4.2 Interface Summary** {.section-page}

| Interface | Signal | Board label | GPIO | Notes |
|---|---|---|---:|---|
| UART0 | RX | `D0` | GPIO12 | |
| UART0 | TX | `D1` | GPIO11 | 499 Ω series resistor R3 |
| SPI | SS / CS | `D10` | GPIO10 | Also J20 pin 4 |
| SPI | MOSI | `D11` | GPIO7 | Shared with microSD and J20 |
| SPI | MISO | `D12` | GPIO26 | Shared with microSD and J20 |
| SPI | SCK | `D13` | GPIO27 | Shared with microSD, J20, and `BUILTIN2` LED |
| microSD | CS | `D5` | GPIO23 | Separate chip select from `D10` |
| Sensor I2C (`SDA_SENSOR`) | SDA | `SDA` / `D18` | GPIO2 | Onboard sensors through solder jumper; 4.7 kΩ pull-up |
| Sensor I2C (`SCL_SENSOR`) | SCL | `SCL` / `D19` | GPIO3 | Onboard sensors through solder jumper; 4.7 kΩ pull-up |
| QWIIC I2C | SDA | `A1` / `D15` | GPIO1 | J1 pin 3; no onboard pull-up |
| QWIIC I2C | SCL | `A6` / `D21` | GPIO6 | J1 pin 4; no onboard pull-up |
| USB | D- | `D8` | GPIO13 | Native USB; 22 Ω R9 |
| USB | D+ | `D9` | GPIO14 | Native USB; 22 Ω R10 |

The `SDA` / `SCL` edge pads are the sensor bus, not a separate external bus:
any device connected there shares the bus with the five onboard I2C devices.
QWIIC is an independent bus on GPIO1 and GPIO6.

Two schematic net labels differ from the connections drawn at the header:

- **`D2`:** IC1 GPIO8 carries the net label `D2`, but header position 20
  (`D2`) is wired to `NEOP_DIN` / GPIO0. No other connection is shown for the
  GPIO8 net; treat GPIO8 as unassigned until the PCB is verified.
- **`A6`:** the net is named `A6/GPIO4/TOUCH4`, but it connects to IC1 pin 15,
  which is GPIO6. GPIO4 is `A2` / `D16` (pin 13). The ESP32-C5 has no touch
  sensor, so `TOUCH4` does not apply.

### **4.3 Analog Inputs** {.section-page}

| Analog label | Digital label | GPIO | ADC channel | Shared use |
|---|---|---:|---|---|
| `A1` | `D15` | GPIO1 | ADC1_CH0 | QWIIC SDA |
| `A2` | `D16` | GPIO4 | ADC1_CH3 | — |
| `A3` | `D17` | GPIO5 | ADC1_CH4 | — |
| `A4` / `SDA` | `D18` | GPIO2 | ADC1_CH1 | Sensor I2C SDA |
| `A5` / `SCL` | `D19` | GPIO3 | ADC1_CH2 | Sensor I2C SCL |
| `A6` | `D21` | GPIO6 | ADC1_CH5 | QWIIC SCL; optional VCNL4040 `INT` |

The Nano `A0` and `A7` positions carry `VBAT` and `NEOPIXEL DOUT` and are not
analog inputs. `A4`/`A5` and `A1`/`A6` are usable as analog inputs only while
the corresponding I2C bus is not in use.

### **4.4 Board Resources and I2C Addresses** {.section-page}

| Resource | Signal / GPIO | Bus | I2C address | Notes |
|---|---|---|---|---|
| Native USB | GPIO13 / GPIO14 | USB Serial/JTAG | — | `D8` / `D9` |
| BOOT button | GPIO28 | — | — | `D3`; strapping pin |
| UART0 | GPIO11 / GPIO12 | UART | — | `D1` / `D0` |
| NeoPixel in | GPIO0 (`NEOP_DIN`) | — | — | LED1 WS2812 1010; `D2` |
| NeoPixel out | LED1 `DO` (`NEOP_DO`) | — | — | Header position 11 through solder jumper |
| Flash | GPIO15–GPIO18, GPIO20–GPIO22 | SPI0/1 | — | BY25Q64; not available as GPIO |
| PSRAM | In package | SPI0/1 | — | 8 MB, ESP32-C5HR8 |
| microSD | GPIO27, GPIO7, GPIO26, GPIO23 | SPI | — | CS on `D5` |
| QWIIC | GPIO1 / GPIO6 | QWIIC I2C | — | J1 |
| Battery monitor | GPIO2 / GPIO3 | Sensor I2C | `0x36` | MAX17048G+T10 |
| Light / proximity | GPIO2 / GPIO3 | Sensor I2C | `0x60` | VCNL4040M3OE |
| Temperature / RH | GPIO2 / GPIO3 | Sensor I2C | `0x44` | FHT40-DD-TR (SHT40 in schematic); verify address for FHT40 |
| Pressure | GPIO2 / GPIO3 | Sensor I2C | `0x5C` | LPS22HBTR (LPS22DFTR in schematic); `SA0` to GND |
| Magnetometer | GPIO2 / GPIO3 | Sensor I2C | `0x30` | MMC5603NJ |
| RF | `ANT_2G` (pin 42), `ANT_5G` (pin 48) | — | — | U1 diplexer to J2 |

### **4.5 Arduino Nano Pinout Compatibility** {.section-page}

The board uses two parallel 15-position edge rows based on the Arduino Nano
layout. This provides a familiar mechanical footprint and labeling pattern, but
does not imply full electrical or shield compatibility.

Important differences include:

- The ESP32-C5HR8 and board GPIO operate in the 3.3 V logic domain.
- Several conventional positions have PULSAR-specific functions: `EN` at
  `AREF`, `VBAT` at `A0`, `NEOPIXEL DOUT` at `A7`, `VBUS` at `5V`, and a
  jumper-selected ground at the second `RESET` position.
- `D2` carries the RGB LED data and `D3` carries the BOOT signal.
- `D8` and `D9` carry the ESP32-C5 native USB D-/D+ signals and should not be
  treated as conventional digital I/O.
- GPIO2, GPIO3, GPIO7, GPIO25, GPIO26, GPIO27, and GPIO28 are strapping pins.
  External circuits must not force a level that changes the boot mode during
  reset.
- Analog channel labels do not follow a sequential GPIO order.

Before installing the board into a Nano-style carrier, verify power, reset,
analog, digital, and mechanically shared positions.

### **4.6 QWIIC Connector** {.section-page}

J1 is a four-position, 1 mm-pitch QWIIC connector carrying:

| Pin | Signal | ESP32-C5HR8 connection | Function |
|---:|---|---:|---|
| 1 | GND | Ground | Common return |
| 2 | 3.3 V | Regulated rail | Peripheral supply |
| 3 | SDA | GPIO1 | External I2C data |
| 4 | SCL | GPIO6 | External I2C clock |

The QWIIC interface operates in the 3.3 V logic domain and is separate from
the GPIO2/GPIO3 sensor bus. The board does not fit pull-up resistors on
GPIO1/GPIO6; QWIIC devices normally provide them.

The QWIIC 3.3 V supply is provided by the SGM6029 regulator, which supports up
to 1 A total output current shared between the onboard circuitry and all
external 3.3 V loads.

### **4.7 J20 SPI Connector** {.section-page}

J20 is a six-position, 1 mm-pitch SH connector for external SPI devices.

| Pin | Signal | ESP32-C5HR8 connection |
|---:|---|---:|
| 1 | SCK | GPIO27 |
| 2 | MOSI | GPIO7 |
| 3 | MISO | GPIO26 |
| 4 | SS | GPIO10 |
| 5 | 3.3 V | Regulated rail |
| 6 | GND | Ground |

SCK, MOSI, and MISO are shared with the microSD socket. Keep the microSD chip
select (GPIO23) high while communicating with a J20 device.

### **4.8 MicroSD Connector** {.section-page}

| Socket signal | GPIO | Description |
|---|---:|---|
| `CLK` | GPIO27 | SPI SCK |
| `CMD` | GPIO7 | SPI MOSI |
| `DAT0` | GPIO26 | SPI MISO |
| `DAT3` | GPIO23 | SPI chip select |
| `DAT1`, `DAT2` | — | Not connected |
| VDD | 3.3 V | Card supply |
| GND / shield | Ground | Electrical return and connector shield |

Insert or remove the microSD card only when filesystem activity has stopped.
Software should flush and close open files before card extraction or power
removal.

### **4.9 Battery Connections** {.section-page}

JP1 (PH 2.0 mm) and J3 (1.25 mm) provide two battery connector options wired
in parallel to `VBAT`. Connect only one battery at a time.

The battery input is designed for a single-cell LiPo battery with a nominal
voltage of 3.7 V and a maximum fully charged voltage of 4.2 V. Charging is
managed by the onboard BQ24074RGTR with a nominal charge current of 250 mA,
and battery state is reported by the MAX17048 fuel gauge.

Always verify connector polarity before connecting a battery, as connector
compatibility alone does not guarantee correct polarity.

A solder jumper can connect the battery rail to edge-pad position 4 (`VBAT`).

Do not connect multi-cell battery packs or batteries exceeding 4.2 V.

### **4.10 RF Antenna Connector** {.section-page}

J2 is an RF1-2626-125CE-CT coaxial connector rated to 6 GHz. It carries the
combined 2.4 GHz and 5 GHz signal from the U1 diplexer. Use a dual-band antenna
with a mating plug and connect it before enabling radio transmission.

### **4.11 USB-C, BOOT, and Reset** {.section-page}

The USB-C connector provides power, programming, and USB data connectivity.
The USB D- and D+ signals connect to the ESP32-C5 USB Serial/JTAG controller
(GPIO13 and GPIO14) through 22 Ω series resistors and are also exposed at the
`D8` and `D9` positions. CC1 and CC2 have 5.1 kΩ pull-down resistors.

The BOOT button pulls GPIO28 low. Holding BOOT during reset selects the
ESP32-C5 download boot mode used for firmware programming and recovery. The
RESET button pulls `CHIP_PU` low and restarts the ESP32-C5 while board power
remains applied.

A typical firmware-recovery sequence is to hold BOOT, press and release RESET,
then release BOOT after the device enumerates in download mode.

### **4.12 Debug Interface** {.section-page}

The ESP32-C5 USB Serial/JTAG controller provides serial console, firmware
download, and JTAG debugging through the USB-C connector without an external
probe. The JTAG pins (MTMS, MTDI, MTCK, MTDO) are used as GPIO2–GPIO5 on this
board and are not routed to dedicated debug pads.
