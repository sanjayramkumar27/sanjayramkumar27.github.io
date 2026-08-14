---
title: "In-Space CAN-SAT Competition"
year: 2025
role: "Structures lead, Control Systems"
outcome: "Only team to use Quadcopter as Secondary descent mechanism"
summary: "A 1Kg small CANSAT equipped with two descent control methods (Parachute and deployable Quadcopter) capable of withstanding rocket launch requirements and impact loads while relaying live telemetry throughout"
cover:
  image: "final.jpeg"
---

**Role:** Structures Lead and Control Systems

<a href="https://github.com/sanjayramkumar27/CanSat_Final_FSW" class="gh-link">
  <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 16 16" fill="currentColor">
    <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38
    0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13
    -.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66
    .07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15
    -.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0
    1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82
    1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01
    1.93-.01 2.2 0 .21.15.46.55.38A8.01 8.01 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
  </svg>
  GitHub Repository
</a>
 
## Overview
 
A 960 g CanSat (148 × 242 mm) built for autonomous, precision recovery rather than a single passive descent. After nosecone ejection, the vehicle falls under parachute to 500 m, then four BLDC rotors deploy on spring hinges (nylon burn-wire release) and take over under closed-loop control, bringing it down at 1 to 3 m/s while steering back toward the launch site on GPS. What starts as a single-use descent ends as a repeatable, reusable recovery.
 
## Structures and mechanisms
 
The airframe is a modular five-piece ABS structure (SLA-printed): isogrid main body, avionics mid-section, and top and bottom end caps, split up for build and debug access. SLA was chosen over FDM after a direct comparison of print quality and strength. The isogrid geometry went through its own iteration: the original 2 mm-thin arms failed drop testing, so the design moved to a 22 mm triangle with a 5 mm fillet at density 1.6.
 
The parachute mount moved from the isogrid top body to a dedicated top end-cap hook once simulation showed the isogrid arms were carrying most of the load; static FEA confirmed a single hook was enough on its own. The foldable rotor arms and spring-hinge deployment (nylon burn-wire release, hard stop at 90°) and the custom 2S2P 18650 battery holder were both designed in-house.

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
 
## Control systems
 
The quadrotor descent phase runs a cascaded-PID architecture, thrust, roll, pitch, and yaw loops feeding a motor-mixing algorithm, with diagonal rotor pairs counter-rotating to cancel reaction torque. The outer loop closes on inertial position rather than attitude alone, so the CanSat holds a fixed (x, y) relative to the launch site instead of just staying upright. That distinction matters: a pure attitude-hold controller drifts with the wind, and closing the loop on position avoids that failure mode. Gains were tuned and step and disturbance response checked in a Simulink model of the same cascaded-PID loop before touching the embedded implementation.

{{< figure src="flow.png" alt="Control Flow Diagram" caption="Control FLow Diagram" >}}
 
## Avionics
 
The avionics split across two hand-soldered perfboard PCBs, a power/ESC board and a sensing/compute board, kept physically and electrically separate so either could be debugged or rewired independently during iteration.

 
<div class="image-pair">
  {{< figure src="top.jpeg" alt="Top PCB" caption="Top PCB" >}}
  {{< figure src="bot.jpeg" alt="Bottom PCB" caption="Bottom PCB" >}}
</div>

### Flight Computer
A Teensy 4.1 runs the full control stack, including a custom-written PID controller for attitude stabilization. No off-the-shelf flight-controller firmware such as Betaflight or ArduPilot is used; sensor fusion, the control loop, and motor mixing are all original code.
 
### Sensing & Navigation
Three sensors feed the Teensy for state estimation: an ICM 20948 IMU for attitude and angular rate, a BMP390 for altitude, and a Bharat PI NavIC receiver for an absolute position fix through India's own regional satellite navigation system rather than a generic GNSS module.
 
### Power & Motor Interface
 Motor control runs through a 4-in-1 ESC on its own perfboard with screw-terminal breakouts for every motor phase and power lead, so a motor can be disconnected without resoldering, useful given how often ESC calibration and motor swaps came up during tuning. Main power distribution uses 12 AWG wire to keep resistive losses down under the large instantaneous current the motors draw.
 
Both boards sit in the airframe's mid-section for easy access during debugging, trading a fully enclosed build for faster iteration. Propulsion is three-inch, three-blade propellers on each motor.
 
{{< figure src="test.jpeg" alt="Test" caption="Test setup for PID Tuning" >}}
 
## Outcome
The finished structure met both mass and size limits (960 g against a 1 kg cap, 148 × 242 mm against a 150 × 400 mm cap) and passed a full-system drop test, with structural integrity, sensor telemetry, and the parachute-to-burn-wire-to-rotor-deployment sequence all holding up end to end.
 
{{< figure src="final.jpeg" alt="Final Model" caption="Final flight ready model" >}}

<div style="position: relative; width: 100%; max-width: 360px; margin: 0 auto; aspect-ratio: 9 / 16;">
  <iframe 
    src="https://youtube.com/embed/yCvTaJbXSIU" 
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
    allowfullscreen>
  </iframe>
</div>
