# ⚡ Kururugi ESC — Technical Specifications

> Detailed hardware architecture and performance targets



## 🧭 Overview

Kururugi is a compact, high-performance ESC designed for drones and differential-drive robotics. The design focuses on high switching frequency operation, low parasitics, and precise sensing.

This version is sensorless, which limits low-speed performance. Detailed sizing is available in:
```
docs/Kururugi-ESC-sizing.xlsx
```

The design is inspired by the B-G431B-ESC1.



## 📟 Board Presentation

![Front](exports/imgs/Kururugi-V1.0-FrontLegend.png)
![Back](exports/imgs/Kururugi-V1.0-BackLegend.png)



## 🧱 PCB Stackup (6 Layers)

The stackup is optimized for current density, thermal performance, and EMI control.

<table>
<tr>
<td>

**Layer 1 - Power (MOSFET, BUCK)**  
<img src="exports/imgs/stackup/Layer1.png" width="250"/>

</td>
<td>

**Layer 2 - GND plane**  
<img src="exports/imgs/stackup/Layer2.png" width="250"/>

</td>
</tr>

<tr>
<td>

**Layer 3 - VBAT plane**  
<img src="exports/imgs/stackup/Layer3.png" width="250"/>

</td>
<td>

**Layer 4 - Signal routing**  
<img src="exports/imgs/stackup/Layer4.png" width="250"/>

</td>
</tr>

<tr>
<td>

**Layer 5 - Clean GND reference**  
<img src="exports/imgs/stackup/Layer5.png" width="250"/>

</td>
<td>

**Layer 6 - Control (MCU, shunts, LDO)**  
<img src="exports/imgs/stackup/Layer6.png" width="250"/>

</td>
</tr>
</table>
Outer layers use 2 oz copper, and vias are filled and capped for reliable via-in-pad and thermal conduction.

## Subsystemps

### 🔋 Power System

The ESC operates from 4S–6S LiPo input. VBAT feeds the power stage directly, while a BUCK converter generates ~11.5 V for the gate driver and an LDO provides 3.3 V for logic.

An optional external 3.3 V input allows independent MCU powering. External capacitors are recommended to reduce ringing from wiring inductance.



### ⚡ Power Stage

The design uses a three-phase half-bridge directly supplied from VBAT. Layout minimizes switching loop area and parasitic inductance while ensuring good thermal spreading.



### 🚪 Gate Driving

A high-current gate driver (~3 A / ~5 A) enables fast switching. Asymmetric gate resistors allow tuning of switching behavior and EMI.

Bootstrap capacitor sizing is critical and defined in the sizing spreadsheet.



### 🧠 MCU & Sensing

The system is built around the STM32G484 with advanced timers, ADCs, and internal op-amps.

Phase current is measured using 0.5 mΩ shunts with Kelvin routing and internal amplification. VBAT and optional phase voltages are measured through resistor dividers.

ADC sampling is optimized for speed (~2.5 cycles), with careful impedance control. A third current channel is optional due to ADC limitations.



### 🌡️ Monitoring

An NTC thermistor provides board temperature monitoring.



### 📡 Communication

A UART interface enables telemetry and future real-time monitoring tools. USB was not retained due to layout constraints.


