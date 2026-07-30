---
title: "RoboCup International 2023, Bordeaux, France"
year: 2023
role: "Software and Electronics"
outcome: "Top 11 finish"
summary: "Line Follow with ramps, obstacles and a final evacuation zone which involved picking up, sorting and deposition of balls"
---

# ML-Based Rescue Robot — RoboCup Junior Rescue (France, Jan–Jul 2023)
**Role:** Electronics and Software

## Context & Goals
Built an autonomous rescue robot to detect and help simulated disaster victims. Objectives:
- Line-follow a course with ramps, speed bumps, obstacles and green patches
- Autonomously avoid obstacles and reach the evacuation zone
- Search for and rescue victims (silver/black balls) into correct safe zones
- Complete all tasks within 8 minutes

![CAD design of complete robot model — front and back view](placeholder-cad-design.png)

## Mechanical Design Evolution
- **Prototype 1:** Single motor per side, four-wheel drive via timing belts. Issues with this prototype - belt tension warped 3D-printed parts, misaligned wheels, and motor length made the chassis too wide.
- **Prototype 2:** Four servo motor prototype - rejected due to low RPM (high gear reduction) and no continuous-rotation encoder support.
- **Final drivetrain:** N20 geared motors with brass couplers to bearings; two normal wheels (front) + two omni wheels (rear) to reduce turning friction.
- Iterated through 6 prototypes total, validating ramp clearance, center-of-gravity stability, and overall performance at each stage. Designed all parts in Onshape with DFA/DFM principles (embedded nut slots, snap-fit tolerances).

![Testing sequence — ramp movement, front/back drive, 360° rotation, see-saw traversal](placeholder-testing-photos.png)

## Electronics & Control
- Switched microcontroller from Arduino Nano → Teensy 4.0 for more interrupt pins (Two pins for each motor) (needed for motor encoders).
- Added IMU (MPU6050) to detect ramp pitch angle and boost motor speed accordingly, preventing stalls.
- Multiple sensors having same I2C address required an I2C expander for simultaneous communication.
- PID motor control attempted via Ziegler–Nichols tuning, but static friction/motor lag forced a trial-and-error approach using encoder feedback instead.
- UART serial link between Raspberry Pi (high-level ML decisions) and Teensy (low-level motor/sensor logic).

![High-level system architecture diagram](placeholder-system-architecture.png)

## ML-Based Victim Detection
- Captured ~250 images of victims and safe zones under varied lighting; labeled with LabelImg (bounding boxes → XML).
- Split into train/validation/test sets; trained **SSD MobileNet v2 FPN-Lite 320** in TensorFlow, optimized as TensorFlow Lite for real-time inference on Raspberry Pi 4.
- **Tracking control:** error = 640 − (ball bounding-box midpoint x-pixel), computed from a 720×1280 camera frame center. Error × Kp sent to Teensy's steering block to keep the ball centered while approaching.
- A three-servo gripper then picks up the ball and deposits it into the correct container.

![Computer vision detection — silver ball identified with confidence score](placeholder-cv-detection.png)

## System Logic
Flowcharts governed obstacle avoidance, green/ramp detection via IR and color sensors, PID-based line following, and intersection handling — all integrated iteratively, module by module, to isolate integration bugs.

![Flowchart of software logic (line follow, obstacle, ramp, ML detection branches)](placeholder-flowchart.png)

## Results
- Completed all challenge tasks within the 8-minute limit
- Achieved >85% victim detection accuracy in the evacuation zone
- One of the Two teams which were able to successfully deposit all balls in their respective zones