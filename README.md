# Kururugi - Electronic Speed Controller (ESC)

Kururugi is a high-performance ESC designed for 6-inch FPV drones, developed as a platform for low-level motor control, power electronics optimization, and embedded system experimentation.

This project is developed alongside the Shirley Flight Controller, forming a complete hardware stack for autonomous drone development under the Bare Metal Foundry initiative.

## Features

### Core Hardware
- **MCU**: STM32G4 series (advanced timers, ADCs, op-amps)
- **Gate Driver**: Infineon 2EDL8024
- **Power Stage**: 3-phase MOSFET bridge (low RDS(on), 60V class)
- **Sensing**:
  - Phase current (shunt + amplification)
  - Bus/phase voltage (resistive divider + filtering)
  - Temperature (NTC thermistor)

### Power System
- **Input**: 4S–6S LiPo (13–25.2V nominal)
- **Transient tolerance**: up to 45V (VBAT)
- **Architecture**:
  - Buck converter → 10V rail
  - LDO → 3.3V logic/analog rail

### Performance Targets
- **PWM frequency**: up to 64 kHz (V1.0), 96 kHz (V2 target)
- **Phase current**:
  - 12.5–15 A continuous
  - 20–25 A few seconds peak
  - up to 50–60 A transient
- **Duty cycle**: up to 90–95%

### Connectivity
- **Control input**: PWM / DShot (timer capture)
- **Debug**: UART + JTAG (SWD)
- **Power input**: direct VBAT

## Design Philosophy

This project prioritizes:
- **Full control**: from hardware to low-level firmware
- **Performance-driven design**: high switching frequency and tight timing
- **Compact integration**: optimized for small drone frames
- **Engineering rigor**: sizing-driven design (losses, thermal, switching)

## Repository Structure
```bash
├── hardware/ # KiCad schematics and PCB
├── firmware/ # ESC firmware (low-level control)
├── docs/ # Design report and calculations (LaTeX)
├── resources/ # Datasheets and references
```


## Project Scope

This ESC is part of a broader effort to develop fast, fully autonomous drones with full stack ownership. It serves both as a production-oriented design and as a learning platform for:

- Power electronics design
- High-frequency switching systems
- Embedded real-time control
- Hardware/software co-design

## Related Projects

- **Shirley Flight Controller**  
  Companion flight controller platform for system integration and control development.

## Status

- V1.0: Initial validated design (64 kHz target)
- V2: Higher performance iteration (96 kHz+, improved efficiency and thermal behavior)