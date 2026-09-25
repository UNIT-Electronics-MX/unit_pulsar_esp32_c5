## **6 Mechanical Information**

Hardware revision V1.2.3 uses a narrow development-board form factor with two
parallel 15-position edge rows following the Arduino Nano template, USB-C at
one end, and QWIIC, J20, and RF connectors for expansion.

### **6.1 Component-side Envelope** {.section-page}

Board photographs for revision V1.2.3 are not yet available.

Adequate vertical and lateral clearance should be provided around the USB-C
connector, pushbuttons, QWIIC and J20 connectors, RF connector, and other
components. The edge pads must remain accessible when the board is mounted on
headers, carrier boards, sockets, or directly soldered to another PCB.

### **6.2 Bottom-side Envelope** {.section-page}

Adequate clearance must be maintained for battery connection, microSD card
insertion and removal, and antenna cable routing. Enclosures and carrier
boards must not cover the pressure, humidity, and light-sensor openings when
those measurements are used.

### **6.3 Board Dimensions** {.section-page}

A controlled dimension drawing and mounting-hole coordinates for revision
V1.2.3 are not yet available. Do not derive dimensions from rendered board
images.

### **6.4 Mechanical Integration Considerations** {.section-page}

When integrating the UNIT PULSAR ESP32-C5 into a carrier board, fixture, or
enclosure, provide sufficient clearance for:

- USB-C cable insertion and removal.
- QWIIC and J20 cable connection.
- Antenna cable and connector on J2.
- Battery connector and cable routing.
- microSD card insertion and removal.
- BOOT and RESET button access.
- Edge-pad soldering or header installation.
- Configuration solder-jumper access when required.
- Airflow and light paths to the environmental and optical sensors.

Keep metal enclosures, ground planes, and batteries away from the antenna.
PCB thickness, maximum component heights, and connector insertion envelopes
should be verified from the corresponding component specifications when they
are critical to the final enclosure or carrier design.
