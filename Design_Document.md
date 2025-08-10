# Hardware Design Document: BatteryCharger_FuelGauge_Breakout_Board

## 1. Overview

This breakout board provides battery charging and fuel gauging for single-cell Li-Ion batteries. It integrates the Texas Instruments BQ24040 battery charger and the BQ27621-G1 fuel gauge IC. The design supports USB-C input and configurable logic levels for host communication.

## 2. Major Components

### 2.1 Battery Charger IC (TI BQ24040)
- **Package:** WSON-10-1EP (2x2mm)
- **Functions:** Li-Ion single-cell battery charging, integrated power path management.
- **Key Pins:**
    - IN: Power input (VBUS from USB-C)
    - OUT: Charger output to battery and system
    - ISET/ISET2: Charge and input current setting (resistor configurable)
    - TS: Battery temperature sensing
    - PRETERM: Precharge current setting
    - ~CHG, ~PG: Charge and power-good status outputs
- **Thermal Pad (EPAD):** Must be connected to ground.

### 2.2 Fuel Gauge IC (TI BQ27621-G1)
- **Package:** DSBGA-9 (1.62x1.58mm)
- **Functions:** Battery fuel gauging via Impedance Track™.
- **Key Pins:**
    - BAT: Battery voltage sense
    - VDD: Power supply (1.8V internal, or external VDD)
    - SCL/SDA: I2C clock/data for host communication
    - GPOUT: Programmable output (status/interrupt)
    - BIN: Optional battery thermistor input (configurable via jumper)
    - NC: No connect

### 2.3 USB-C Input Connector
- **Type:** USB_C_Receptacle_USB2.0_16P
- **Pins:** VBUS, GND, CC1/CC2, SBU1/SBU2, D+/D- for USB 2.0 only.

### 2.4 Connectors
- **J1:** 5-pin host interface (VDDE, GPOUT, SCL, SDA, GND)
- **J2:** 3-pin battery connector (+BATT, BIN, GND)
- **J4:** 3-pin auxiliary connector

### 2.5 Passive Components
- **Resistors:** Current setting, pull-ups, configuration (e.g., 10k, 5.1k, 1.8M, 2k, 1k)
- **Capacitors:** Decoupling and filtering (e.g., 0.1uF, 1uF, 0.47uF)
- **TVS Diode:** USB port protection (SMBJ7.5CA)

### 2.6 Jumpers
- **JP1 (BIN select):** Selects battery temperature sensing mode (default disables BIN, option for thermistor).
- **JP2 (VDD select):** Selects power source for fuel gauge I/O (default external 3.3V, option for internal 1.8V).

## 3. Configuration Options

| Jumper | Default | Alternate | Description |
|--------|---------|-----------|-------------|
| JP1(1-2) | Pulled down through 10k; BIN disabled | JP1(2-3): Pulled up through 1.8M; battery pack must have thermistor | BIN configuration |
| JP2(1-2) | Connect external 3.3V on VDDE pin | JP2(2-3): Use 1.8V regulator output | VDD configuration for I2C interface |

## 4. Interfaces

### 4.1 Host Interface (J1)
| Pin | Signal | Description |
|-----|--------|-------------|
| 1 | VDDE | Connect 3.3V (logic level) |
| 2 | GPOUT | Programmable output |
| 3 | SCL | I2C clock |
| 4 | SDA | I2C data |
| 5 | GND | Ground |

### 4.2 Battery Connector (J2)
| Pin | Signal | Description |
|-----|--------|-------------|
| 1 | +BATT | Battery positive terminal |
| 2 | BIN | Battery NTC/thermistor |
| 3 | GND | Battery ground |

## 5. Power Domains

- **VBUS (USB):** Main input power for charging.
- **VDDE:** External I/O voltage (3.3V or 1.8V via JP2).
- **VDD:** Fuel gauge IC supply, from internal regulator or external VDDE.
- **GND:** System ground.

## 6. Notes & Recommendations

- By default, the BIN pin function is disabled. If your battery pack has an internal NTC, set JP1 to 2-3.
- By default, use external VDD of 3.3V on J1 pin 1. If your interface logic is 1.8V, set JP2 to 2-3 to use the fuel gauge's internal 1.8V regulator.
- TVS diode protects USB data lines.
- Mounting holes and fiducials are provided for PCB assembly.

## 7. Reference Designators

- **U1:** Fuel Gauge (BQ27621-G1)
- **U2:** Charger (BQ24040)
- **J1, J2, J3, J4:** Connectors
- **JP1, JP2:** Configuration jumpers
- **R1-R14, C1-C5, D1:** Passive components (refer to schematic for values and placement)
- **H1-H4:** Mounting holes
- **FID1-FID3:** Fiducials

## 8. Manufacturer & Part Numbers

| Ref | Component Type | MPN | Manufacturer | Value | Package/Footprint |
|-----|---------------|-----|--------------|-------|-------------------|
| U1  | Fuel Gauge IC | BQ27621YZFR-G1A | Texas Instruments | - | DSBGA-9 (1.62x1.58mm) |
| U2  | Battery Charger IC | BQ24040DSQT | Texas Instruments | - | WSON-10-1EP (2x2mm) |
| J1, J2, J4 | Pin Header Connector | 61300511121 (J1), G800W305018EU (J2/J4) | Würth Elektronik (J1), Amphenol ICC (J2/J4) | 1x5/1x3 | PinHeader_2.54mm_Vertical |
| J3 | USB-C Connector | USB4105-xx-A | GCT | USB 2.0, 16P | USB_C_Receptacle_GCT_USB4105-xx-A_16P_TopMnt_Horizontal |
| R1, R5, R8, R9 | Resistor | RMCF0603FT10K0 | Stackpole Electronics Inc | 10kΩ | R_0603_1608Metric |
| R2, R3, R13, R14 | Resistor | RMCF0603FT5K10 | YAGEO (R13/R14), Stackpole Electronics Inc | 5.1kΩ | R_0603_1608Metric |
| R4 | Resistor | RC0603FR-071M8L | YAGEO | 1.8MΩ | R_0603_1608Metric |
| R6 | Resistor | ERA-3AEB202V | Panasonic Electronic Components | 2kΩ | R_0603_1608Metric |
| R7 | Resistor | RMCF0603FT1K00 | Stackpole Electronics Inc | 1kΩ | R_0603_1608Metric |
| C1 | Capacitor | UMK107B7474KA-TR | Taiyo Yuden | 0.47µF | C_0603_1608Metric |
| C2, C4, C5 | Capacitor | CL10B105KO8NNNC | Samsung Electro-Mechanics | 1µF | C_0603_1608Metric |
| C3 | Capacitor | CC0603KRX7R7BB104 | YAGEO | 0.1µF | C_0603_1608Metric |
| D1 | TVS Diode | SMBJ7.5CA | Littelfuse Inc. | TVS | D_SMB_Handsoldering |

## 9. Schematic Source

File: `BatteryCharger_FuelGauge_Breakout_Board.kicad_sch`  
Version: v1  
Date: 2025-08-03

---

For further details, refer to the schematic and associated datasheets linked in the file.
