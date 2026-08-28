---
title: "Two-wheel Balancing Robot"
date: 2026-06-01
summary: "Custom designed two-wheeled balancing robot, simulated control algorithm in Simulink and physically validated it"
cover:
  image: "model.jpg"
---

**Solo project · Mechanical design, Control systems, Mechatronics, Simulation**

 <a href="https://github.com/sanjayramkumar27/Balancing_robot" class="gh-link">
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

A two-wheeled robot balancing on its own is the classic inverted-pendulum problem, used here to work through control systems and mechatronics end to end. The goal was not just getting it to stand upright, but following the whole pipeline: mathematical modeling, controller design, and a physical build.

- Build a Simulink model capturing the robot's mass distribution, center of gravity, and motor behavior.
- Design a PID loop converting pitch angle into corrective motor torque to keep the robot vertical.
- Fabricate and assemble a physical prototype driven by live sensor feedback.
- Build a wireless controller to retune PID gains on the fly, without reflashing firmware.


{{< figure src="cad.jpg" alt="CAD Model of Robot" caption="CAD Model of Robot" >}}

## System Overview

### Simulation and control design
 
The dynamics, weight, center of gravity, and motor torque characteristics were modeled in Simulink first, so the controller could be designed and tested before any hardware existed. A cascaded PID loop where pitch angle converts tilt into a torque command for the drive motors.


{{< figure src="simu.jpg" alt="Simulink Model" caption="Simulink Model" >}}

### Hardware build
 
The mechanical structure was designed as a full CAD assembly, then 3D-printed and assembled. A Teensy microcontroller ran the real-time loop, reading IMU data over I2C and driving the motors via PWM. Raw accelerometer readings drift and gyroscope readings are noisy on their own, so a complementary filter combined the two into a stable angle estimate.


{{< figure src="model.jpg" alt="Final built robot" caption="Final built robot" >}}

### Wireless tuning tool
 
Reflashing code every time a gain needed adjusting was slow, so a separate tuning device, built from NRF24L01 radio modules and potentiometers, dialed in Kp, Ki, and Kd live while the robot was running. That replaced the flash-test-repeat cycle with real-time feedback, and serial plots of the sensor and controller output next to it made instability easy to catch quickly.


{{< figure src="tuner.jpeg" alt="Tuner" caption="Wireless Tuner" >}}

## Design decisions
- Used a simple complementary filter to keep computation fast and still get reliable data.
- Added a tungsten weight at the top to increase the moment of inertia of the robot which increases the time of fall.
- Built a wireless tuner to tune the PID gains on the go, which is much faster than modifying and reuploading the code.
- Compact design that houses the batteries, microcontroller and other electronics.

## Outcome
 
The robot recovered from disturbances of up to 10° in under 2 seconds, settling within 10 cm of its original position. The Simulink model tracked real hardware behavior closely enough that most tuning happened in simulation first, cutting iteration time on the physical unit.


{{< youtube Nl21jv8NVsU >}}