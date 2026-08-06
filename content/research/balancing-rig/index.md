---
title: "Rotational Imbalance Measurement & Correction System"
date: 2026-06-01
summary: "A low-cost Testing rig that detect mass imbalance in a rotating disk and computes required correction"
tags: ["robotics", "aerospace", "mechatronics"]
cover:
  image: "cover.jpeg"
---

**AE341 Aerospace Structures Lab - IIST | April 2026**

## Overview

A low-cost rig that detects mass imbalance in a rotating disk and computes the correction mass and angular position needed to balance it, without any specialized balancing equipment.

## Problem

Rotating systems with an off-axis center of mass generate periodic vibration, noise, and mechanical wear. The goal was to build a system that measures where the imbalance sits and how large it is, then computes a correction.

## Approach

- **Hardware:** 3D-printed test rig, 775 DC motor spinning a disk, IR sensor for RPM, MPU6050 IMU for vibration, Arduino Uno for acquisition, L298D driver.
- **Signal model:** At steady RPM, imbalance produces vibration at the rotation frequency. An FFT of the accelerometer signal isolates this component as a complex number `A = |A|e^(jφ)`, where magnitude gives imbalance size and phase (relative to the IR pulse) gives angular position.
- **Calibration:** A baseline vibration vector is measured first to capture the disk's inherent imbalance. A known test mass at a known angle then gives a magnitude scaling factor (grams per unit FFT amplitude).
- **Measurement:** For an unknown imbalance, the baseline vector is subtracted from the measured vector (after normalizing for sample count and RPM) to isolate the unknown component. The correction mass goes on at the opposite phase (`φ + π`) with equal magnitude.

{{< figure src="iso.jpeg" alt="Test Rig" caption="Test Rig" >}}

## Results

- Correctly localized a 1 g test imbalance at both 270° and 150°, matching within a few degrees.
- Repeated trials for a 1 g imbalance at 60° gave an average phase of 58.14° (3.1% error) and an average magnitude of 0.872 g (12.8% error). Phase estimation was consistently more accurate than magnitude estimation.
- Applying the computed correction mass measurably flattened the FFT peak, confirming the correction worked.
- Vibration isolation of the rig base turned out to matter a lot: without it, the accelerometer signal was dominated by noise rather than the clean sinusoid needed for FFT phase extraction.


{{< figure src="150_img.png" alt="result" caption="Results for 1g imbalance placed at 150°" >}}

## Key Takeaways

- Magnitude accuracy is limited by the Arduino's sampling rate and the minimum achievable motor RPM, since both FFT frequency resolution and amplitude fidelity depend on them.
- Mechanical isolation of the sensing platform mattered as much as the signal processing pipeline.
- A single-plane balancing problem can be solved end-to-end with off-the-shelf components (IR sensor, IMU, Arduino) and a calibration-based FFT approach, no commercial balancing hardware required.

## Tools & Skills

3D printing, Arduino/PlatformIO, MPU6050 IMU, IR-based RPM sensing, FFT-based signal processing, Python (data acquisition and analysis), experimental calibration methodology.