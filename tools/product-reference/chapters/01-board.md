## **1 The Board**

The UNIT PULSAR ESP32-C5 is a development board for evaluating the Espressif
ESP32-C5HR8 and building connected applications that combine dual-band Wi-Fi 6,
Bluetooth LE, and IEEE 802.15.4 radio with environmental sensing, removable
storage, battery operation, and general-purpose I/O. Schematic revision V1.2.3
places the SoC, flash, RF diplexer, power-management devices, sensors,
indicators, and user controls on a Nano-style board with two parallel edge-pad
rows.

### **1.1 Accessories** {.section-page}

The following optional accessories are recommended for use with the UNIT
PULSAR ESP32-C5. Select accessories according to the interfaces and features
required by the application.

| Accessory | Purpose | Selection notes |
|---|---|---|
| Dual-band antenna with matching coaxial plug | Wi-Fi, Bluetooth LE, and 802.15.4 radio operation | Must cover 2.4 GHz and 4.9–5.95 GHz and mate with the J2 RF1-2626 connector; no onboard antenna is fitted |
| [QWIIC cable](https://uelectronics.com/producto/arnes-qwiic-4-pines-pitch-1mm/) | External I2C expansion | 4-pin, 1 mm pitch cable for connecting compatible 3.3 V QWIIC devices |
| USB-C data cable | Power, programming, and USB serial | Must support data; a charge-only cable cannot upload firmware |
| [MicroSD card](https://uelectronics.com/producto/memoria-micro-sd-kingston-16-64-gb-clase-10/) | Removable storage and data logging | Class 10 microSD card; FAT32 formatting is recommended |
| [LiPo 3.7 V 650 mAh Battery](https://uelectronics.com/producto/bateria-lipo-3-7v-650mah-802535/) | Battery-powered operation | Single-cell LiPo battery; verify polarity and connector orientation before connection |

Verify connector type, orientation, polarity, voltage domain, and pinout before
connecting accessories.

### **1.2 Board Identification**

| Item | Value |
|---|---|
| Product | UNIT PULSAR ESP32-C5 |
| SKU | UE0134 |
| Product family | UNIT DevLab ecosystem |
| Product type | Multi-interface ESP32-C5 development board |
| Main component | Espressif ESP32-C5HR8, QFN-48 (6 × 6 mm) |
| Hardware revision | V1.2.3 (schematic) |
| Product Reference | Version 0.1.0 |
| Primary programming interface | USB-C / ESP32-C5 USB Serial/JTAG |
| Debug interface | USB Serial/JTAG through USB-C |

Hardware revision and documentation revision are identified independently.

### **1.3 Main Assemblies**

| RefDes | Component | Function |
|---|---|---|
| IC1 | ESP32-C5HR8 | Main SoC with 8 MB in-package PSRAM and 2.4/5 GHz radio |
| IC3 | BY25Q64ESHIG | 64 Mbit (8 MiB) external quad SPI flash |
| IC2 | BQ24074RGTR | Single-cell Li-Ion charger with power path |
| U2 | SGM6029CYG | 1 A buck regulator for the 3.3 V rail |
| IC4 | MAX17048G+T10 | Battery fuel gauge |
| A2 | LPS22HBTR | Barometric pressure sensor |
| U$36 | FHT40-DD-TR | Humidity and temperature sensor |
| U3 | VCNL4040M3OE | Proximity and ambient-light sensor |
| U4 | MMC5603NJ | Three-axis magnetometer |
| U1 | LFD182G45DCHD277 | 2.4 GHz / 5 GHz RF diplexer |
| J2 | RF1-2626-125CE-CT | RF coaxial antenna connector |
| XTAL1 | SX0B48.000F0810F30 | 48 MHz main crystal |
| MICRO_SD-HOLDER | 47309-2651 | Removable microSD storage |
| LED1 | WS2812 1010 | Addressable RGB indicator |
| J1 | HCZZ0032-4 | Four-position QWIIC-style I2C connector |
| J20 | SM06B-SRSS-TB | Six-position, 1 mm SPI expansion connector |
| JP1, J3 | PH 2.0 mm 2P, 1.25 mm 2P | Parallel battery connections |
| S1, S2 | 1TS026A-1600-0553-CT | BOOT and RESET push buttons |

### **1.4 Board Views** {.section-page}

Top-side and bottom-side board photographs for revision V1.2.3 are not yet
available. Refer to the schematic in Section 9.1 for component designators.

### **1.5 Handling** {.section-page}

Handle the board using normal ESD precautions. Avoid touching the RF
connector, sensor apertures, connector contacts, or exposed pads. Remove power
before connecting or removing the battery. Connect an antenna to J2 before
enabling radio transmission. Keep conductive objects away from the battery and
microSD areas when the board is energized.
