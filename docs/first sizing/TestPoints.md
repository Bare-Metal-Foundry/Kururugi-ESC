# ESC Testpoints 

### Power / Safety (bring-up)
- **VBAT+ / PGND (at input connector)**  
  Battery sag, input ripple, connector issues.
- **VBUS (DC link) / PGND (at bulk caps)**  
  True bus ripple, spikes, cap/ESL validation.
- **VDRV / GND_DRV (at gate driver)**  
  Driver UVLO, brownouts under switching.
- **nFAULT / GND**  
  Immediate shutdown root cause (OC/OT/UV).

### Switching / Control
- **PH_U, PH_V, PH_W (switch nodes)**  
  Commutation, deadtime, ringing, bootstrap health.
- **GH_U/V/W, GL_U/V/W (logic-side if possible)**  
  PWM correctness, polarity, deadtime verification.

### Sensing / ADC integrity
- **I_SENSE (or I_U/V/W) / AGND**  
  Amplifier bandwidth, PWM noise, ADC timing.
- **VBUS_DIV / AGND**  
  Scaling, RC settling, protection accuracy.
- **VPH_U/V/W_DIV / AGND (if sensorless)**  
  BEMF, sector detection, divider settling.
- **VREF+ / AGND**  
  ADC noise/drift sanity check.

### Debug / Timing
- **SWDIO, SWCLK, NRST, 3V3, GND**  
  Programming, reset/brownout diagnosis.
- **3V3 / GND (at MCU)**  
  Regulator stability during di/dt.
- **TIMING_GPIO / GND**  
  PWM↔ADC alignment proof on scope.
- **CANH/CANL or UART TX/RX**  
  Telemetry integrity, bus noise.

### Placement rules
- Pair every TP with its local ground.  
- Put phase-node pads HV-probe friendly.  
- Analog TPs near ADC/op-amp nodes.
