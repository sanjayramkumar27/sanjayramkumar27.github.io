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

{{< youtube We57wuRG5Ek >}}