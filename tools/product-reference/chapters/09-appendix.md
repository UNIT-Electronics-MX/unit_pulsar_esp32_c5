## **9 Appendix**

### **9.1 Schematic** {.section-page}

The following two sheets are rendered from
`unit_sch_v_1_2_3_ue0134_pulsar_esp32_c5.pdf`. The original PDF remains the
authoritative electrical schematic for hardware revision V1.2.3.

<div class="schematic-page">

#### **9.1.1 I/Os, USB-C, Charger, 3.3 V Regulator, Antenna, ESP32-C5, Flash, and LEDs**

![](hardware/resources/unit_schematic_v_1_2_3_pulsar_esp32_c5_sheet_1.png){width=7.2in}

</div>

<div class="schematic-page">

#### **9.1.2 Battery Monitor, microSD, and Sensors**

![](hardware/resources/unit_schematic_v_1_2_3_pulsar_esp32_c5_sheet_2.png){width=7.2in}

</div>

### **9.2 Technical References** {.section-page}

The documentation set for the UNIT PULSAR ESP32-C5 is organized around the
following technical sources:

1. The hardware V1.2.3 schematic defines board connectivity, component
   designators, power architecture, and electrical implementation.

2. The bill of materials defines the assembled part numbers.

3. The official Espressif ESP32-C5 datasheet defines device-level electrical
   characteristics, processor capabilities, GPIO behavior, strapping pins, ADC
   operation, and radio functionality.

4. The UNIT Electronics Wiki provides setup procedures, peripheral usage
   documentation, application examples, and software workflows.

### **9.3 Design and Application Notes** {.section-page}

The following characteristics depend on the final application, firmware
configuration, connected peripherals, or mechanical integration:

- Total board current consumption varies with CPU activity, radio mode and
  transmit power, enabled peripherals, RGB LED brightness, microSD activity,
  and external loads.
- Available current for external 3.3 V devices depends on the portion of the
  SGM6029 1 A output capability consumed by the board itself.
- Radiated RF performance depends on the antenna, cable, enclosure, and the
  populated matching network.
- Environmental-sensor accuracy depends on self-heating, airflow, and
  enclosure design.
- Thermal performance and maximum component temperatures depend on workload,
  ambient conditions, airflow, enclosure design, and external loading.

### **9.4 Document Control** {.section-page}

| Field | Value |
|---|---|
| Product | UNIT PULSAR ESP32-C5 |
| SKU | UE0134 |
| Product family | UNIT DevLab ecosystem |
| Hardware revision | V1.2.3 |
| Product Reference | Version 0.1.0 |
| Publication date | 2026-09-24 |

#### **9.5 Source Notes** {.section-page}

- The schematic title block shows `REV: 1.2.0` on sheet 1 and `REV: 1.2.3` on
  sheet 2; the schematic file name uses V1.2.3, and the revision history is
  labeled Version 1.2. This Product Reference uses V1.2.3.

- The schematic names the humidity sensor `SHT40` and the pressure sensor
  `LPS22DFTR`; the bill of materials specifies `FHT40-DD-TR` and `LPS22HBTR`.
  This Product Reference follows the bill of materials for part numbers.

- The schematic revision history states that R12 was changed to 1.5 kΩ for an
  approximately 1 A USB charging current. The schematic and bill of materials
  show R12 = 1.1 kΩ on `ILIM` (input current limit) and R13 = 3.57 kΩ on
  `ISET`, with the annotation `ICHRG=250mA`. This Product Reference uses the
  schematic and bill-of-materials values.

- The J3 part number is `653102124022` in the schematic and `653102131822` in
  the bill of materials.

- GPIO8 carries the net label `D2` at IC1, but no header connection is shown
  for that net. Header position `D2` connects to GPIO0 (`NEOP_DIN`).

- The net connected to header position 10 and QWIIC pin 4 is named
  `A6/GPIO4/TOUCH4`, but it connects to IC1 pin 15 (GPIO6). The ESP32-C5 has no
  touch sensor. This Product Reference uses GPIO6.

- The label `D21` for header position 10 is taken from the board pinout
  artwork; the schematic shows only `A6`.

- The ESP32-C5 does not provide an SD host controller; the microSD socket uses
  SPI mode and `DAT1`/`DAT2` are not connected.

### **9.6 Hardware Errata** {.section-page}

No hardware errata have been recorded for revision V1.2.3.
