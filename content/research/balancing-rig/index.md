---
title: "Rotational Imbalance Measurement & Correction System"
date: 2026-06-01
summary: "A low-cost Testing rig that detect mass imbalance in a rotating disk and computes required correction"
tags: ["robotics", "aerospace", "mechatronics"]
cover:
  image: "cover.jpeg"
---

**AE341 Aerospace Structures Lab, IIST, April 2026**
 
Balancing a rotating machine commercially requires purpose-built shop equipment. This project asked whether the same job, locating and canceling a rotor's mass imbalance, could be done instead with an IMU, an IR tachometer, and a calibration procedure.


## The problem
 
A rotating system with its center of mass off the spin axis vibrates, wears bearings faster, and generates noise proportional to how far off-axis that mass sits. Fixing it means finding both where the imbalance is and how large it is, then adding a correction mass at the opposite phase.


 
## Approach
 
The rig: a 3D-printed test frame, a 775 DC motor spinning a disk, an IR sensor for RPM, an MPU6050 IMU for vibration, an Arduino Uno for acquisition, and an L298D driver.

<div class="image-pair">
  {{< figure src="cad_front.jpeg" alt="CAD Model" caption="Front View" >}}
  {{< figure src="cad_iso.jpeg" alt="CAD Model" caption="Isometric View" >}}
</div>
 
At steady RPM, imbalance shows up as vibration at the rotation frequency. An FFT of the accelerometer signal isolates that component as a complex number A = |A|e^(jφ): magnitude gives the imbalance size, phase (relative to the IR pulse) gives its angular position.
 
Calibration ran in two steps. A baseline vibration vector, measured with no added mass, captured the disk's inherent imbalance. A known test mass at a known angle then gave a magnitude scaling factor, grams per unit FFT amplitude. For an unknown imbalance, subtracting the baseline vector from the measured one (normalized for sample count and RPM) isolated the unknown component, and the correction mass went on at the opposite phase (φ + π) with equal magnitude.


{{< figure src="iso.jpeg" alt="Test Rig" caption="Test Rig" >}}

## Results
 
The rig correctly localized a 1 g test imbalance at both 270° and 150°, within a few degrees. Repeated trials at 60° gave an average phase of 58.14° (3.1% error) and an average magnitude of 0.872 g (12.8% error); phase estimation was consistently more accurate than magnitude estimation across trials. Applying the computed correction measurably flattened the FFT peak, confirming the correction worked rather than just matching a formula on paper.
 
Vibration isolation of the rig base mattered as much as the signal processing itself. Without it, the accelerometer signal was dominated by mounting noise rather than the clean sinusoid the FFT phase extraction needed.



{{< figure src="150_img.png" alt="result" caption="Results for 1g imbalance placed at 150°" >}}

## Takeaways
 
Magnitude accuracy was bounded by the Arduino's sampling rate and the minimum achievable motor RPM, since both FFT frequency resolution and amplitude fidelity depend on them. A single-plane balancing problem can be solved end to end with off-the-shelf parts and a calibration-based FFT approach, no commercial balancing hardware required.
 
**Tools:** 3D printing, Arduino/PlatformIO, MPU6050 IMU, IR-based RPM sensing, FFT-based signal processing, Python (data acquisition and analysis)
