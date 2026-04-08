# ESC

## Timings

```
Dead time on edges (GD+MOSFET switch): 80ns 
Switching noise duration: 400ns
Blanking time: 480->700ns

Sampling duration: 88ns@170Mz,15cycles
Forced off duration: 788ns
Margin: 688 ->1µs

PWM off duration: 2µs(2x1µs)@24kHz,95.2%duty,centre-aligned -> ok
```

The issue with center aligned PWM is that it not a true double resolution. Indeed, the ZC computation must happen 2 times per period. But for 2 neihgbouring PWM, they share the same, (sometime really short) PWM off period: the 2 off period collapse together. Hence, the resolution decreases with speed as `t_off` becomes shorter. For current control, as it can be both sampled in the `t_on` and `t_off` phase, this is much less of an issue. 

For instance, if `t_off` last 2µs. Let t be the time of the falling edge of PWMi (24kHz, 98.2%duty), t+700 is the time at which we start the ADC conv, it end at t+800. At t+1000, we change for PWMi+1. No blank time is needed, so we can start conversion right away a t+1000 and it will end a t+1100. Hence there is only dt=(t+1000)-(t+700)=300ns of difference. The period at 24kHw of PWN is 42µs and dt is 0.7% of that. Useless.

A better strategy is to sample the ADC of PWMi+1 at t+1700 (finishes at t+1800), wich is symetrical with respect to the underflow event. dt is now 1µs and represent 2% of the PWM period. But still useless.

Hence, at high duty-cycle, true double resolution cannot be reached. Thus we choosed to branch for only 1 ZC reading per period once dt is less than 25% of the PWN: 10.5µs. 

```
LOW PWM DUTY:
2µs(2x1µs)@24kHz,95.2%duty,centre-aligned,one-current-sampling-per-period

MEDIUM PWM DUTY:
2µs(2x1µs)@24kHz,95.2%duty,centre-aligned, doubled control in 

HIGH PWM DUTY:
2µs(2x1µs)@24kHz,95.2%duty,centre-aligned,one-tension-sampling-per-period
```

## Theory

`main()` init the peripherals and then goes while(1) and is empty. This guarantee that if the interrupt triggered on UEV of TIM is shorter than half a preriod (-somepennuts), we are hard real time guaranted.

```
On each UEV triggered by TIM1:
A) PWM(i-1) -> falling edge -> ISR counter update event (UEV) -> trigger ADC reading of tension&current simultaneously after 700ns -> DMA export conversions
B) PWM(i-1)&PWM(i) -> look for ZC with tension reading (hysteresis+windowed) -> if ZC, Compute the speed using [t_zc(i)-t_zc(i-3)]/4 in ticks/30deg (conversion can be done to get s/deg but it slows down the computation) -> start a TIMER that will DMA the pre-computed next pattern in the MOSFETpattern register 30deg later with respect to ZC time -> Update MOSFETpatternnext register using circular DMA. 
C) PWM(i-1)&PWM(i) -> run control loop on current reading -> update PWMduty register if needed
By this time, we should be in PWM(i) period. We can estimate that B&C take 1us. A PWM is 41us. We have consumed 1us of the period, as the A) step happened at PWM(i-1). Hence, we have 20.5-2=19.5us remaining before the next UEV (centre aligned).

On each [UEV%10==0] triggered by TIM1:
D) Compute the speed using [t_zc(i)-t_zc(i-3)]/4 in ticks/30deg (conversion can be done to get s/deg but it slows down the computation) (if not done in B). 
E) Run the control loop for speed -> Update current setpoint register if needed and clamp if it goes beyond max

If DMA captured a DShot order:
F) Decode the latest complete 16-bit DShot frame -> update your speed setpoint
```

However, we also must consider the falt of:
 - The gate drivers
 - The DMA: error interrupts (DMEIF, TEIF)
 - The CPU: HardFault / MemManage / BusFault / UsageFault
 - The TIM: Break interrupt (BRK / BRK2)
 - The ADC: analog watchdog interrupt
 - System: System-level interrupts
 - (optional) Kill switch, vbus voltage, crystal fails
And the other interrupt must be disabled

To go further:
 - DMA double buffer (or check DMA race condition for hard RT)
 - ZC latency (static and dynamic)
 - Variable PWM frequency to ensure that the t-off is kept with minimal impaect on max t-off and optimized frequency (LOW RPM 24kHz, HIGH RPM 128kHz, then reduce to give more room to t-off)

## Mono ISR

Because there is only one ISR, it cannot be interrupted at a bad moment.
This removes:
 - ISR jitter
 - preemption chains
 - priority inversions
 - long worst-case analyses

Works only if following rules are respected:
 1. ISR MUST finish before the next timer event
 2. ISR must never call blocking functions, everything must be O(1)
 3. All other IRQs must be disabled or lower priority
	a. Hard Real-Time Mode → Only one ISR ever triggers
	b. Soft background → TIM1_UP priority = highest

## Loop frequencies

All loops follow the 5-10x rule, to avoid both undersaqmpling (margin from Niquist) and oversampling with no sensible gain. This optimize bandwith.

```
Current loop:
Frequency: 5-10x faster than smallest motor current variation
Purpose: Shape torque/acceleration -> electrical

Speed loop:
Frequency: 5-10x slower than the current loop - must stay 5-10x faster than the smallest motor speed variation
Purpose: Shape the rotating power/speed -> mechanical

Optionnal [
Position loop:
Frequency: 5-10x slower than the speed loop - must stay 5-10x faster than the smallest motor position variation determined by sensor and/or estimators
Purpose: Shape the rotating energy/position -> trajectory
]
```

## Pseudo code

```
HW Trigger
1) ISR TIM1_UP+700ns trigger simulataneous injection conversions of ADC1&ADC2, read of current and active phase to neutral tension. The DMA move it on the memory address of the specified variable. All HW.
2) On complete writing, DMA trigger ISR for CPU.

CPU ZC
3) CPU ISR check if a ZC happened with respect to phase state
	3.1) If ZC happen: the number of ticks needed (speed)  for 30deg is computed (optional: latency is compensated): delay_30deg
    3.2) A one pulse timer is reseted, counter setted for delay_30deg and launched
	3.3) The next pattern is computed
	LATER->3.4) DMA detect the one pulse counter UEV and load on the shadow register of the PWM TIM

When running purely on PWM, we assume that torque=k*I=k*duty, no measurment is done to correct it. It is technically a closed loop but not proprely regulated: we pulse energy on a system that commutate itself via feedback, but nothing more. Thus, we need a current loop. 
On FPV drone, no current loop is added as it reduce bandwith, and the control is given to the FC. However, the human is here to close the loop. Slip can only appears on a BLDC if, BEMF is too weak (low speed), extrem load shock, braking to reversal, duty to high for BEMF measure, phase advance is mistuned at high speed.

CPU Current loop
4) Compute the difference btw the current setpoint and the current sensed. Once done, apply PI controller to get the correction factor. Once done, increase/decrease the current by increase/decrease the PWM.

[Optionnal]
SPEED control
5) Only run when the UEV is a multiple of 5-10x. Same method that for the current loop. However, if a ZC as happen on the same ISR, no need to compute the speed again.

[Different communication protocols]
Read DShot 
6) A timer in input mode is waiting for the 16 bits of DShoot1600, once receive DMA transfer it in a buffer. It is then converted by the CPU and we update the SPEED/Current setpoint based on it.

Voltage (meanPwm, potentimeter)
7) HW trigger of ADC reading just after the previous one in 1), then DMA does it jobs and we update the SPEED/Current setpoint based on it.
```

## Faults

FAULTS
 - The CPU: HardFault / MemManage / BusFault / UsageFault
On the CPU, faults are diagnostic events followed by a controlled reset. Not something that can be managed.
Step1: HW level stop of everything
Step2: Minilog on ram memory that survives reset
Step3: Trigger a system reset via NVIC_SystemReset()
Step4: At reboot, look for fault in ram memory that survives reset, then do a safe boot mode, disable everything until fault clear by exterior and report crash telemetry.

However, we also must consider the falt of:
 - The gate drivers
 - The DMA: error interrupts (DMEIF, TEIF)
 - The CPU: HardFault / MemManage / BusFault / UsageFault
 - The TIM: Break interrupt (BRK / BRK2)
 - The ADC: analog watchdog interrupt
 - System: System-level interrupts
 - (optional) Kill switch, vbus voltage, crystal fails
And the other interrupt must be disabled

## V2

A adc reading is at least 15 cycles. We have to measure 2 current (third can be guessed) + 