---
title: "Two wheel Balancing Robot"
date: 2026-06-01
summary: "Custom designed two-wheeled balancing robot, simulated control algorithm in Simulink and physically validated it"
tags: ["robotics", "Control Sysytems", "mechatronics"]
cover:
  image: "model.png"
---



## Overview
This project explored control systems and mechatronics through the classic inverted-pendulum problem: getting a two-wheeled robot to stand upright on its own. Rather than just getting it working, the aim was to trace the full pipeline - from mathematical modeling, to controller design, to a physical build that behaves the way the simulation predicted.

## Objectives
- Build a Simulink model of the robot capturing its mass distribution, center of gravity, and motor behavior.
- Design a PID loop that converts pitch angle into corrective motor torque to keep the robot vertical.
- Fabricate and assemble a physical prototype driven by live sensor feedback.
- Build a wireless controller to retune PID gains on the fly, without reflashing firmware.

{{< figure src="cad.png" alt="CAD Model of Robot" caption="CAD Model of Robot" >}}

## Simulation and Control Design
The dynamics, weight, center of gravity, motor torque characteristics were built out in Simulink first, so the controller could be designed and tested before any hardware existed. A cascaded PID controller was tuned around pitch angle, converting tilt into a torque command for the drive motors.
{{< figure src="simu.png" alt="Simulink Model" caption="Simulink Model" >}}

## Hardware Build
The mechanical structure was designed as a full CAD assembly, then 3D printed and assembled. A Teensy microcontroller handled the real-time loop: reading IMU data over I2C and driving the motors via PWM. Since raw accelerometer and gyroscope readings are individually unreliable (drift and noise, respectively), a complementary filter fused the two into a stable angle estimate.

{{< figure src="model.png" alt="Final built robot" caption="Final built robot" >}}

## Wireless Tuning Tool
Re-flashing code every time a gain needed adjusting was slow, so a custom tuning device was built using NRF24L01 radio modules and potentiometers, letting Kp, Ki, and Kd be dialed in live while the robot was running. This turned tuning from a flash-test-repeat cycle into a live feedback loop, and real-time serial plots of sensor and controller output were used alongside it to catch instability quickly.



{{< figure src="tuner.jpeg" alt="Tuner" caption="Wireless Tuner" >}}

## Outcome
The robot recovered from disturbances of up to 10° in under 2 seconds, settling within 5 cm of its original position. The Simulink model tracked real hardware behavior closely enough that most tuning could happen in simulation first, cutting down iteration time on the physical unit. Overall, the project built practical experience across simulation, control design, and hardware-in-the-loop tuning.