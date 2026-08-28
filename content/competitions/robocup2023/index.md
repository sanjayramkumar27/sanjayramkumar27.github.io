---
title: "ML-Based Rescue Robot | RoboCup International 2023 Bordeaux, France"
year: 2023
role: "Software and Electronics"
outcome: "Represented India, Top 11 finish"
summary: "Line Follow with ramps, obstacles and a final evacuation zone which involved picking up, sorting and deposition of balls"
cover:
  image: "real.jpg"
---

**Role:** Electronics, Software and Control Systems

<a href="https://github.com/sanjayramkumar27/RoboCup_Rescue_line_2023" class="gh-link">
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

# Problem Statement
Build an autonomous robot that follows a line with green markers, avoid obstacles on the way, go over ramps, speedbumps, sew-saw. Inside the Evacuation zone, the robot's task is to pick up the silver balls and drop them in the green safe zone and pick up the black balls and drop them in a red safe zone.

# System Overview
- Mechanical Design
  - Went through 6 prototypes and finally landed on N20 motors for each wheel. Regular wheel in the front and omni-directional wheels in the back.
  - Fully designed in OnShape around DFA and DFM principles.
  - 3D printed parts with Arcylic chasis
  - 3 servo gripper for picking up balls
  - 2 containers to separate silver and black balls

  <div class="image-pair">
  {{< figure src="Front.jpg" alt="CAD Model" caption="Front View" >}}
  {{< figure src="back.jpg" alt="CAD Model" caption="Back View" >}}
</div>

- Electronics
  - Low level motor control and sensor data processing handled by Teensy 4.0
  - High level ML based object detection for balls and safe zone detection handled by Raspberry Pi 4
  - IMU - MPU6050, Motor encoders(one for each wheel), i2C expander, Colour sensors, Time of flight sensors, IR sensors
  - A total of 5 Servos for picking up the balls, separating and depositing them.
  - i2C expader enabled i2C communication to multiple sensors having same i2C address.

- Control Systems
  - PD control for each motor
  - Ball tracking during evacuation zone
  - Ramp detection followed by speed increase

- ML based victim and safe zone detection
  - TensoflowLite with SSD MobileNet v2 FPN-Lite 320
  - dataset contained 250 images of victims and safe zones under various lighting and exposure conditions
  - Split the dataset to train, test and validation
  - Images were labelled using labelImg software
  - Achieved over 85% accuracy in detection

  {{< figure src="camera.jpg" alt="Camera View" caption="Silver Ball Detection" >}}


## My Contributions
### Electronics
- Started with an Arduino Uno for the low level control, but since each motor required two interrupt pins, switched to Teensy 4.0 since it has more hardware interrupt pins
- Used Raspberry Pi 4 for the ML based object detection to distribute the workload across two computers.
- The robot required multiple colour and time of flight sensors and they had the same i2C address, so i used an i2C expander to communicate with each sensor independently
- Used MPU6050 for ramp detection and increase speed to avoid stalling. The IMU was also used to make accurate turnsin the evacuation zone in the presence of debris
- Used the Pi camera for Object detection
- Used Buck converters to achieve different levels of required voltage busses.
- Used limit switches to detect obstacles.

### Control Systems
- Implemented a PD controller for each motor to maintain a constant RPM under varied conditions
- Once a ball is detected, the robot must go to it and make sure the ball is at the centre while picking it up, to ensure that this happens I used the bounding box coordinates of the detected ball and used the middle of the camera frame as reference for a proportional ball tracking.


### Software
- The Teensy was programmed in C++ using the Arduino IDE. Instead of having all the code ina single file, chose to segregate it into functions and call them in the main loop which made the code more legible and debugging error much easier.
- The Raspberry Pi4 used Python+OpenCV+Tensorflow.
- A serial communication was setup between the Teensy and the RPi4, to initiate the ML algorithm, sending bounding box coordinates and initating ball picking up and dropping sequence.


{{< figure src="flow.png" alt="Flowchart" caption="Flowchart of System Architecture" >}}

## Results
The robot completed all tasks inside the eight-minute limit, achieved over 85% victim detection accuracy in the evacuation zone, and we were one of only two teams at the competition to get every ball into its correct zone.

{{< figure src="real.jpg" alt="Robot" caption="Final Robot" >}}


{{< youtube We57wuRG5Ek >}}