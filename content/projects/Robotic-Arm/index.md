---
title: "A 4DoF Robotic Arm"
date: 2026-06-01
summary: "Custom designed 4 DoF Robotic arm with manually derived Inverse Kinematics and simulated in MuJoCo"
tags: ["robotics", "Control Sysytems", "mechatronics"]
cover:
  image: "cover.png"
---

# Custom 4-DOF Robotic Arm

**Ongoing independent project · Mechanical design, servo control, kinematics, simulation**

A robotic arm project spanning two distinct phases: a physical-build phase focused on custom mechanical and electrical design with closed-form kinematics, followed by a simulation-driven phase applying numerical control methods to support faster iteration and a concrete shape-drawing demo. The shift from analytical to numerical IK between phases was a deliberate trade-off, not a course correction — closed-form geometry was sufficient for a single fixed configuration on hardware, but simulation-based development called for a method that generalizes without re-deriving equations by hand each time the model changed.

{{< figure src="cover.png" alt="CAD Model of Arm" caption="CAD Model of Arm" >}}

## Phase 1 — Physical Prototype & Analytical Kinematics
- Designed in SolidWorks across two iterations: Prototype 1 in laser-cut acrylic with 3D-printed joints, Prototype 2 fully 3D-printed and redesigned smaller to cut inertia and simplify assembly.
- Removed the internal controller boards from MG995 servos — factory-tuned for low-inertia loads — and built a custom external controller sized for the arm's actual load.
- Built a power distribution board for a 2s2p Li-ion pack: dual buck converters for load sharing, bulk electrolytics for stability, decoupling caps at each load. Integrated a PCA9685 driver to work around the Arduino Uno's PWM limits; evaluated TLC59711 as a higher-frequency alternative.
- Derived a closed-form IK solution for the 4-DOF configuration by extending 2R planar-arm equations — no general solution existed online for this joint layout. Validated in Desmos and against a Simulink numerical solver, confirming a match while running fast enough for real-time Arduino Uno control.
- Diagnosed structural resonance in Prototype 1 through testing; added servo dampers and foam damping, measurably reducing vibration.
- Mapped the real-world reachable workspace using the validated IK model, accounting for actual servo travel limits (180°) rather than idealized ranges.

{{< figure src="board.jpg" alt="Power Distribution Board" caption="Power Distribution Board" >}}

## Phase 2 — Simulation & Real-Time Control
- Rebuilt the arm as an MJCF model in MuJoCo to support rapid iteration on control strategy without physical rebuilds.
- Switched to numerical IK: damped least-squares with nullspace projection, chosen for robustness across joint configurations as the model evolved — where Phase 1's closed-form solution would have needed re-derivation for each change.
- Generated a 2D reachability map and trapezoidal path timing profiles to drive shape-drawing trajectories.
- Rebuilt the actuation stack for direct motor control: MG995 servos driven via a DRV8833 H-bridge with custom PID, PCA9685 expander for multi-channel PWM on an Arduino Uno.
- Building a three-trace tracking error analysis — commanded path vs. MuJoCo-ideal vs. real-arm forward-kinematics output — to quantify where simulation and hardware diverge, targeted as a shape-drawing demo within a two-week build window.


## Engineering Judgment Demonstrated
- Recognized when off-the-shelf servo tuning didn't match actual load conditions and built corrective hardware rather than accepting degraded performance.
- Chose analytical IK for embedded real-time constraints in Phase 1, then deliberately switched to numerical IK in Phase 2 once the development bottleneck shifted from runtime speed to iteration speed.
- Treated vibration and tracking error as measurable, testable problems rather than assumed ones — instrumenting the system to quantify what's actually happening rather than relying on visual inspection.

## Future Work
- Complete three-trace tracking error analysis (commanded vs. simulated vs. real).
- Finish the multi-servo PWM driver circuit for full 4-DOF control from a single Arduino Uno.
- Extend trajectory generation beyond shape-drawing to general path planning.