---
title: "ADCS Team in Small Spacecraft Systems and Payload Centre"
date: 2026-06-01
summary: "Part of the ADCS team for InspireSat-3 (Student built satellite). Responsible for Simulink Simulation of Satellite dynamics with reaction wheel and Magnetorquers"
tags: ["Control Systems", "ADCS", "Satellite Dynamics", "Simulink"]
cover:
  image: "cover.png"
---

**Role:** ADCS Team Member | Small Spacecraft Systems and Payload Centre, IIST
**Tools:** MATLAB, Simulink, Simulink Satellite Viewer

## Overview
Designed and simulated the attitude control system for a student built CubeSat in a 500 km Sun-Synchronous Orbit, covering detumbling with magnetorquers, precision pointing with reaction wheels, and ground-station tracking maneuvers. Work spanned control design, dynamics modeling, and trajectory generation.

{{< figure src="simu.png" alt="Simulink Model" caption="Simulink Model" >}}

## 1. Detumbling — B-dot Algorithm
- Generated the SSO orbit (500 km, 97.5° inclination) and modeled the local magnetic field using the IGRF model.
- Built a Simulink model implementing the B-dot control law, using a 31.25 cm cube satellite (MOI: [3, 3, 10] kg·m²) actuated by magnetorquers (max moment 5 A·m²).
- Tested detumbling from an initial 10°/sec tumble along both the lower and higher inertia axes:
  - **Lower inertia axis:** settled in ~58 minutes
  - **Higher inertia axis:** settled in ~400 minutes
- Result highlighted how initial spin axis significantly affects detumbling time.

## 2. Pointing & Tracking — Reaction Wheel Control
- Built a full 6-DOF satellite dynamics + quaternion kinematics model in Simulink, driven by a PID controller commanding reaction wheel torque through a BLDC motor transfer function (closed-loop current control).
- Modeled four ground-station pass maneuvers: limb pointing → limb-to-GS transition → GS tracking → GS-to-limb transition, with reference angles generated from orbit time.
- Visualized results using Simulink Satellite Viewer with a 30° FOV sensor cone over a 97.5°, 500 km SSO.
- **Later iteration:** added reaction wheel desaturation logic and parameterized the reference-angle generation block for flexible mission timing.

{{< figure src="cover.png" alt="Satellite viewer" caption="Satellite Viewer" >}}

## 3. Trajectory Smoothing
- Identified that the original reference trajectory was continuous but not differentiable at maneuver transitions, causing discontinuous angular velocity commands and large torque spikes.
- Redesigned the transition function for first-order smoothness between limb-pointing and GS-tracking segments.
- **Result:** cleaner controller output, reduced oscillations, and improved tracking accuracy — verified by comparing angle/angular-velocity profiles before and after the change.

---
*This work was part of ongoing ADCS development at SSPACE, focused on building a control stack from detumbling through precision ground-station tracking for CubeSat operations.*