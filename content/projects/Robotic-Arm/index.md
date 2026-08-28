---
title: "A 4DoF Robotic Arm"
date: 2026-06-01
summary: "Custom designed 4 DoF Robotic arm for shape drawing with manually derived Inverse Kinematics and simulated in MuJoCo"
cover:
  image: "cover.png"
---

**Ongoing independent project · Mechanical design, servo control, kinematics, simulation**

<a href="https://github.com/sanjayramkumar27/4DoF_Robotic_Arm" class="gh-link">
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

## Objective

A robotic arm project built to work through the full mechatronics stack including mechanical design, closed-form kinematics and low-level motor control. Then extend into simulation based control in MuJoCo due to physical build's constraints (motor backlash, deadband and saturation) started limiting fow fast i could iterate.

{{< figure src="cover.png" alt="CAD Model of Arm" caption="CAD Model of Arm" >}}

## System Overview

### Phase 1 — Physical Prototype & Analytical Kinematics
- Designed in SolidWorks across two iterations: Prototype 1 in laser-cut acrylic and 3D-printed links, Prototype 2 fully 3D-printed and redesigned smaller to reduce inertia and simplify assembly.
- Removed the internal controller boards from MG995 servos, which were factory-tuned for low-inertia loads, and built a custom external controller tuned for the arm's actual load.
- Built a power distribution board for a 2s2p Li-ion pack: dual buck converters for load sharing, bulk electrolytics for stability, decoupling caps at each IC. 
- Derived a closed-form IK solution for the 4-DOF configuration by extending 2R planar-arm equations, since no general solution existed online for this joint layout. Validated it in Desmos and against a Simulink numerical solver, and it matched while running fast enough for real-time Arduino Uno control.
- Found structural resonance in Prototype 1 through testing and added servo dampers and foam damping, which measurably reduced vibration.
- Mapped the real-world reachable workspace using the validated IK model, accounting for actual servo travel limits (180°) rather than idealized ranges.

{{< figure src="board.jpg" alt="Power Distribution Board" caption="Power Distribution Board" >}}

### Phase 2 — Simulation & Real-Time Control
- Rebuilt the arm as an MJCF model in MuJoCo to allow rapid iteration on control strategy without physical rebuilds.
- Switched to numerical IK using damped least-squares, chosen for robustness across joint configurations as the model evolved, since Phase 1's closed-form solution would have needed re-deriving for each change.
- Rebuilt the actuation stack for direct motor control: MG995 servos driven via a DRV8833 H-bridge with custom PID.
- Building a tracking error analysis (commanded path vs. MuJoCo-ideal vs. real-arm forward-kinematics output) to quantify where simulation and hardware diverge.

{{< figure src="tracking_summary.png" alt="Tracking" caption="Tracking path and error in MuJoCo" >}}

## Design Decisions
- The stock servo tuning didn't match the arm's actual load, so I built custom PID control by removing the internal controller and taking feedback from the internal potentiometer.
- Derived the inverse kinematics from scratch and verified it in Desmos and simulink before implementing it in the physical arm.
- After extensive testing, realised first prototype had huge arm with high inertia so switched to lighter and smaller arms.
- Added dual buck converters in parallel to share the current load.
- Added bulk capacitors after noticing voltage dips during quick motions.
- Phase 1 used analytical IK because the Arduino Uno needed real-time speed
- Phase 2 switched to numerical IK once the bottleneck moved from runtime speed to iteration speed.
- Noticed vibrations in the first prototype and added Servo dampers and foam damping which reduced the vibrations and improved tracking error.

{{< youtube w32eCuOhc1g >}}

## Future Work
- Complete tracking error analysis (commanded vs. simulated vs. real).
- Finish the multi-servo PWM driver circuit for full 4-DOF control from a single Arduino Uno.
- Extend trajectory generation beyond shape-drawing to general path planning.

