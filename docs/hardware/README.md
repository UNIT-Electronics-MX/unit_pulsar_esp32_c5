# UNIT PULSAR ESP32-C5 Hardware

## Hardware Scope

This page summarizes the UNIT PULSAR ESP32-C5 board from
schematic revision V1.2.3 and the existing Product Reference source. Top and
bottom board photographs and a controlled dimension drawing are not available
for that revision. The 2D pinout remains the available board preview.

| Field | Value |
|---|---|
| MPN | UE0134 |

## Power Tree

The schematic shows USB VBUS and `VIN` feeding the `+5V` node through separate
Schottky diodes. The BQ24074 charger and power path connects this input and a
single-cell battery to `BMS_OUT`; the SGM6029 buck regulator generates the
3.3 V logic rail. The MAX17048 monitors the battery.

```text
USB-C / VIN -> +5V -> BQ24074 power path -> BMS_OUT -> SGM6029 -> 3.3 V
                         ^                       |
                         |                       +-- ESP32-C5 and peripherals
                       battery
                         |
                      MAX17048
```

This diagram summarizes the documented functional connections. For operating
conditions and component-specific limits, see the
[Product Reference source](../tools/product-reference/chapters/02-ratings.md).

## Block Diagram

A standalone block-diagram image is not available. The Product Reference
source describes these functional relationships:

```text
ESP32-C5HR8 (in-package PSRAM)
  |-- QSPI flash
  |-- RF diplexer -> coaxial antenna connector
  |-- I2C -> onboard sensors and battery fuel gauge
  |-- SPI -> microSD and J20 expansion connector
  |-- GPIO -> RGB LED, buttons, and edge pads
  `-- QWIIC -> external I2C devices
```

## Naming Rules

- Product name: **UNIT PULSAR ESP32-C5**.
- Product family: **UNIT DevLab ecosystem**.
- Existing source assets retain their published filenames and revisions.
- Generated Product Reference files use
  `unit_product_reference_v_0_1_0_pulsar_esp32_c5.*`.

## Hardware

| RefDes | Component | Confirmed role |
|---|---|---|
| IC1 | ESP32-C5HR8 | Main SoC with in-package 8 MB PSRAM |
| IC3 | BY25Q64ESHIG | 64 Mbit (8 MiB) external QSPI flash |
| IC2 | BQ24074RGTR | Single-cell Li-Ion charger and power path |
| U2 | SGM6029CYG | 3.3 V buck regulator |
| IC4 | MAX17048G+T10 | Battery fuel gauge |
| A2 | LPS22HBTR | Barometric pressure sensor |
| U$36 | FHT40-DD-TR | Humidity and temperature sensor |
| U3 | VCNL4040M3OE | Proximity and ambient-light sensor |
| U4 | MMC5603NJ | Three-axis magnetometer |
| U1 | LFD182G45DCHD277 | 2.4 GHz / 5 GHz RF diplexer |
| J2 | RF1-2626-125CE-CT | RF coaxial antenna connector |
| MICRO_SD-HOLDER | 47309-2651 | microSD socket |
| LED1 | WS2812 1010 | Addressable RGB LED |
| J1 | HCZZ0032-4 | Four-position QWIIC connector |
| J20 | SM06B-SRSS-TB | Six-position SPI expansion connector |
| S1, S2 | 1TS026A-1600-0553-CT | BOOT and RESET buttons |

[Revision V1.2.3 electrical schematic](unit_sch_v_1_2_3_ue0134_pulsar_esp32_c5.pdf)

Product-specific top and bottom board photographs are pending publication.

## Pinout

![UNIT PULSAR ESP32-C5 2D pinout](../tools/product-reference/assets/pulsar_c5_pinout_.png)

| Board label | ESP32-C5 connection | Function / status |
|---|---|---|
| `TX0` / `D1` | GPIO11 | UART0 transmit |
| `RX0` / `D0` | GPIO12 | UART0 receive |
| `D2` / `RGB` | GPIO0 | WS2812 data input |
| `D3` / `BOOT` | GPIO28 | BOOT button; strapping pin |
| `D5` | GPIO23 | microSD chip select |
| `D10` / `SS` | GPIO10 | J20 SPI chip select |
| `D11` / `MOSI` | GPIO7 | SPI MOSI; microSD and J20 |
| `D12` / `MISO` | GPIO26 | SPI MISO; microSD and J20 |
| `D13` / `SCK` | GPIO27 | SPI clock; microSD and J20 |
| `A1` / `D15` | GPIO1 / ADC1_CH0 | QWIIC SDA |
| `SDA` / `D18` | GPIO2 / ADC1_CH1 | Onboard sensor I2C data |
| `SCL` / `D19` | GPIO3 / ADC1_CH2 | Onboard sensor I2C clock |
| `A6` / `D21` | GPIO6 / ADC1_CH5 | QWIIC SCL |
| `D8`, `D9` | GPIO13, GPIO14 | Native USB D−, D+ |
| `3V3` | 3.3 V rail | Regulated logic supply |
| `VIN` | Board input | Nominal 5 V; board-level range not characterized |
| `VBAT` | Battery rail | Single-cell LiPo connection through a solder jumper |
| `GND` | Ground | Common return |

The 2D artwork labels `D2` as GPIO8, while the schematic maps the `D2`
header position to the RGB input on GPIO0. Verify this conflict on the board
before using that connection. The full 30-position mapping, shared resources,
and other schematic-label caveats are in the [Product Reference pinout source](../tools/product-reference/chapters/04-connectors-pinouts.md).

## Dimensions

A controlled dimension drawing and mounting-hole coordinates for V1.2.3 are
not available. Do not derive dimensions from the 2D pinout image.

## Topology

The ESP32-C5HR8 connects to external flash, a dual-band RF path, the onboard
sensors and fuel gauge over I2C, microSD over SPI, and a WS2812 RGB LED. The
QWIIC connector is on a separate I2C bus from the onboard sensors. See the
[functional overview](../tools/product-reference/chapters/03-functional-overview.md)
for the full component and solder-jumper mapping.

## Pin & Connector Layout

- **USB-C:** board power, USB Serial/JTAG programming, and USB data.
- **J1 QWIIC:** GND, 3.3 V, GPIO1/SDA, and GPIO6/SCL.
- **J20 SPI:** external SPI expansion sharing the microSD bus.
- **J2 RF:** coaxial antenna connector; no onboard antenna is fitted.
- **Battery connectors:** JP1 and J3 provide parallel battery connections.
- **Edge pads:** two 15-position rows expose power, serial, SPI, analog, and
  digital signals; see the full pinout source for positions and shared use.

Connector and cable orientation must be confirmed against the physical board
before assembly.

## Functional Description

The ESP32-C5HR8 boots from external QSPI flash and provides in-package PSRAM,
2.4/5 GHz Wi-Fi 6, Bluetooth LE, and IEEE 802.15.4 connectivity. Onboard
sensors measure pressure, humidity and temperature, proximity and ambient
light, and magnetic field. The MAX17048 reports battery state; microSD offers
removable storage. BOOT and RESET switches control the programming workflow.

All onboard digital interfaces use the 3.3 V logic domain. The 3.3 V rail is
shared by the SoC, peripherals, and external loads, so regulator component
ratings do not equal the current available at an expansion connector.

## Applications

- ESP32-C5 firmware and peripheral prototyping
- Dual-band wireless and IEEE 802.15.4 development
- Environmental and magnetic sensing
- microSD data logging
- Battery-powered applications

## References

- [UNIT PULSAR ESP32-C5 repository](https://github.com/UNIT-Electronics-MX/unit_pulsar_esp32_c5)
- [Product Reference](../docs/hardware/unit_product_reference_v_0_1_0_pulsar_esp32_c5.pdf)
- [Product Reference source](../tools/product-reference/README.md)
- [V1.2.3 schematic](unit_sch_v_1_2_3_ue0134_pulsar_esp32_c5.pdf)
