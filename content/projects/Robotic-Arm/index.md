---
title: "A 4DoF Robotic Arm"
date: 2026-06-01
summary: "Custom designed 4 DoF Robotic arm with manually derived Inverse Kinematics and simulated in MuJoCo"
tags: ["robotics", "Control Sysytems", "mechatronics"]
cover:
  image: "cover.png"
---

**Ongoing independent project · Mechanical design, servo control, kinematics, simulation**

A robotic arm project built in two phases. The first was a physical build with custom mechanical and electrical design and closed-form kinematics. The second moved to simulation, using numerical control methods to iterate faster and work toward a shape-drawing demo.

{{< figure src="cover.png" alt="CAD Model of Arm" caption="CAD Model of Arm" >}}

## Phase 1 — Physical Prototype & Analytical Kinematics
- Designed in SolidWorks across two iterations: Prototype 1 in laser-cut acrylic and 3D-printed links, Prototype 2 fully 3D-printed and redesigned smaller to reduce inertia and simplify assembly.
- Removed the internal controller boards from MG995 servos, which were factory-tuned for low-inertia loads, and built a custom external controller tuned for the arm's actual load.
- Built a power distribution board for a 2s2p Li-ion pack: dual buck converters for load sharing, bulk electrolytics for stability, decoupling caps at each load. Added a PCA9685 driver to work around the Arduino Uno's PWM limits.
- Derived a closed-form IK solution for the 4-DOF configuration by extending 2R planar-arm equations, since no general solution existed online for this joint layout. Validated it in Desmos and against a Simulink numerical solver, and it matched while running fast enough for real-time Arduino Uno control.
- Found structural resonance in Prototype 1 through testing and added servo dampers and foam damping, which measurably reduced vibration.
- Mapped the real-world reachable workspace using the validated IK model, accounting for actual servo travel limits (180°) rather than idealized ranges.

{{< figure src="board.jpg" alt="Power Distribution Board" caption="Power Distribution Board" >}}

## Phase 2 — Simulation & Real-Time Control
- Rebuilt the arm as an MJCF model in MuJoCo to allow rapid iteration on control strategy without physical rebuilds.
- Switched to numerical IK using damped least-squares, chosen for robustness across joint configurations as the model evolved, since Phase 1's closed-form solution would have needed re-deriving for each change.
- Generated a 2D reachability map and trapezoidal path timing profiles to drive shape-drawing trajectories.
- Rebuilt the actuation stack for direct motor control: MG995 servos driven via a DRV8833 H-bridge with custom PID.
- Building a three-trace tracking error analysis (commanded path vs. MuJoCo-ideal vs. real-arm forward-kinematics output) to quantify where simulation and hardware diverge, targeted as a shape-drawing demo within a two-week build window.

## Design Decisions
The stock servo tuning didn't match the arm's actual load, so I built custom PID control instead of living with the degraded performance that came with it. Phase 1 used analytical IK because the Arduino Uno needed real-time speed; Phase 2 switched to numerical IK once the bottleneck moved from runtime speed to iteration speed as the model kept changing. Vibration and tracking error were treated as things to measure and test rather than judge by eye, so both got instrumented instead of eyeballed.

## Future Work
- Complete three-trace tracking error analysis (commanded vs. simulated vs. real).
- Finish the multi-servo PWM driver circuit for full 4-DOF control from a single Arduino Uno.
- Extend trajectory generation beyond shape-drawing to general path planning.