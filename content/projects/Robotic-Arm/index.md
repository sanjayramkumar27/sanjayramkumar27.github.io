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

A robotic arm project built to work through the full mechatronics stack including mechanical design, closed-form kinematics and low-level motor control, paired with a MuJoCo model. The two run parallel. The simulations where the control strategies get iterated on and the hardware is where the model's assumptions get tested against backlash, dead-band and saturation.

{{< figure src="cover.png" alt="CAD Model of Arm" caption="CAD Model of Arm" >}}

## System Overview

### Hardware

- I went through two SolidWorks iterations. Prototype 1 used laser-cut acrylic and 3D-printed links. Prototype 2 is fully 3D-printed and smaller, which reduced inertia and simplified assembly. Prototype 1 showed structural resonance in testing, and servo dampers and foam damping measurably reduced the vibration.

- The power board is a custom distribution board for a 2s2p Li-ion pack. It uses dual buck converters for load sharing and has decoupling at each IC. I added bulk capacitors after seeing voltage dips during fast motions.

- For actuation, I removed the internal controller boards from the MG995 servos because they were tuned for low-inertia loads. The motors now run through a DRV8833 H-bridge with a custom PID loop that uses the servo’s internal potentiometer for feedback.

- I derived a closed-form IK solution for the 4-DOF layout by extending the 2R planar-arm equations, since no general solution existed online. I validated it in Desmos and against a Simulink numerical solver. It runs fast enough for real-time control on an Arduino Uno.

  <div class="image-pair">
  {{< figure src="board.jpg" alt="Power Distribution Board" caption="Power Distribution Board" >}}
  {{< figure src="arm.jpg" alt="Robotic Arm" caption="Robotic Arm" >}}
  </div>



### Simulation

- I built a digital twin of the arm in MuJoCo to simulate different control strategies. 

- As a first test, I used a simple damped least-squares Jacobian IK and analytical IK to make the simulated arm draw a circle.

- The main reason I moved to simulation is hardware. The motors need two PWM pins each, so three of them use six pins, and an Uno has only six PWM pins in total. A servo can be driven from any digital pin, but the Servo library uses the same timer that generates PWM, which interferes with the motor outputs.

{{< figure src="tracking_summary.png" alt="Tracking" caption="Tracking path and error in MuJoCo" >}}


## Design decisions

- Hardware: the stock servo tuning didn’t match the load, so I removed the internal controller and built an external PID.
- Hardware: the first prototype’s inertia and vibration led to a lighter redesign plus damping.
- Hardware: I used analytical IK because the Uno needed the runtime speed.
- Hardware: parallel buck converters and bulk capacitors give load sharing and voltage stability.
- Simulation: I moved to numerical IK once iteration speed became the bottleneck.

{{< youtube w32eCuOhc1g >}}

## Future work
- Bring up all four joints, including the multi-servo driver circuit so a single Arduino Uno can control them.
- Finish the tracking error analysis (commanded vs. simulated vs. real).
- Extend trajectory generation beyond shape drawing to general path planning.
