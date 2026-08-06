---
title: "ML-Based Rescue Robot | RoboCup International 2023 Bordeaux, France"
year: 2023
role: "Software and Electronics"
outcome: "Top 11 finish"
summary: "Line Follow with ramps, obstacles and a final evacuation zone which involved picking up, sorting and deposition of balls"
cover:
  image: "real.jpg"
---

**Role:** Electronics, Software and Control Systems

## Goals
Built an autonomous rescue robot to detect and help simulated disaster victims:
- Line-follow a course with ramps, speed bumps, obstacles and green patches
- Autonomously avoid obstacles and reach the evacuation zone
- Search for and rescue victims (silver/black balls) into the correct safe zones (green for silver, red for black)
- Complete all tasks within 8 minutes

## Mechanical Design Evolution
- **Prototype 1:** Single motor per side, four-wheel drive via timing belts. Belt tension warped the 3D-printed parts, wheels went out of alignment, and the motor length made the chassis too wide.
- **Prototype 2:** Four servo motors, dropped because of low RPM (too much gear reduction) and no support for continuous-rotation encoders.
- **Final drivetrain:** N20 geared motors with brass couplers to bearings; two normal wheels up front, two omni wheels at the rear to cut turning friction.
- Six prototypes in total, each tested for ramp clearance, center-of-gravity stability, and overall performance. Parts were designed in Onshape with DFA/DFM principles built in (embedded nut slots, snap-fit tolerances).

<div class="image-pair">
  {{< figure src="Front.jpg" alt="CAD Model" caption="Front View" >}}
  {{< figure src="back.jpg" alt="CAD Model" caption="Back View" >}}
</div>

## Electronics & Control
- Switched microcontrollers from an Arduino Nano to a Teensy 4.0 for more interrupt pins, two per motor, needed for the encoders.
- Added an IMU (MPU6050) to sense ramp pitch and boost motor speed on inclines so the robot wouldn't stall.
- Several sensors shared the same I2C address, so an I2C expander was needed to let them talk at the same time.
- Tried PID motor control with Ziegler-Nichols tuning first, but static friction and motor nonlinearities made it unreliable. Switched to trial-and-error tuning against encoder feedback instead.
- UART link between the Raspberry Pi (high-level ML decisions) and the Teensy (low-level motor/sensor logic).

## ML-Based Victim Detection
- Captured about 250 images of victims and safe zones under varied lighting, labeled with LabelImg (bounding boxes to XML).
- Split into train/validation/test sets and trained an SSD MobileNet v2 FPN-Lite 320 in TensorFlow.
- **Tracking control:** error = 640 − (ball bounding-box midpoint x-pixel), from a 720×1280 camera frame. Error × Kp fed into the Teensy's steering block to keep the ball centered on approach.
- A three-servo gripper picks up the ball and drops it in the right container.

{{< figure src="camera.jpg" alt="Camera View" caption="Silver Ball Detection" >}}

## System Logic
Flowcharts handled obstacle avoidance, green/ramp detection via IR and color sensors, PID line-following, and intersection handling. Each module was built and tested on its own before integration, which made it much easier to isolate bugs.

{{< figure src="flow.png" alt="Flowchart" caption="Flowchart of System Architecture" >}}

## Results
- Completed all challenge tasks within the 8-minute limit
- Over 85% victim detection accuracy in the evacuation zone
- One of only two teams that got every ball into its correct zone

{{< figure src="real.jpg" alt="Robot" caption="Final Robot" >}}