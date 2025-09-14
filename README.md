# ESP‑12 Pressure Control Circuit

A modular ESP‑12E (ESP8266) hardware design for pressure sensing, load driving, and safe power management with battery charging and automatic source switchover.

### Overview

This repository contains KiCad 9.0.2 schematics for a pressure‑control system built around an ESP‑12E module, dual precision pressure measurement channels, high‑side battery/adapter power path control, and isolated MOSFET drivers for solenoids and a motor.
The design includes an onboard CN3703 Li‑ion charger, AMS1117‑3.3/LM7805 regulators, TLP250 gate drivers, and IRF540N power MOSFETs with Schottky flyback protection, organized across four hierarchical sheets.

### Schematic set

- ESP‑12 top‑level: ESP‑12E module, boot/EN/RST conditioning, ESD protection, sensor ADCs, and header breakout.
- MOSFETs: Four opto‑isolated TLP250 driver channels feeding IRF540N MOSFETs for Sol_1…Sol_4 and Motor outputs with MBR745 freewheel diodes and LED indicators.
- Automatic Load Switchover: LTC2952 power management, dual 2SJ174 P‑MOSFET path control, and local 3.3 V/5 V regulation for state control and status.
- Battery Charger: CN3703 Li‑ion charger with SS36 rectifiers, 22 µH power inductors, 0.1 Ω sense, and charge/done LED indicators.


### Files and structure

- ESP12E.kicad_sch: Top‑level ESP‑12 sheet with sensors, regulators, ESD, UART, and I/O header.
- MOSFETs.kicad_sch: Isolated drivers, MOSFET power stages, connectors for solenoids and motor.
- Automatic Load Switchover.kicad_sch: LTC2952 control, P‑channel power multiplexing, timing/configuration network.
- Battery Charger.kicad_sch: CN3703 charger stage with power path elements and indicators.


### Power architecture

- Input power is provided via a 12 V PWR connector with bulk filtering (e.g., 470 µF/16 V C3) feeding a 5 V LM7805 and a 3.3 V AMS1117 rail, duplicated where needed on control sheets.
- Automatic source switchover uses LTC2952 to supervise ON/OFF timing, power‑fail, watchdog, and gate‑drive outputs for back‑to‑back 2SJ174 P‑MOSFETs controlling PWR_in/PWR_out and Battery_in.
- The CN3703 charger implements Li‑ion charging with SS36 diodes, 22 µH inductors, 0.1 Ω current sense, and CHRG/DONE LED status, feeding the battery power domain.


### Sensing and ADC

- Two HX710(A/B) precision ADC channels interface differential pressure sensors labeled MPS‑2108, each with Vo+/Vo−, VCC, and local decoupling.
- Each ADC section provides AVDD/DVDD partitioning, PD_SCK/DOUT digital lines to the ESP‑12E, and VREF/AGND nodes for stable measurement.


### Microcontroller core

- ESP‑12E (ESP8266) module exposes GPIO0–GPIO16, UART TX/RX, SPI, ADC (A0), EN, and RST, with pull networks for proper boot mode and reset behavior.
- ESD protection diodes (CESD5V0D3) are placed on sensitive I/O and rails including UART, reset, and 3.3 V nodes to improve robustness.


### Isolated drivers and loads

- Four TLP250 opto‑gate drivers provide isolation between the ESP‑12 logic domain and the IRF540N power stages, each with dedicated gate resistors and LED activity indicators.
- Outputs are presented on headers as Sol_1, Sol_2, Sol_3, Sol_4, and Motor, each clamped by MBR745 Schottky diodes for inductive flyback protection.


### Connectors

- J4 12v PWR: main DC input for the regulator stages and system rails.
- J6–J10: load connectors labeled Sol_1…Sol_4 and Motor for external actuators.
- J3 UART: UART header with GND and supply pins for programming and debug.
- J1/J2 headers: general I/O and power breakouts, including +3.3 V, +5 V, and ground.


### Pin breakout (IO1 1×16)

| Pin/Net | Function |
| :-- | :-- |
| +3.3V | 3.3 V rail for peripherals  |
| GND | Ground reference  |
| D0/GPIO16 | Wake/deep‑sleep capable digital I/O  |
| D1/SCL/GPIO5 | I2C SCL or GPIO  |
| D2/SDA/GPIO4 | I2C SDA or GPIO  |
| D3/GPIO0 | Boot strap/GPIO, used for programming mode  |
| D4/GPIO2 | Boot strap/GPIO  |
| D5/GPIO14/SCLK | SPI SCLK or GPIO  |
| D6/GPIO12/MISO | SPI MISO or GPIO  |
| D7/GPIO13/MOSI | SPI MOSI or GPIO  |
| D8/GPIO15/SS | Boot strap/SPI SS  |
| TX/GPIO1 | UART TXD  |
| RX/GPIO3 | UART RXD  |
| A0/ADC | Analog input to ESP8266  |

### Boot and programming

- EN and RST are broken out with push‑buttons and pull resistors for stable power‑up and manual reset.
- Boot straps on GPIO0/GPIO2/GPIO15 are provisioned via resistor networks to ensure normal boot, with access for entering programming mode via GPIO0.


### Protections and indicators

- Transient suppression uses CESD5V0D3 devices on supply and data lines close to connectors and the ESP‑12E pins.
- Multiple LEDs indicate power presence, driver activity, and charger state (CHRG/DONE), aiding bring‑up and troubleshooting.


### Build notes

- Designed with KiCad E.D.A. 9.0.2; open the top‑level ESP12E.kicad_sch and traverse hierarchical sheets for editing and ERC.
- Validate regulator thermal performance and inductor/diode current ratings for intended solenoid and motor loads before committing to PCB or enclosure.


### Key components

- MCU and IO: ESP‑12E module with 1×16 IO header and a 1×6 programming/UART header.
- Sensing: 2× HX710(A/B) ADCs with MPS‑2108 pressure sensors and precision analog routing.
- Power: LM7805_TO220, AMS1117‑3.3, bulk/tantalum/ceramic decoupling, and LTC2952‑controlled high‑side switching with 2SJ174 P‑MOSFETs.
- Charger: CN3703 with SS36, 22 µH inductors, 0.1 Ω sense, and charge/done LEDs.
- Drivers: TLP250 opto drivers and IRF540N MOSFETs with MBR745 clamps and LED indicators.


### Getting started

- Power the board from the 12 V input or the battery domain once assembled and verified on bench with dummy loads.
- Program the ESP‑12E via the UART header using normal ESP8266 flashing procedure after pulling GPIO0 low for programming mode.


### Safety

- Observe proper isolation, heat sinking, and wiring gauge for motor/solenoid currents, and verify flyback diodes are oriented correctly before first power‑up.
- Double‑check charger and battery polarity, and do not operate unattended during initial charge‑cycle validation.


### Attribution

Metanoia — designed by Mohamed Yousry, Mechatronics/Embedded Hardware Engineer.

### License

Add a LICENSE file of choice in the repository root to specify terms for schematics and any derived PCB/firmware.

### Version

Schematic Rev v1.0, dated 2025‑09‑07.

### What’s included

- Complete multi‑sheet KiCad schematics with hierarchical organization for core MCU, drivers, switchover, and charging.
- Net labels and headers to speed firmware pin mapping for UART, SPI, I2C, and ADC integration on the ESP‑12E.


### Why this design

The project combines reliable power‑path control, isolated high‑current drivers, and precision sensing into a compact ESP8266 platform for pressure‑based automation tasks.

Use this README.md as the repository landing page; copy it to README.md in the project root and adjust badges, images, or PCB references once layout is added.

<div style="text-align: center">⁂</div>

