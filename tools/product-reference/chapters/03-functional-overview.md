## **3 Functional Overview**

The UNIT PULSAR ESP32-C5 is organized around the ESP32-C5HR8 SoC, with an
external flash, a dual-band RF path, fixed onboard sensors, and a battery
power path. Firmware can use each block independently or combine wireless
connectivity, sensing, storage, and user feedback in one application.

### **3.1 Block Diagram** {.section-page}

A block-diagram drawing for revision V1.2.3 is not yet available. The
functional relationships are:

```text
USB-C / VIN / battery
          |
   BQ24074 power path ---- MAX17048 fuel gauge
          |
   SGM6029 buck -> 3.3 V
          |
     ESP32-C5HR8 (8 MB PSRAM in package)
   +------+--------+--------+-------+---------+
   |      |        |        |       |         |
 QSPI    RF      I2C       SPI    WS2812    GPIO
 flash  diplexer  sensors  microSD  RGB     + QWIIC
         + J2               + J20
```

The ESP32-C5HR8 controls every data path. The BY25Q64 flash stores executable
code and nonvolatile application data. The in-package PSRAM extends working
memory. The microSD socket provides removable storage. The pressure,
humidity/temperature, proximity/light, and magnetic sensors and the battery
fuel gauge share an internal I2C bus. QWIIC, J20, and the edge pads expose
expansion signals, while the RF connector and RGB LED provide wireless and
visual outputs.

### **3.2 Board Topology** {.section-page}

| RefDes | Component | Confirmed role |
|---|---|---|
| IC1 | ESP32-C5HR8 | Main SoC with in-package 8 MB PSRAM |
| IC3 | BY25Q64ESHIG | 64 Mbit (8 MiB) QSPI flash memory |
| IC2 | BQ24074RGTR | Single-cell Li-Ion/LiPo charger with power path |
| U2 | SGM6029CYG | 4 MHz, 1 A buck regulator for the 3.3 V rail |
| L2 | 470 nH inductor | SGM6029 output inductor |
| IC4 | MAX17048G+T10 | Battery fuel gauge |
| A2 | LPS22HBTR | Barometric pressure sensor |
| U$36 | FHT40-DD-TR | Humidity and temperature sensor |
| U3 | VCNL4040M3OE | Proximity and ambient-light sensor |
| U4 | MMC5603NJ | Three-axis magnetometer |
| U1 | LFD182G45DCHD277 | 2.4 GHz / 5 GHz RF diplexer |
| J2 | RF1-2626-125CE-CT | RF coaxial antenna connector |
| XTAL1 | 48 MHz crystal | ESP32-C5 main clock source |
| MICRO_SD-HOLDER | 47309-2651 | microSD card socket on SPI signals |
| LED1 | WS2812 1010 | Addressable RGB LED |
| J1 | HCZZ0032-4 | Four-position, 1 mm pitch QWIIC connector |
| J20 | SM06B-SRSS-TB | Six-position, 1 mm pitch SPI connector |
| NANO1 | USB-C connector | USB power, programming, and USB data interface |
| JP1, J3 | Battery connectors | PH 2.0 mm and 1.25 mm, wired in parallel |
| S1 | BOOT switch | Pulls GPIO28 low to enter download boot mode |
| S2 | RESET switch | Pulls `CHIP_PU` low |
| D1, D4 | SDM2U30CSP-7B | Schottky diodes from VBUS and VIN to `+5V` |

The board also provides solder jumpers that connect optional signals:

| Solder jumper function | Connection |
|---|---|
| 3.3 V regulator enable | SGM6029 `EN` to header position 3 (`EN`) |
| Battery access | `VBAT` to header position 4 |
| RGB chain extension | LED1 `DO` to header position 11 |
| Ground | GND to header position 13 (Nano `RESET` position) |
| Sensor bus | `SDA_SENSOR` / `SCL_SENSOR` to GPIO2 / GPIO3 |
| Fuel-gauge alert | MAX17048 `ALERT` to `D5` / GPIO23 |
| Fuel-gauge quick start | MAX17048 `QSTRT` to `D6` / GPIO24 |
| Charger power good | BQ24074 `PGOOD` to `D7` / GPIO25 |
| Proximity interrupt | VCNL4040 `INT` to `A6` / GPIO6 |

The default state of each solder jumper is not stated in the schematic; verify
it on the assembled board.

The two edge-pad rows expose power, analog, serial, SPI, and general-purpose
signals. Several of these signals are shared with onboard devices, so firmware
ownership must be decided before reusing them.

### **3.3 Processor** {.section-page}

The ESP32-C5HR8 is the main controller. According to the Espressif component
datasheet, the device provides a high-performance 32-bit RISC-V core up to
240 MHz, a low-power RISC-V core up to 48 MHz, 384 KB of HP SRAM, 16 KB of LP
SRAM, and 320 KB of ROM.

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

The BY25Q64ESHIG provides 64 Mbit (8 MiB) of external quad SPI flash on the
dedicated flash pins (GPIO15–GPIO18 and GPIO20–GPIO22). It stores the
bootloader, partition table, application firmware, and any filesystem or data
region defined by the selected partition scheme. These pins are not available
for other uses.

The ESP32-C5HR8 includes 8 MB of quad SPI PSRAM inside the package. In the
Arduino workflow, PSRAM must be enabled in the board options; ESP-IDF enables
it through `menuconfig`. PSRAM is suited to network buffers, file buffers,
sensor history, and large application structures. Interrupt state and
timing-critical data should remain in internal SRAM. The application must
check allocation results.

### **3.5 RF Path and Antenna Connector** {.section-page}

The ESP32-C5HR8 provides separate RF ports for the 2.4 GHz band (`ANT_2G`,
pin 42) and the 5 GHz band (`ANT_5G`, pin 48). The U1 LFD182G45DCHD277
diplexer combines both ports into a single path covering 2.4–2.5 GHz and
4.9–5.95 GHz, which is routed to the J2 RF1-2626-125CE-CT coaxial connector.

| Path | Series element | Shunt positions |
|---|---|---|
| J2 to diplexer common | L5 (0 Ω) | C20, C21 (not populated) |
| Diplexer LF to `ANT_2G` | L1 (0 Ω) | C7, C9 (not populated) |
| Diplexer HF to `ANT_5G` | L3 (0 Ω) | C18, C4 (not populated) |

The 0 Ω links and unpopulated capacitor positions form tuning networks that
can be adjusted for a specific antenna. No onboard antenna is fitted. Connect
a dual-band antenna to J2 before enabling radio transmission.

### **3.6 MicroSD Card Socket** {.section-page}

The onboard 47309-2651 microSD socket is connected in SPI mode and operates in
the 3.3 V logic domain.

| Socket signal | GPIO | SPI role |
|---|---:|---|
| `CLK` | 27 | SCK |
| `CMD` | 7 | MOSI |
| `DAT0` | 26 | MISO |
| `DAT3` | 23 | Chip select |
| `DAT1`, `DAT2` | — | Not connected |

SCK, MOSI, and MISO are shared with the J20 connector and the `D11`–`D13`
header positions. External SPI devices must use their own chip select, such as
GPIO10 on J20. GPIO23 is also the optional MAX17048 `ALERT` input.

File writes should be flushed and closed before card extraction or power
removal to reduce the risk of filesystem corruption. FAT32 is recommended.

### **3.7 LED Indicators** {.section-page}

LED1 is a WS2812 1010 addressable RGB LED driven by GPIO0 (`NEOP_DIN`), which
is also available at header position 20. A solder jumper can connect LED1 `DO`
to header position 11, allowing the chain to be extended with compatible
external addressable LEDs.

The board also includes a user indicator (`BUILTIN2`, pink) on GPIO27 through
a 10 kΩ resistor, a red power indicator on the 3.3 V rail, and an orange
charge-status indicator driven by the BQ24074 `CHG` output.

Applications should limit RGB brightness when power consumption or thermal
rise is important.

### **3.8 BQ24074 and SGM6029 Power Management System** {.section-page}

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
| `PGOOD` | Optional solder jumper to `D7` / GPIO25 |

U2 is an SGM6029CYG 4 MHz synchronous buck regulator. It converts `BMS_OUT`
into the regulated 3.3 V rail, with the output voltage selected by R14 on the
`VSEL/MODE` pin. The regulator is rated for up to 1 A. Its `EN` pin is pulled
up to `BMS_OUT` by R8 (10 kΩ) and can be connected to header position 3
through a solder jumper for external control of the 3.3 V rail.

The battery input is intended for a single-cell LiPo battery with a nominal
voltage of 3.7 V and a maximum fully charged voltage of 4.2 V. JP1 (PH 2.0 mm)
and J3 (1.25 mm) are wired in parallel to `VBAT`.

### **3.9 Power Tree** {.section-page}

```text
USB-C VBUS --D1--+
                 +--> +5V --> BQ24074 IN
VIN -------D4----+              |
                                +--> BMS_OUT --> SGM6029 --> 3.3 V
VBAT (JP1 / J3) <--> BQ24074 BAT
        |
        +--> MAX17048 CELL / VDD
```

The power tree summarizes the main supply domains and their relationship. It
is intended as a functional reference and does not replace the complete
schematic.

The 3.3 V rail is available through the edge pads, the QWIIC connector, and
J20. The total 3.3 V load, including radio transmission peaks, must remain
within the 1 A regulator output capability.

### **3.10 Environmental and Magnetic Sensors** {.section-page}

Four sensors share the internal `SDA_SENSOR` / `SCL_SENSOR` I2C bus with the
fuel gauge. R15 and R16 (4.7 kΩ) pull the bus up to 3.3 V, and solder jumpers
connect it to GPIO2 (`SDA`) and GPIO3 (`SCL`).

| Device | Measurement | I2C address | Notes |
|---|---|---|---|
| LPS22HBTR (A2) | Barometric pressure, 260–1260 hPa | `0x5C` | `SA0` tied to GND; `INT_DRDY` not connected |
| FHT40-DD-TR (U$36) | Relative humidity and temperature | `0x44` | SHT40-compatible default; verify for the assembled part |
| VCNL4040M3OE (U3) | Proximity and ambient light | `0x60` | `INT` to GPIO6 through optional solder jumper |
| MMC5603NJ (U4) | Three-axis magnetic field | `0x30` | Compass and orientation applications |

Typical applications include weather stations, indoor air monitoring, altitude
estimation, gesture or presence detection, automatic display brightness, and
compass headings. Measurement range, filtering, output data rate, and
interrupt behavior are configured through the sensor registers or software
driver and are not fixed by the PCB.

### **3.11 MAX17048 Fuel Gauge** {.section-page}

IC4 is a MAX17048G+T10 single-cell fuel gauge with ModelGauge. Its `CELL` and
`VDD` pins connect to `VBAT`, and it communicates on the sensor I2C bus at
address `0x36`. Firmware can read battery voltage and state of charge without
an external sense resistor.

The `ALERT` output can be connected to `D5` / GPIO23 and the `QSTRT` input to
`D6` / GPIO24 through optional solder jumpers. Because GPIO23 is also the
microSD chip select, applications using both functions must account for this
shared connection before closing the `ALERT` jumper.

### **3.12 I2C and QWIIC Expansion** {.section-page}

The UNIT PULSAR ESP32-C5 provides two independent I2C routes, allowing the
onboard sensors and external QWIIC devices to operate on separate buses.

| Interface | SDA | SCL | Primary use |
|---|---:|---:|---|
| Sensor I2C | GPIO2 | GPIO3 | Onboard sensors, fuel gauge, and edge-pad access |
| QWIIC I2C | GPIO1 | GPIO6 | External QWIIC devices |

GPIO2 and GPIO3 are also exposed at the `SDA` / `SCL` edge pads and are
ESP32-C5 strapping pins (MTMS, MTDI). GPIO1 and GPIO6 are also exposed at the
`A1` and `A6` edge pads.

Both interfaces operate in the 3.3 V logic domain. Device addresses, pull-up
configuration, bus capacitance, and cable length must be considered when
multiple devices are connected to the same bus.

### **3.13 Combined System Operation** {.section-page}

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
