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

An autonomous rescue robot built to find and evacuate simulated disaster victims inside a fixed eight-minute run: line-follow a course with ramps, speed bumps, obstacles and green patches, reach the evacuation zone without help, then locate and sort victims (silver and black balls) into the correct safe zones.

## Mechanical design
 
Getting a reliable drivetrain took three attempts. The first prototype ran one motor per side through timing belts to four wheels; belt tension warped the 3D-printed parts, threw the wheels out of alignment, and the motor length made the chassis too wide. The second swapped to four servo motors, dropped once the gear reduction needed for enough torque brought RPM too low to support continuous-rotation encoders. The final drivetrain used N20 geared motors on brass couplers to bearings, two standard wheels up front and two omni wheels at the rear to cut turning friction. Six prototypes went through this cycle, each checked for ramp clearance, center-of-gravity stability, and overall performance, with parts designed in Onshape around DFA/DFM principles: embedded nut slots, snap-fit tolerances.


<div class="image-pair">
  {{< figure src="Front.jpg" alt="CAD Model" caption="Front View" >}}
  {{< figure src="back.jpg" alt="CAD Model" caption="Back View" >}}
</div>

## Electronics and control
 
Encoder feedback needed two interrupt pins per motor, more than an Arduino Nano could offer, so the build moved to a Teensy 4.0. An MPU6050 IMU sensed ramp pitch and boosted motor speed on inclines to stop the robot stalling. Several sensors shared the same I2C address, which needed an I2C expander to let them run at the same time. Motor control started as PID with Ziegler-Nichols tuning, but static friction and motor nonlinearities made that unreliable, so tuning switched to trial and error against encoder feedback instead. A UART link split the workload between a Raspberry Pi running high-level ML decisions and the Teensy handling low-level motor and sensor logic.


## ML-based victim detection
 
About 250 images of victims and safe zones, shot under varied lighting and labeled with LabelImg, trained an SSD MobileNet v2 FPN-Lite 320 in TensorFlow after a train/validation/test split. Tracking control computed an error term from a 720×1280 camera frame, 640 minus the ball's bounding-box midpoint x-pixel, and fed error times Kp into the Teensy's steering block to keep the ball centered on approach. A three-servo gripper handled the pick-up and drop into the correct container.


{{< figure src="camera.jpg" alt="Camera View" caption="Silver Ball Detection" >}}

## System logic
 
Flowcharts handled obstacle avoidance, green and ramp detection via IR and color sensors, PID line-following, and intersection handling. Building and testing each module on its own before integration made bugs much easier to isolate once everything came together.


{{< figure src="flow.png" alt="Flowchart" caption="Flowchart of System Architecture" >}}

## Results
The robot completed all tasks inside the eight-minute limit, hit over 85% victim detection accuracy in the evacuation zone, and was one of only two teams at the competition to get every ball into its correct zone.

{{< figure src="real.jpg" alt="Robot" caption="Final Robot" >}}

**Tools:** Onshape, Teensy 4.0, Raspberry Pi, TensorFlow (SSD MobileNet v2), LabelImg