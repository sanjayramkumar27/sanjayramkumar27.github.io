---
title: "Rotational Imbalance Measurement & Correction System"
date: 2026-06-01
summary: "A low-cost Testing rig that detect mass imbalance in a rotating disk and computes required correction"
cover:
  image: "cover.jpeg"
---

**AE341 Aerospace Structures Lab, IIST, April 2026**

<a href="https://github.com/sanjayramkumar27/Structures_Test_Rig" class="gh-link">
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
Balancing a rotating machine commercially requires purpose-built shop equipment. This project did the same job, locating and canceling a rotor's mass imbalance,  with an IMU, an IR tachometer, and a calibration procedure at a smaller scale.
A rotating system with its center of mass off the spin axis vibrates, wears bearings faster, and generates noise proportional to how far off-axis that mass sits. Fixing it means finding both where the imbalance is and how large it is, then adding a correction mass at the opposite phase.

# System Overview
 
The rig: a 3D-printed test frame, a 775 DC motor spinning a disk, an IR sensor for RPM  and phase reference measurement, an MPU6050 IMU for vibration, an Arduino Uno for acquisition, and an L298D driver.

<div class="image-pair">
  {{< figure src="cad_front.jpeg" alt="CAD Model" caption="Front View" >}}
  {{< figure src="cad_iso.jpeg" alt="CAD Model" caption="Isometric View" >}}
</div>
 
At steady RPM, imbalance shows up as vibration at the rotation frequency. An FFT of the accelerometer signal isolates that component as a complex number A = |A|e^(jφ): magnitude gives the imbalance size, phase (relative to the IR pulse) gives its angular position.
 
Calibration ran in two steps. A baseline vibration vector, measured with no added mass, captured the disk's inherent imbalance. A known test mass at a known angle then gave a magnitude scaling factor, grams per unit FFT amplitude. For an unknown imbalance, subtracting the baseline vector from the measured one (normalized for sample count and RPM) isolated the unknown component, and the correction mass went on at the opposite phase (φ + π) with equal magnitude.


{{< figure src="iso.jpeg" alt="Test Rig" caption="Test Rig" >}}

# Results
 
The rig correctly localized a 1 g test imbalance at both 270° and 150°, within a few degrees. Repeated trials at 60° gave an average phase of 58.14° (3.1% error) and an average magnitude of 0.872 g (12.8% error); phase estimation was consistently more accurate than magnitude estimation across trials. Applying the computed correction measurably flattened the FFT peak, confirming the correction worked rather than just matching a formula on paper.
 
Vibration isolation of the rig base mattered as much as the signal processing itself. Without it, the accelerometer signal was dominated by mounting noise rather than the clean sinusoid the FFT phase extraction needed.


{{< figure src="150_img.png" alt="result" caption="Results for 1g imbalance placed at 150°" >}}

# Takeaways
 
Magnitude accuracy was bounded by the Arduino's sampling rate and the minimum achievable motor RPM, since both FFT frequency resolution and amplitude fidelity depend on them. A single-plane balancing problem can be solved end to end with off-the-shelf parts and a calibration-based FFT approach, no commercial balancing hardware required.
 
