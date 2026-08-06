---
title: "Two wheel Balancing Robot"
date: 2026-06-01
summary: "Custom designed two-wheeled balancing robot, simulated control algorithm in Simulink and physically validated it"
tags: ["robotics", "Control Sysytems", "mechatronics"]
cover:
  image: "model.jpg"
---

A two-wheeled robot balancing on its own is the classic inverted-pendulum problem, used here to work through control systems and mechatronics end to end. The goal was not just getting it to stand upright, but following the whole pipeline: mathematical modeling, controller design, and a physical build that matched what the simulation predicted.

## Objectives
 
- Build a Simulink model capturing the robot's mass distribution, center of gravity, and motor behavior.
- Design a PID loop converting pitch angle into corrective motor torque to keep the robot vertical.
- Fabricate and assemble a physical prototype driven by live sensor feedback.
- Build a wireless controller to retune PID gains on the fly, without reflashing firmware.


{{< figure src="cad.jpg" alt="CAD Model of Robot" caption="CAD Model of Robot" >}}

## Simulation and control design
 
The dynamics, weight, center of gravity, and motor torque characteristics were modeled in Simulink first, so the controller could be designed and tested before any hardware existed. A cascaded PID loop tuned around pitch angle converts tilt into a torque command for the drive motors.


{{< figure src="simu.jpg" alt="Simulink Model" caption="Simulink Model" >}}

## Hardware build
 
The mechanical structure was designed as a full CAD assembly, then 3D-printed and assembled. A Teensy microcontroller ran the real-time loop, reading IMU data over I2C and driving the motors via PWM. Raw accelerometer readings drift and gyroscope readings are noisy on their own, so a complementary filter combined the two into a stable angle estimate.


{{< figure src="model.jpg" alt="Final built robot" caption="Final built robot" >}}

## Wireless tuning tool
 
Reflashing code every time a gain needed adjusting was slow, so a separate tuning device, built from NRF24L01 radio modules and potentiometers, dialed in Kp, Ki, and Kd live while the robot was running. That replaced the flash-test-repeat cycle with real-time feedback, and serial plots of the sensor and controller output next to it made instability easy to catch quickly.


{{< figure src="tuner.jpeg" alt="Tuner" caption="Wireless Tuner" >}}

## Outcome
 
The robot recovered from disturbances of up to 10° in under 2 seconds, settling within 5 cm of its original position. The Simulink model tracked real hardware behavior closely enough that most tuning happened in simulation first, cutting iteration time on the physical unit.
