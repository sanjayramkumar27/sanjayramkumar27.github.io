---
title: "In-Space CAN-SAT Competition"
year: 2025
role: "Structures lead, Control Systems"
outcome: "Only team to use Quadcopter as Secondary descent mechanism"
summary: "A 1Kg small CANSAT equipped with two descent control methods (Parachute and deployable Quadcopter) capable of withstanding rocket launch requirements and impact loads while relaying live telemetry throughout"
---

**Contribution:** Structures & Mechanisms · Structural Analysis · Control Systems

## Project
A ~960 g, 148×242 mm CanSat built for autonomous, precision recovery: after nosecone ejection and a passive-parachute descent to 500 m, four BLDC rotors deploy on spring hinges (nylon burn-wire release) and take over under closed-loop control, bringing the vehicle down at 1–3 m/s and steering it back toward the launch site using GPS — turning a single-use descent into a reusable, repeatable recovery system.

## Structures & Mechanisms
- Modular 5-piece ABS airframe (SLA-printed),  isogrid main body, avionics mid-section, top/bottom end caps, chosen for build/debug access; SLA selected over FDM after a direct print-quality and strength comparison.
- Iterated isogrid geometry (22 mm triangle, 5 mm fillet, density 1.6) after the original 2 mm-thin arms failed drop testing.
- Designed the foldable rotor-arm + spring-hinge deployment (nylon burn-wire release, hard stop at 90°) and the custom 2S2P 18650 battery holder.
- Relocated the parachute mount from the isogrid top body to a dedicated top end-cap hook after simulation showed the isogrid arms as the dominant stress path — single-hook design confirmed adequate by static FEA.

{{< figure src="cad.png" alt="CAD" caption="CAD Model of CANSAT" >}}

## Structural Simulation & Verification (ANSYS)
| Analysis | Condition | Result |
|---|---|---|
| Shock, axial | 295 m/s², 1 ms impulse | 0.01 MPa max |
| Shock, off-axis | axial + lateral | 3.8 MPa max (ABS UTS: 37.5 MPa) |
| Drop test, explicit dynamics | 3 m/s impact | 23 MPa max, **FoS 1.63** |
| Modal | - | 1st mode 205 Hz |
| Rotor arm, static | 240 Pa dynamic pressure + 5 N motor load | **FoS 2.08** |
| CFD, steady-state | 20 m/s flow | Cd = 0.55 (bare structure) |

## Control Systems
- Designed the cascaded-PID attitude/position control architecture for the quadrotor descent phase: thrust, roll, pitch, yaw loops feeding a motor-mixing algorithm, with diagonal rotor pairs counter-rotating to cancel reaction torque.
- Closed the outer loop on inertial position (not attitude alone) - the CanSat holds a fixed (x, y) relative to the launch site rather than just "upright," avoiding the translate-with-the-wind failure mode of a pure attitude-hold controller.
- Modeled the cascaded-PID quadrotor control loop in Simulink to tune gains and verify closed-loop step/disturbance response ahead of embedded implementation.

{{< figure src="flow.png" alt="Control Flow Diagram" caption="Control FLow Diagram" >}}

## Avionics

The avionics are split across two hand-soldered perfboard PCBs - a power/ESC board and a sensing/compute board - kept physically and electrically separate so each could be debugged or re-wired independently during iteration.

<div class="image-pair">
  {{< figure src="top.jpeg" alt="Top PCB" caption="Top PCB" >}}
  {{< figure src="bot.jpeg" alt="Bottom PCB" caption="Bottom PCB" >}}
</div>

### Flight Computer
A **Teensy 4.1** runs the full control stack, including a custom-written PID controller for attitude stabilization. No off-the-shelf flight-controller firmware (Betaflight, ArduPilot, etc.) was used - sensor fusion, the control loop, and motor mixing are all our own code.

### Sensing & Navigation
- **ICM 20948 IMU** - attitude and angular rate feedback for the PID loop
- **BMP390** barometric pressure sensor - altitude estimate
- **Bharat PI NavIC receiver** - absolute position fix via India's own regional satellite navigation system, rather than a generic GNSS module

All three feed into the Teensy 4.1 for state estimation.

### Power & Motor Interface
Motor control runs through a **4-in-1 ESC**, hand-soldered onto its own perfboard with screw-terminal breakouts for every motor phase and power lead. This made it possible to disconnect motor without resoldering, useful given how often ESC calibration and motor swaps came up during tuning. Main power distribution uses **12 AWG wire** to keep resistive losses down, since the motors draw large instantaneous current under load.

## Physical Integration
Both boards are mounted in the airframe's mid-section, chosen specifically for easy access during debugging which was a deliberate trade against a fully enclosed build, in favor of iteration speed. Propulsion is via 3-inch, 3-blade propellers on each motor.

{{< figure src="test.jpeg" alt="Test" caption="Test setup for PID Tuning" >}}

## Outcome
Final structure met all requirements (960 g vs. 1 kg limit, 148×242 mm vs. 150×400 mm limit) and passed a full-system drop test - structural integrity, sensor telemetry, and the parachute → burn-wire → rotor-deployment sequence all validated end-to-end.

{{< figure src="final.jpeg" alt="Final Model" caption="Final flight ready model" >}}