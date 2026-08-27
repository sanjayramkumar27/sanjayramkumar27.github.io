---
title: "In-Space CAN-SAT Competition"
year: 2025
role: "Structures lead, Control Systems"
outcome: "Only team to use Quadcopter as Secondary descent control system"
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
 
## Problem Statement
 
Build a Can-Sat that is equipped with two descent control systems, the first being a parachute which brings the CanSat to 20 m/s terminal velocity. At 500 m altitude, a secondary descent control system must activate/deploy and reduce the Can-Sat's velocity to 1-3m/s at touchdown. The Can-Sat will be launced to an altitude of 1Km by a rocket provided by the organisers. During the entire descent, the Can-Sat must relay live telemetry data like - velocity, altitude, temperature, orientation etc. The Can-Sat must be capable of withstanding launch loads and impact loads while protecting the avionics inside.
 
## System Overview

- Structure
  - Modular 5 piece ABS structure manufactured by SLA. 
  - Isogrid Main Body
  - Mid Avionics Bay
  - Spring hinges for dployable rotors

- Avionics
  - Flight Computer - Teensy 4.1
  - IMU, Pressure and temperature sensor, GPS, XBEE
  - 4-in-1 ESC for BLDC motor control

- Control Systems
  - Custom cascaded PID structure for Roll, Pitch, Yaw, position and Thrust control

- Ground Station
  - Custom built GUI using PyQT


{{< figure src="cad.png" alt="CAD" caption="CAD Model of CANSAT" >}}
 
## My Contribution

### Structural Design
- The biggest hurdle faced was keeping the mass of the entire Can-Sat under 1 Kg while housing all the avionics and make deployable rotors and include a battery that is sufficient to sustain the secondary descent control system and at the same time making the Can-Sat easy for debugging and access and make sure it is designed for manufacturing. 
- I chose a modular 5 piece design for easy access and debugging, with Isogrid main body to reduce weight. The avionics bay was made the middle of the Can-Sat. I used Spring hinges and Nylon burn wire mechanism for deploying the rotor arms. 
- I kept the battery at the bottom of the Can-Sat to ensure the Cg is as low as possible for stability.

### Control Systems
- Built a simulink model to simulate the secondary descent control mechanism.
- Imported the CAD model from SOLIDWORKS to Simulink and built a cascaded PID control.
- Performed thrust characterization of physical motors using a test rig.

### Testing
- Designed a 3D-printed a thust measurement and motor thrust chracterization rig with load cell and amplifier.
- Set up a test stand to test single axis and multi axis contorl test of CansSat.
- Tested the Burn wire mechanism using a bench supply to confirm current requirements and timing.
- Performed a drop test from 20m height to ensure impact handling and reliable telemetry relay.

{{< figure src="test.jpeg" alt="Test" caption="Test setup for PID Tuning" >}}
 
## Outcome
The finished structure met both mass and size limits (1 Kg weight, 148 × 242 mm against a 150 × 400 mm cap) and passed a full-system drop test, with structural integrity, sensor telemetry, and the parachute-to-burn-wire-to-rotor-deployment sequence all holding up end to end. We were the only team to use a quadcopter as the secondary descent control mechanism.

## Design Descisions
- Decided against a monolithic structure and chose a 5 piece modular structure.
- Used connectors rather than soldering the Motors to the Perf-board PCBs.
- Used ready-made spring hinges for reilable rotor deployment.
- Optimized the placement of electronic components on perf-board to reduce the PCB to PCB wires and reduce influence of magnetic radiation from motor wires.
- Used foldable propellers to ensure the CanSat fits inside the rocket and does not interfere during deploymet from the rocket.
- Isogrid main body and ABS material for entire CanSat ensured high strength to weight ratio and made sure the natural frequencies of CanSat are well above the launch vehicle's vibration frequencies.
 
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
