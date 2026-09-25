# UNIT PULSAR ESP32-C5 Hardware

## Hardware Scope

This hardware reference covers schematic revision V1.2.3 (SKU `UE0134`) and
its bill of materials. It separates design-defined connections from values not
specified by the available board-level documentation. The board uses an
ESP32-C5HR8 controller; the schematic title block says `PULSAR ESP32 C5`, with
`REV: 1.2.0` on sheet 1 and `REV: 1.2.3` on sheet 2.

## Power Tree

A power-tree drawing for the ESP32-C5 board is not yet available. The
functional path defined by the schematic is:

```text
USB-C VBUS --D1--+
                 +--> +5V --> BQ24074 IN
VIN -------D4----+              |
                                +--> BMS_OUT --> SGM6029 buck --> 3.3 V
VBAT (JP1 / J3) <--> BQ24074 BAT
```

## Block Diagram

A block-diagram drawing for the ESP32-C5 board is not yet available. See
[Topology](#topology) for the functional relationships.

## Naming Rules

- Product name: **UNIT PULSAR ESP32-C5 Multi-Interface Development Board**.
- Product family: **UNIT DevLab ecosystem**; `DevLab` is not part of the
  product name.
- Source assets use lowercase, descriptive names and explicit
  revisions, for example `unit_sch_v_1_2_3_ue0134_pulsar_esp32_c5.pdf`.
- Generated Product Reference files use
  `unit_product_reference_v_0_1_0_pulsar_esp32_c5.*`.

## Hardware

| RefDes | Component | Confirmed role |
|---|---|---|
| IC1 | ESP32-C5HR8 | Main SoC; 2.4/5 GHz Wi-Fi 6, Bluetooth LE 5, IEEE 802.15.4; 8 MB in-package PSRAM |
| IC3 | BY25Q64ESHIG | 64 Mbit (8 MiB) quad SPI NOR flash |
| IC2 | BQ24074RGTR | Single-cell Li-Ion charger with power-path management |
| U2 | SGM6029CYG | 1 A synchronous buck regulator for the 3.3 V rail |
| IC4 | MAX17048G+T10 | Battery fuel gauge on the sensor I2C bus |
| U4 | MMC5603NJ | Three-axis magnetometer on the sensor I2C bus |
| U$36 | FHT40 (BOM) / SHT40 (schematic) | Humidity and temperature sensor on the sensor I2C bus |
| A2 | LPS22HBTR (BOM) / LPS22DFTR (schematic) | Barometric pressure sensor on the sensor I2C bus |
| U3 | VCNL4040M3OE | Proximity and ambient-light sensor on the sensor I2C bus |
| U1 | LFD182G45DCHD277 | 2.4 GHz / 5 GHz RF diplexer |
| J2 | RF1-2626-125CE-CT | RF coaxial antenna connector |
| XTAL1 | SX0B48.000F0810F30 | 48 MHz main crystal |
| MICRO_SD-HOLDER | 47309-2651 | microSD socket on SPI signals |
| LED1 | WS2812 1010 | Addressable RGB LED |
| J1 | HCZZ0032-4 | Four-position, 1 mm QWIIC-style connector |
| J20 | SM06B-SRSS-TB | Six-position, 1 mm SPI expansion connector |
| JP1 | PH2.0 2P | Two-position battery connection, 2.0 mm |
| J3 | Würth 1.25 mm 2P | Two-position battery connection, 1.25 mm (parallel to JP1) |
| S1, S2 | 1TS026A-1600-0553-CT | BOOT and RESET push buttons |

Individual component ratings are not module ratings. In particular, the allowed
`VIN`, `VBAT`, and 3.3 V rail loads are not specified at module level.

Board photographs for the ESP32-C5 revision are not yet available.

## Pinout

A pinout drawing for the ESP32-C5 board is not yet available. The following
mapping is transcribed from the V1.2.3 schematic (`ARDUINO_NANO_TEMPLATE`
header, positions 1–30). GPIO numbers follow the ESP32-C5 datasheet pin table.

| Pos. | Board label | ESP32-C5HR8 connection | Function / status |
|---:|---|---|---|
| 1 | `D13` / `SCK` / LED | GPIO27 | SPI clock; microSD CLK; J20; `BUILTIN2` LED; strapping pin |
| 2 | `3V3` | 3.3 V rail | Regulated rail; available current not specified |
| 3 | `EN` | SGM6029 enable | Through solder jumper |
| 4 | `VBAT` | Battery rail | Through solder jumper |
| 5 | `A1` / `D15` | GPIO1 / ADC1_CH0 | QWIIC SDA |
| 6 | `A2` / `D16` | GPIO4 / ADC1_CH3 | Analog-capable digital I/O (MTCK) |
| 7 | `A3` / `D17` | GPIO5 / ADC1_CH4 | Analog-capable digital I/O (MTDO) |
| 8 | `SDA` / `D18` | GPIO2 / ADC1_CH1 | I2C data; sensor bus; strapping pin (MTMS) |
| 9 | `SCL` / `D19` | GPIO3 / ADC1_CH2 | I2C clock; sensor bus; strapping pin (MTDI) |
| 10 | `A6` / `D21` | GPIO6 / ADC1_CH5 | QWIIC SCL; optional VCNL4040 `INT` |
| 11 | `NEOPIXEL DOUT` | LED1 `DO` | Through solder jumper |
| 12 | `VBUS` | USB VBUS | USB-C supply rail |
| 13 | `GND` | Ground | Through solder jumper (Nano `RESET` position) |
| 14 | `GND` | Ground | Common return |
| 15 | `VIN` | System supply input | Joins `+5V` through Schottky diode D4 |
| 16 | `TX0` / `D1` | GPIO11 / U0TXD | UART TX through 499 Ω R3 |
| 17 | `RX0` / `D0` | GPIO12 / U0RXD | UART RX |
| 18 | `RST` | `CHIP_PU` | Reset; 10 kΩ pull-up and 100 nF |
| 19 | `GND` | Ground | Common return |
| 20 | `D2` / `RGB` | GPIO0 | WS2812 data (`NEOP_DIN`) |
| 21 | `D3` / `BOOT` | GPIO28 | BOOT button; strapping pin |
| 22 | `D4` | GPIO9 | Digital I/O |
| 23 | `D5` | GPIO23 | microSD chip select; optional MAX17048 `ALERT` |
| 24 | `D6` | GPIO24 | Digital I/O; optional MAX17048 `QSTRT` |
| 25 | `D7` | GPIO25 | Digital I/O; optional BQ24074 `PGOOD`; strapping pin |
| 26 | `D8` | USB D- | Same net as USB-C D- (GPIO13 through 22 Ω R9) |
| 27 | `D9` | USB D+ | Same net as USB-C D+ (GPIO14 through 22 Ω R10) |
| 28 | `D10` / `SS` | GPIO10 | SPI chip select; J20 |
| 29 | `D11` / `MOSI` | GPIO7 | SPI MOSI; microSD CMD; J20; strapping pin |
| 30 | `D12` / `MISO` | GPIO26 | SPI MISO; microSD DAT0; J20; strapping pin |

Two schematic net labels differ from the connections drawn at the header:

- **`D2`:** IC1 GPIO8 carries the net label `D2`, but header position 20
  (`D2`) is wired to `NEOP_DIN` / GPIO0. No other connection is shown for the
  GPIO8 net; treat GPIO8 as unassigned until the PCB is verified.
- **`A6`:** the net is named `A6/GPIO4/TOUCH4`, but it connects to IC1 pin 15,
  which is GPIO6. GPIO4 is `A2` / `D16` (pin 13). The ESP32-C5 has no touch
  sensor, so `TOUCH4` does not apply.

### Interfaces

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

### Analog Inputs

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

### Board Resources

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

## Dimensions

A controlled dimension drawing and mounting-hole coordinates are not present
in the available V1.2.3 package. Do not derive dimensions from rendered board
images.

## Topology

```text
USB-C / VIN / battery
          |
   BQ24074 power path
          |
   SGM6029 buck -> 3.3 V
          |
     ESP32-C5HR8 (8 MB PSRAM in package)
   +------+--------+--------+-------+---------+
   |      |        |        |       |         |
 QSPI    RF      I2C       SPI    WS2812    GPIO
 flash  diplexer  sensors  microSD  RGB     + QWIIC
         + J2     + fuel    + J20
                  gauge
```

The diagram shows functional relationships only; it is not an electrical
power-path specification.

## Pin & Connector Layout

- **USB-C:** native USB data (GPIO13/GPIO14) and nominal USB VBUS entry
  through Schottky diode D1.
- **J1 QWIIC:** four positions carrying GND, 3.3 V, GPIO1/SDA, and
  GPIO6/SCL. Cable orientation is not specified by a controlled drawing.
- **J20 SPI:** six-position, 1 mm SH connector carrying SCK (GPIO27), MOSI
  (GPIO7), MISO (GPIO26), SS (GPIO10), 3.3 V, and GND.
- **J2 RF:** coaxial antenna connector shared by the 2.4 GHz and 5 GHz paths
  through the U1 diplexer. No onboard antenna is listed in the BOM.
- **microSD:** SPI connection (CLK, CMD, DAT0, DAT3/CS); DAT1 and DAT2 are not
  connected.
- **JP1 / J3 battery:** PH 2.0 mm and 1.25 mm two-position connectors wired in
  parallel to `VBAT`. Compatible batteries and cables are not specified.

## Functional Description

The ESP32-C5HR8 boots from the external BY25Q64 64 Mbit flash connected to the
dedicated SPI0/1 flash pins, and provides 8 MB of in-package PSRAM. A 48 MHz
crystal (XTAL1) is the main clock source. The 2.4 GHz and 5 GHz RF ports are
combined by the U1 diplexer and routed to the J2 coaxial connector; 0 Ω links
and unpopulated positions form the matching networks.

The MAX17048, MMC5603NJ, humidity/temperature sensor, pressure sensor, and
VCNL4040 share the `SDA_SENSOR` / `SCL_SENSOR` bus, which connects to GPIO2 and
GPIO3 through solder jumpers. The QWIIC connector uses a separate GPIO1/GPIO6
bus. The microSD socket uses SPI signals shared with the J20 connector and the
`D11`–`D13` header positions. One WS2812 LED is driven from GPIO0.

USB VBUS and `VIN` are combined through Schottky diodes into the `+5V` input of
the BQ24074, whose power-path output (`BMS_OUT`) feeds the SGM6029 buck
regulator. The schematic annotates a 250 mA charge current (`ICHRG=250mA`).
Module-level input ranges, rail current, and source-selection behavior are not
specified at module level.

## Applications

- Dual-band Wi-Fi 6, Bluetooth LE, Zigbee, and Thread prototyping
- Environmental monitoring (pressure, humidity, temperature, light)
- Battery-powered IoT nodes with fuel gauging
- microSD data logging
- Magnetometer and proximity-based user interfaces
- I2C sensor integration through QWIIC

## References

- [UNIT PULSAR ESP32-C5 repository](https://github.com/UNIT-Electronics-MX/unit_pulsar_esp32_c5)
- [Technical wiki](https://github.com/UNIT-Electronics-MX/unit_pulsar_esp32_c5/wiki)
- [Official ESP32-C5 datasheet](https://documentation.espressif.com/esp32-c5_datasheet_en.pdf)
- [Product Reference](https://unit-electronics-mx.github.io/unit_pulsar_esp32_c5/hardware/unit_product_reference_v_0_1_0_pulsar_esp32_c5.pdf)
- [V1.2.3 schematic](https://github.com/UNIT-Electronics-MX/unit_pulsar_esp32_c5/blob/main/hardware/unit_sch_v_1_2_3_ue0134_pulsar_esp32_c5.pdf)
