# KuppiSmart Energy Monitor (REV 0.1)

An ESP32-based hardware platform for collecting electrical energy measurements from RS-485 Modbus energy meters and enabling IoT monitoring.

Designed in Altium Designer by Praveen Gowthaman for KuppiSmart / Livestockify.

**Status:** REV 0.1 prototype

## PCB

| Top | Bottom |
|---|---|
| ![Top](images/pcb_top.png) | ![Bottom](images/pcb_bottom.png) |

2-layer board, 90.4 x 50.0 mm, black solder mask with gold pads. Renders generated from the Gerbers.

## Overview

A custom PCB for an IoT energy monitoring system. The board integrates an ESP32 module, power regulation, RS-485 communication, input protection and diagnostic indicators in one compact platform.

The goal is a maintainable, reproducible hardware design that can be fabricated, assembled, tested and iterated on through later revisions.

## Features

- **ESP32 controller:** ESP32-WROOM-32E-N4 module for processing, Wi-Fi connectivity and system control.
- **RS-485 communication:** ST3485 transceiver with four 4-pin ports (+12 V, B, A, GND) for compatible Modbus RTU meters.
- **Power management:** 12 V DC input, TPS54227 buck converter to 5 V, TLV75733 LDO to 3.3 V for the ESP32.
- **Input protection:** resettable fuse, bidirectional TVS diode and a P-MOSFET reverse-polarity stage.
- **Diagnostic indicators:** PWR, WiFi, COM and ERR LEDs.
- **Programming and debug:** 6-pin UART header (GND, TX, RX, RTS, DTR, +3V3) with EN, RST and WiFi RST buttons.
- **Expansion:** I2C header (GND, +5 V, GPIO 21, GPIO 22).
- **Manufacturing outputs:** schematic, layout, Gerbers and drill files.

## Hardware Architecture

```
        12 V DC Input
              |
      Input Protection
   (fuse, TVS, reverse polarity)
              |
   TPS54227 DC-DC buck converter
              |
            +5 V
              |
     TLV75733 3.3 V LDO
              |
     ESP32-WROOM-32E module
        /      |       \
   UART/PROG  I2C    ST3485 RS-485
                          |
                 Modbus energy meters
```

Conceptual architecture. Confirm the final board against the released schematic.

## Key Design Decisions

### 1. ESP32 as the controller
The ESP32 is a flexible embedded platform for acquiring meter data, managing communications and supporting future IoT features. The WROOM-32E module keeps the RF design off the board.

### 2. RS-485 for meter communication
RS-485 suits compatible Modbus RTU meters and is common in industrial monitoring, where devices can be separated by long cables. The ST3485 transceiver is paired with TVS protection on the bus (SM712-02HTG) to improve robustness of the field interface.

### 3. Separate 5 V and 3.3 V power stages
A buck converter generates 5 V from the 12 V input, and a 3.3 V LDO then powers the ESP32 and compatible logic. This separates the input conversion stage from the microcontroller supply rail, so switching noise from the buck stage does not sit directly on the 3.3 V rail.

### 4. Input protection
The input has a resettable fuse, a bidirectional TVS (SMBJ15CA) and a P-MOSFET (AO3407) reverse-polarity stage with a zener gate clamp. Effectiveness depends on component ratings, layout and the actual input conditions, and the board is marked **12 V DC ONLY**.

### 5. Diagnostic LEDs
Dedicated indicators make debugging possible without relying only on serial logs:

- **PWR:** supply present
- **WiFi:** wireless connection status
- **COM:** meter communication activity
- **ERR:** communication or system fault

Final behaviour depends on the firmware and is confirmed during bring-up.

### 6. Prototype-first development
REV 0.1 is meant to validate the schematic, layout, footprints, power rails and communication interface before further optimisation or production. Changes identified during testing are documented in later revisions.

## Firmware and Data Acquisition

The intended system uses Modbus RTU over RS-485 to read electrical measurements from a compatible energy meter. The workflow can include:

- Voltage and current
- Active power and power factor
- Supply frequency
- Accumulated energy
- Periodic data transmission to an IoT backend

Available measurements depend on the connected meter, its register map and the firmware implementation.

## Files

- [`Energy_Monitoring_Schematic_REV0.1.pdf`](Energy_Monitoring_Schematic_REV0.1.pdf) - schematic
- [`Energy_Monitor_PCB_Layout.pdf`](Energy_Monitor_PCB_Layout.pdf) - PCB layout (PDF)
- [`Gerbers/Energy_Monitoring_Gerber_2026-10-20.zip`](Gerbers/Energy_Monitoring_Gerber_2026-10-20.zip) - Gerber files
