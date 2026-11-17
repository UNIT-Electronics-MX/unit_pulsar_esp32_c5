## **3 Functional Overview**

The UNIT PULSAR ESP32-C5 is organized around the ESP32-C5HR8 SoC, with an
external flash, a dual-band RF path, fixed onboard sensors, and a battery
power path. Firmware can use each block independently or combine wireless
connectivity, sensing, storage, and user feedback in one application.

The board is built from eight functional groups: USB-C and controls, the
ESP32-C5HR8 processor, onboard sensors, indicators, battery and power,
microSD and SPI, radio and antenna, and the edge pads with QWIIC expansion.

### **3.1 Block Diagram** {.section-page}

![UNIT PULSAR ESP32-C5 functional block diagram](tools/product-reference/assets/unit_block_diagram_v1_2_3_ue0134_pulsar_esp32_c5.png){width=7.0in}

The ESP32-C5HR8 controls every data path. The BY25Q64 flash stores executable
code and nonvolatile application data. The in-package PSRAM extends working
memory. The microSD socket provides removable storage. The pressure,
humidity/temperature, proximity/light, and magnetic sensors and the battery
fuel gauge share an internal I2C bus. QWIIC, J20, and the edge pads expose
expansion signals, while the RF connector and RGB LED provide wireless and
visual outputs.

### **3.2 Board Topology** {.section-page}

![Views of the UNIT PULSAR ESP32-C5 board topology](tools/product-reference/assets/unit_topology_v1_2_3_ue0134_devlab_pulsar_esp32_c5.png){width=7.2in}

#### **Top Side**

| Ref. | Description | Ref. | Description |
|---|---|---|---|
| IC1 | ESP32-C5HR8 SoC with 8 MB in-package PSRAM | IC2 | BQ24074RGTR Li-Ion/LiPo charger with power path |
| IC3 | BY25Q64ESHIG 64 Mbit (8 MiB) QSPI flash | IC4 | MAX17048G+T10 battery fuel gauge |
| U1 | LFD182G45DCHD277 2.4 GHz / 5 GHz RF diplexer | U2 | SGM6029CYG 4 MHz, 1 A buck regulator (3.3 V) |
| U3 | VCNL4040M3OE proximity and ambient-light sensor | U4 | MMC5603NJ three-axis magnetometer |
| U$36 | FHT40-DD-TR humidity and temperature sensor | A2 | LPS22HBTR barometric pressure sensor |
| XTAL1 | 48 MHz main crystal | MICRO_SD-HOLDER | 47309-2651 microSD card socket |
| J1 | HCZZ0032-4 QWIIC I2C connector | J2 | RF1-2626-125CE-CT RF coaxial antenna connector |
| J4 | USB-C connector | LED1 | WS2812 1010 addressable RGB LED |
| S1 | BOOT push button (GPIO28) | S2 | RESET push button (`CHIP_PU`) |
| PWR | Red power LED (3.3 V rail) | CHG | Orange charge LED (BQ24074 `CHG`) |
| BUILTIN2 | Pink user LED (GPIO27) | JP-0, JP-1 | 15-position edge-pad rows (Nano layout) |

#### **Bottom Side**

| Ref. | Description | Ref. | Description |
|---|---|---|---|
| JP1 | PH 2.0 mm two-pin battery connector | J3 | 1.25 mm two-pin battery connector |
| J20 | SM06B-SRSS-TB six-position, 1 mm SPI connector | | |

The bottom side also carries the configuration solder jumpers:

| Ref. | Silkscreen | Connection | Default state |
|---|---|---|---|
| EN_PAD | `3EN` | SGM6029 `EN` to header position 3 (`EN`) | Open |
| EN_PAD1 | `VBAT` | `VBAT` to header position 4 | Open |
| EN_PAD3 | `PGOOD` | BQ24074 `PGOOD` to `D7` / GPIO25 | Open |
| EN_PAD5 | `QSTRT` | MAX17048 `QSTRT` to `D6` / GPIO24 | Open |
| EN_PAD2 | `ALERT` | MAX17048 `ALERT` to `D5` / GPIO23 | Open |
| JP2, JP3 | `Sensor BUS SDA` / `SCL` | `SDA_SENSOR` / `SCL_SENSOR` to GPIO2 / GPIO3 | Closed |
| — | `VCN Int` | VCNL4040 `INT` to `A6` / `D21` / GPIO6 | Open |
| EN_PAD4 | `RGB` | LED1 `DO` to header position 11 (`NEOPIXEL DOUT`) | Open |
| GND_PAD | `GND` | GND to header position 13 (Nano `RESET` position) | Open |

![Bottom-side solder jumpers and battery connectors](tools/product-reference/assets/unit_detail_jumpers_v1_2_3_ue0134_pulsar_esp32_c5.png){width=6.5in}

Bridge an open pad to enable its connection. Cut the trace between a closed
pad pair to disconnect it; opening JP2 and JP3 disconnects the onboard sensor
block from GPIO2 and GPIO3.

Additional power-path components:

| Ref. | Description |
|---|---|
| D1, D4 | SDM2U30CSP-7B Schottky diodes from VBUS and VIN to `+5V` |
| L2 | 470 nH SGM6029 output inductor |

### **3.3 Processor** {.section-page}

The ESP32-C5HR8 (IC1) is the main controller. According to the Espressif
component datasheet, the device provides a high-performance 32-bit RISC-V core
up to 240 MHz, a low-power RISC-V core up to 48 MHz, 384 KB of HP SRAM, 16 KB
of LP SRAM, and 320 KB of ROM. The HR8 variant adds 8 MB of in-package PSRAM,
and the board adds 8 MiB of external QSPI flash.

The radio supports dual-band 2.4 GHz and 5 GHz Wi-Fi 6 (802.11ax) together
with 802.11a/b/g/n, Bluetooth LE (Bluetooth Core 6.0 certified), and IEEE
802.15.4 for Thread 1.4 and Zigbee 3.0.

The peripheral set includes a 12-bit SAR ADC with up to six channels, UART,
I2C, I2S, a general-purpose SPI port, USB Serial/JTAG, two CAN FD controllers,
LED PWM, MCPWM, RMT, PARLIO, and GDMA. The PULSAR design routes a selected
subset of these functions to onboard devices and external connectors.

The ESP32-C5 does not include an SD host controller; the microSD socket is
therefore wired for SPI mode.

### **3.4 Memory Architecture: Flash and PSRAM** {.section-page}

The BY25Q64ESHIG (IC3) provides 64 Mbit (8 MiB) of external quad SPI flash on
the dedicated flash pins (GPIO15–GPIO18 and GPIO20–GPIO22). It stores the
bootloader, partition table, application firmware, and any filesystem or data
region defined by the selected partition scheme. These pins are not available
for other uses.

The ESP32-C5HR8 includes 8 MB of quad SPI PSRAM inside the package. Because
the PSRAM die is stacked within the SoC package, it does not appear as a
separate component in the schematic or on the PCB; the BY25Q64ESHIG is the
only discrete memory device. In the
Arduino workflow, PSRAM must be enabled in the board options; ESP-IDF enables
it through `menuconfig`. PSRAM is suited to network buffers, file buffers,
sensor history, and large application structures. Interrupt state and
timing-critical data should remain in internal SRAM. The application must
check allocation results.

### **3.5 Radio and Antenna Connector** {.section-page}

The ESP32-C5HR8 provides 2.4/5 GHz Wi-Fi 6, Bluetooth LE 5, and IEEE 802.15.4
through separate RF ports for the 2.4 GHz band (`ANT_2G`, pin 42) and the
5 GHz band (`ANT_5G`, pin 48). The U1 LFD182G45DCHD277 diplexer combines both
ports into a single path covering 2.4–2.5 GHz and 4.9–5.95 GHz, which is
routed to the J2 RF1-2626-125CE-CT coaxial connector.

| Path | Series element | Shunt positions |
|---|---|---|
| J2 to diplexer common | L5 (0 Ω) | C20, C21 (not populated) |
| Diplexer LF to `ANT_2G` | L1 (0 Ω) | C7, C9 (not populated) |
| Diplexer HF to `ANT_5G` | L3 (0 Ω) | C18, C4 (not populated) |

![Coaxial RF connector J2](tools/product-reference/assets/unit_detail_rf_v1_2_3_ue0134_pulsar_esp32_c5.png){height=2.6in}

The 0 Ω links and unpopulated capacitor positions form tuning networks that
can be adjusted for a specific antenna. No onboard antenna is fitted. Connect
a dual-band antenna to J2 before enabling radio transmission.

### **3.6 USB-C and Controls** {.section-page}

The USB-C connector (J4) is the single port for board power, firmware
programming, and the serial console. Its D- and D+ lines connect to the
ESP32-C5 USB Serial/JTAG controller on GPIO13 and GPIO14 through 22 Ω series
resistors (R9, R10). CC1 and CC2 have 5.1 kΩ pull-down resistors (R5, R6).
VBUS reaches the `+5V` node through Schottky diode D1.

| Control | Signal | Function |
|---|---|---|
| S1 BOOT | GPIO28 | Pulls GPIO28 low; held during reset to enter download boot mode |
| S2 RESET | `CHIP_PU` | Pulls `CHIP_PU` low to restart the ESP32-C5; 10 kΩ pull-up (R1) and 100 nF (C3) |

The BOOT signal is also available at header position 21 (`D3`), and the reset
signal at header position 18 (`RST`).

### **3.7 Indicators** {.section-page}

LED1 is a WS2812 1010 addressable RGB LED driven by GPIO0 (`NEOP_DIN`), which
is also available at header position 20. Solder jumper EN_PAD4 can connect
LED1 `DO` to header position 11, allowing the chain to be extended with
compatible external addressable LEDs.

![WS2812 RGB LED LED1](tools/product-reference/assets/unit_detail_rgb_v1_2_3_ue0134_pulsar_esp32_c5.png){height=2.6in}

| Ref. | Color | Driven by |
|---|---|---|
| LED1 | RGB (WS2812 1010) | GPIO0 (`NEOP_DIN`) |
| BUILTIN2 | Pink | GPIO27 through 10 kΩ (R24) |
| PWR | Red | 3.3 V rail through 10 kΩ (R4) |
| CHG | Orange | BQ24074 `CHG` output through 1.5 kΩ (R2) |

Applications should limit RGB brightness when power consumption or thermal
rise is important.

### **3.8 MicroSD Card Socket and J20 SPI Connector** {.section-page}

The onboard 47309-2651 microSD socket is connected in SPI mode and operates in
the 3.3 V logic domain. The microSD slot and the six-pin J20 connector share
the SPI bus.

| Socket signal | GPIO | SPI role |
|---|---:|---|
| `CLK` | 27 | SCK |
| `CMD` | 7 | MOSI |
| `DAT0` | 26 | MISO |
| `DAT3` | 23 | Chip select |
| `DAT1`, `DAT2` | — | Not connected |

![microSD socket](tools/product-reference/assets/unit_detail_microsd_v1_2_3_ue0134_pulsar_esp32_c5.png){height=2.8in}
![J20 SPI connector](tools/product-reference/assets/unit_detail_j20_v1_2_3_ue0134_pulsar_esp32_c5.png){height=2.8in}

SCK, MOSI, and MISO are shared with the J20 connector and the `D11`–`D13`
header positions. External SPI devices must use their own chip select, such as
GPIO10 on J20. GPIO23 is also the optional MAX17048 `ALERT` input.

File writes should be flushed and closed before card extraction or power
removal to reduce the risk of filesystem corruption. FAT32 is recommended.

### **3.9 Battery and Power: BQ24074 and SGM6029** {.section-page}

IC2 is a BQ24074RGTR single-cell Li-Ion/LiPo charger with dynamic power-path
management. Its `IN` pin is fed from the `+5V` node, which combines USB VBUS
(through D1) and `VIN` (through D4). Its `OUT` pin (`BMS_OUT`) supplies the
system while the battery charges.

| BQ24074 pin | Board configuration |
|---|---|
| `ISET` | R13 = 3.57 kΩ; 250 mA nominal charge current |
| `ILIM` | R12 = 1.1 kΩ; ≈1.4 A input current limit |
| `EN1`, `EN2` | GND and `BMS_OUT`; input limit set by `ILIM` |
| `CE` | GND; charging enabled |
| `TS` | 10 kΩ to GND (R11); no battery thermistor |
| `CHG` | Orange charge LED |
| `PGOOD` | Solder jumper EN_PAD3 to `D7` / GPIO25 |

U2 is an SGM6029CYG 4 MHz synchronous buck regulator. It converts `BMS_OUT`
into the regulated 3.3 V rail, with the output voltage selected by R14 on the
`VSEL/MODE` pin. The regulator is rated for up to 1 A. Its `EN` pin is pulled
up to `BMS_OUT` by R8 (10 kΩ) and can be connected to header position 3
through solder jumper EN_PAD for external control of the 3.3 V rail.

The battery input is intended for a single-cell LiPo battery with a nominal
voltage of 3.7 V and a maximum fully charged voltage of 4.2 V. JP1 (PH 2.0 mm)
and J3 (1.25 mm) are wired in parallel to `VBAT`.

### **3.10 MAX17048 Fuel Gauge** {.section-page}

IC4 is a MAX17048G+T10 single-cell fuel gauge with ModelGauge. Its `CELL` and
`VDD` pins connect to `VBAT`, and it communicates on the sensor I2C bus at
address `0x36`. Firmware can read battery voltage and state of charge without
an external sense resistor.

The `ALERT` output can be connected to `D5` / GPIO23 through EN_PAD2, and the
`QSTRT` input to `D6` / GPIO24 through EN_PAD5. Because GPIO23 is also the
microSD chip select, applications using both functions must account for this
shared connection before closing EN_PAD2.

### **3.11 Power Tree** {.section-page}

![UNIT PULSAR ESP32-C5 power tree](tools/product-reference/assets/unit_power_tree_v1_2_3_ue0134_pulsar_esp32_c5.png){width=7.0in}

The power tree summarizes the main supply domains and their relationship. It
is intended as a functional reference and does not replace the complete
schematic.

The 3.3 V rail is available through the edge pads, the QWIIC connector, and
J20. The total 3.3 V load, including radio transmission peaks, must remain
within the 1 A regulator output capability.

### **3.12 Sensors** {.section-page}

Four sensors share the internal `SDA_SENSOR` / `SCL_SENSOR` I2C bus with the
fuel gauge. R15 and R16 (4.7 kΩ) pull the bus up to 3.3 V, and solder jumpers
JP2 and JP3 connect it to GPIO2 (`SDA`, `D18`) and GPIO3 (`SCL`, `D19`).

![Onboard sensor block on the sensor I2C bus](tools/product-reference/assets/unit_detail_sensors_v1_2_3_ue0134_pulsar_esp32_c5.png){width=7.0in}

| Ref. | Device | Measurement | I2C address | Notes |
|---|---|---|---|---|
| A2 | LPS22HBTR | Barometric pressure, 260–1260 hPa | `0x5C` | `SA0` tied to GND; `INT_DRDY` not connected |
| U$36 | FHT40-DD-TR | Relative humidity and temperature | `0x44` | |
| U3 | VCNL4040M3OE | Proximity and ambient light | `0x60` | `INT` to GPIO6 through the `VCN Int` solder jumper |
| U4 | MMC5603NJ | Three-axis magnetic field | `0x30` | Compass and orientation applications |
| IC4 | MAX17048G+T10 | Battery voltage and state of charge | `0x36` | See Section 3.10 |

Typical applications include weather stations, indoor air monitoring, altitude
estimation, gesture or presence detection, automatic display brightness, and
compass headings. Measurement range, filtering, output data rate, and
interrupt behavior are configured through the sensor registers or software
driver and are not fixed by the PCB.

### **3.13 Pins and QWIIC Expansion** {.section-page}

The board exposes 30 Nano-style positions on two 15-position edge-pad rows
(JP-0 and JP-1). Several of these signals are shared with onboard devices, so
firmware ownership must be decided before reusing them. Chapter 4 lists the
complete pinout.

The UNIT PULSAR ESP32-C5 provides two independent I2C routes, allowing the
onboard sensors and external QWIIC devices to operate on separate buses.

| Interface | SDA | SCL | Primary use |
|---|---:|---:|---|
| Sensor I2C | GPIO2 | GPIO3 | Onboard sensors, fuel gauge, and edge-pad access |
| QWIIC I2C (J1) | GPIO1 | GPIO6 | External QWIIC devices |

![QWIIC connector J1](tools/product-reference/assets/unit_detail_qwiic_v1_2_3_ue0134_pulsar_esp32_c5.png){height=2.6in}

GPIO2 and GPIO3 are also exposed at the `SDA` / `SCL` edge pads and are
ESP32-C5 strapping pins (MTMS, MTDI). GPIO1 and GPIO6 are also exposed at the
`A1` and `A6` / `D21` edge pads. The QWIIC bus has no onboard pull-up
resistors. GPIO6 is shared with the VCNL4040 `INT` output when the `VCN Int`
jumper is bridged; in that configuration, QWIIC I2C cannot be used.

Both interfaces operate in the 3.3 V logic domain. Device addresses, pull-up
configuration, bus capacitance, and cable length must be considered when
multiple devices are connected to the same bus.

### **3.14 Combined System Operation** {.section-page}

The UNIT PULSAR ESP32-C5 is designed to support concurrent operation of its
onboard subsystems. The ESP32-C5HR8 can combine wireless connectivity,
environmental sensing, battery monitoring, removable storage, and RGB
indication within the same application.

For example, sensor data can be buffered in PSRAM, logged to the microSD card,
and published over Wi-Fi or Thread, while the fuel gauge reports battery state
and the RGB LED indicates connection status.

Applications using the radio together with storage and sensors should consider
current peaks during transmission, memory allocation, task priorities, and
storage write latency. Shared GPIO functions must also be considered when
enabling optional solder-jumper connections.

### **3.15 Wireless Coexistence and Board RF Path** {.section-page}

The ESP32-C5 has Wi-Fi in the 2.4 GHz and 5 GHz bands; Bluetooth LE and
IEEE 802.15.4 use the 2.4 GHz band. Espressif describes coexistence between
the three radio families as time division in the [ESP32-C5 Series Datasheet
v1.5](https://documentation.espressif.com/esp32-c5_datasheet_en.pdf), Product
Overview. The board's U1 diplexer combines the chip's 2.4 GHz and 5 GHz RF
ports into the single J2 coaxial path. A connected antenna must cover the
intended bands and mate with J2.

| Application choice | Board consequence |
|---|---|
| 2.4 GHz Wi-Fi with Bluetooth LE or 802.15.4 | The protocols share 2.4 GHz radio time; test latency and packet loss with the final duty cycles |
| 5 GHz Wi-Fi plus 2.4 GHz protocol | Both bands use the one board RF connector and antenna; validate the complete RF assembly |
| High transmit duty cycle | Include radio peaks in the 3.3 V power budget and inspect supply stability |
| Antenna or enclosure change | Recheck range and regulatory suitability for the finished product |

No conducted output power, antenna gain, radiated range, or coexistence
throughput is characterized here for the assembled PULSAR board. Chip-level
radio figures in the Espressif datasheet do not include J2, the diplexer,
matching components, cable, antenna, or enclosure.

### **3.16 SoC Security Features and Provisioning** {.section-page}

The ESP32-C5 silicon provides secure boot, external-memory encryption,
cryptographic accelerators, a true random number generator, and eFuse-based
configuration, according to the [Espressif datasheet](https://documentation.espressif.com/esp32-c5_datasheet_en.pdf),
Section 4.1.4. These are **SoC capabilities**. The V1.2.3 PULSAR schematic
does not by itself show that a particular shipped board has keys provisioned,
secure boot enabled, or flash encryption activated.

For a product derived from this development board, decide which firmware
images must be authenticated, which data must be encrypted, and how keys and
recovery access will be managed. Record chip revision and eFuse state before
provisioning. Validate the full update and recovery process on a spare board
before applying irreversible eFuse settings to production hardware. Keep
development credentials and production credentials separate in the firmware
workflow.

The microSD socket provides removable storage but is not automatically
protected by the SoC's flash/PSRAM encryption path. An application that stores
sensitive records on a card must handle their protection explicitly.
