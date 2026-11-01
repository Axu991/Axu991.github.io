---
title: "Underwater Acoustic Positioning: Principles, Methods, and Systems"
date: 2026-11-01
description: "How underwater vehicles determine their position with sound — the d = v × t principle, TOA/TDOA/AOA/phase-difference methods, LBL/SBL/USBL system classification, the technology roadmap, and real-world deployments."
tags: ["Underwater Acoustics", "Positioning", "USBL", "LBL", "TDOA", "Navigation"]
categories: ["Engineering Notes"]
---

{{< katex >}}

## The basic principle

Sound travels at roughly 1500 m/s in seawater — far slower than electromagnetic waves (3 × 10⁸ m/s). This low speed is precisely what makes acoustic positioning feasible: if sound were as fast as light, a few milliseconds of time difference would correspond to tens of kilometers, and nothing could be measured. Because sound is slow, measuring time differences and converting them to distances becomes practical.

The core formula is simple:

$$d = v \times t$$

where $d$ is the distance, $v$ the sound speed, and $t$ the propagation time. For example, a propagation time of 10 ms gives $d = 0.01 \times 1500 = 15$ m. Every underwater acoustic positioning method ultimately measures time or phase, converts it to distance, and determines position through multi-point intersection.

**The geometry of localization.** In two dimensions, knowing one distance places the target on a circle; two distances intersect at two points; three distances determine a unique position. In three dimensions, at least four distance measurements are required.

**The effect of sound speed.** The sound speed is not a constant — it varies with temperature, salinity, and depth, typically between 1450 and 1550 m/s. High-precision systems correct for this using a sound speed profile (SSP); low-precision ranging can simply use the 1500 m/s approximation.

## Positioning methods

### TOA (Time of Arrival) — ranging

TOA is the most basic ranging method: the transmitter sends a pulse at $t_1$, the receiver receives it at $t_2$, and the time difference $\Delta t = t_2 - t_1$ times the sound speed gives the distance:

$$d = v \times (t_2 - t_1)$$

The principle is simple and accuracy depends on time measurement precision. The catch is clock synchronization: both ends must share a common time reference, otherwise the time difference is wrong. Two independent MCUs with a 1 ms clock error introduce a 1.5 m distance error. Real systems often use a transponder (reply) scheme: the receiver immediately sends back an acknowledgment pulse, and the transmitter measures the round-trip time, divides by two, and multiplies by the sound speed — sidestepping synchronization at the cost of both ends being able to transmit.

### TDOA (Time Difference of Arrival) — hyperbolic positioning

TDOA does not require knowing the absolute transmission time; it only needs the time difference between the same signal arriving at different receivers. The time difference between two receivers maps to a distance difference, and the set of points with a constant distance difference forms a hyperbola:

$$\Delta d = v \times (t_2 - t_1)$$

Two receivers → one hyperbola (the position lies on it); three receivers → two hyperbolas intersect → 2D localization; four receivers → 3D localization. This is the core principle of LBL (Long Baseline) systems. The key implementations are multi-channel synchronized sampling and cross-correlation to extract time differences. The difficulty is multi-channel time synchronization: timing accuracy must reach the microsecond level for millimeter-level ranging. The larger the hydrophone spacing, the larger the time difference and the smaller the relative error — which is why the method is called "long baseline": longer baselines give higher accuracy.

### AOA (Angle of Arrival) — beamforming

AOA measures the direction from which the signal arrives. Two or more hydrophones form an array; the signal reaches different hydrophones at slightly different times (phase differences), from which the incidence angle is derived:

$$\sin\theta = \frac{v \times \Delta t}{d}$$

where $d$ is the hydrophone spacing and $\Delta t$ the time difference. Knowing both angle and distance locates the target. This is the principle of USBL (Ultra-Short Baseline) systems — the array spacing is very small and angles are measured via phase differences.

The difficulty is that angular errors are amplified by distance: a 1° angle error gives a 17 cm position error at 10 m, but 1.7 m at 100 m. USBL is therefore accurate at close range and degrades at long range.

### Phase-difference ranging

Instead of measuring pulse arrival times, this method measures the phase difference of the carrier, which is proportional to distance:

$$\Delta\varphi = 2\pi \times \frac{d}{\lambda}$$

where the wavelength $\lambda = v/f$. Accuracy is excellent (millimeter level). But phase is measured modulo $2\pi$, so the result is ambiguous — a measured 90° could mean 0.25 wavelengths or 1.25 wavelengths traveled. Practical systems combine TOA for coarse ranging and phase difference for fine ranging. The classic difficulty is the integer-cycle ambiguity: at 70 kHz the wavelength is about 2.1 cm, so every 2.1 cm the phase wraps once; ranging over meters requires knowing how many full wavelengths were crossed. GNSS positioning faces the same problem.

## System classification: LBL / SBL / USBL

Acoustic positioning systems are classified by baseline length — the spacing between hydrophones:

| Type | Baseline | Principle | Accuracy | Characteristics |
|---|---|---|---|---|
| LBL (long) | Hundreds to thousands of meters | Seabed transponders, TDOA hyperbolic intersection | Highest (meter level) | Accurate but hard to deploy; requires transponders on the seabed |
| SBL (short) | A few to tens of meters | Ship-mounted array, TDOA + AOA | Medium | Deployed on the ship; needs an underwater transponder |
| USBL (ultra-short) | Centimeters | Compact array, phase-difference angle | Medium (meter level) | Small and easy to install; accuracy limited by angular error |

**LBL:** 3–4 transponders are deployed on the seabed to form a baseline array. The vehicle measures its distance to each transponder and intersects the range circles to locate itself. Highest accuracy, unaffected by water depth, but deployment and recovery are cumbersome — suited to long-term fixed sites.

**SBL:** The hydrophone array is mounted on the ship's hull with meter-to-decameter spacing. With one underwater transponder, the ship measures range and bearing to compute relative position. More flexible than LBL, but accuracy is affected by ship attitude and water depth.

**USBL:** The array is compact (centimeter level), mounted on the ship's hull or on an ROV. Angles are measured by phase difference, combined with ranging to obtain position. Small, easy to install, and the most common choice for consumer-grade and small ROVs. A single 70 kHz transmit-receive pair is the basic building block of USBL.

## Technology roadmap

| Year | Event | Significance |
|---|---|---|
| 1963 | USS Thresher sinking | Triggered development; the origin of modern acoustic positioning, gave rise to SBL |
| 1965 | Alvin submersible with ALNAV acoustic navigation | First practical underwater acoustic navigation system |
| 1970 | Cestone, *Precise Underwater Navigation* | Systematized early underwater navigation principles |
| 1972 | Watkins & Schevill, TDOA hyperbolic method | Established the mathematical foundation of TDOA |
| 1972 | Davies, near-bottom acoustic navigation comparison | Analyzed LBL-class hyperbolic systems |
| 1980s | LBL commercialization | Acoustic positioning moved from military to civil use |
| 2000s | USBL miniaturization, low-cost options | Consumer ROVs and small platforms began to adopt it |
| 2020s | Sparse LBL, single-beacon, multi-sensor fusion | Lower deployment cost, miniaturization |

## Implementation essentials and difficulties

### Method comparison

| Method | Core implementation | Main difficulty | Accuracy |
|---|---|---|---|
| TOA ranging | Pulse edge detection, high-precision timing | Clock synchronization | cm–dm level |
| TDOA positioning | Multi-channel synchronized sampling, cross-correlation | Multi-channel sync, baseline geometry | meter level |
| AOA angle | Small-spacing array, phase-difference angle | Angular error grows with distance | meter level (close range) |
| Phase-difference ranging | Continuous carrier, I/Q quadrature demodulation | Integer-cycle ambiguity | mm–cm level |

### Three common challenges

**1. Non-uniform sound speed.** Temperature, salinity, and depth all affect sound speed (1450–1550 m/s). The sound speed profile varies with depth and rays bend rather than travel straight. High-precision positioning requires ray-tracing corrections; low-precision ranging uses the 1500 m/s approximation.

**2. Multipath.** Sound reflects off the surface and seabed, so the receiver sees reflected paths alongside the direct path. Reflected and direct signals overlap, widening pulses and blurring edges, increasing timing errors. This is why shallow-water positioning is harder than deep-water — multipath is severe in shallow water.

**3. The bandwidth–distance trade-off.** High frequencies (e.g. 70 kHz) give good directionality and accuracy but attenuate quickly and cover short ranges; low frequencies (e.g. 10 kHz) travel far but have poor directionality and lower accuracy. Once the transducer frequency is chosen, range and accuracy are largely fixed.

## Engineering applications

**Jiaolong manned submersible — USBL + LBL combined navigation.** Jiaolong, China's first 7,000-meter-class manned submersible, uses a combined USBL + LBL scheme. A USBL transducer on the mother ship tracks the submersible in real time, while a seabed LBL transponder array provides high-precision calibration. Sea trials show LBL accuracy exceeds USBL, but USBL is far more flexible to deploy — the two complement each other.

**Miniature USBL transducers for consumer ROVs.** With MEMS and miniaturization, USBL transducers are now palm-sized. Advanced Navigation's Subsonus, for example, is about 10 cm in diameter and under 1 kg, mounts directly on small ROVs or AUVs, delivers sub-meter positioning, and suits shallow near-shore operations.

**Open source: Raspi2USBL.** A Raspberry Pi-based open-source USBL positioning project designed for underwater robots. Sea trials report a maximum localization range of about 1300 m with stable slant range and bearing measurements. The project closely resembles a single-frequency (e.g. 70 kHz) transmit-receive scheme — low-cost hardware validating acoustic positioning.

### Industrial solutions at a glance

| System | Vendors | Accuracy | Range | Use case |
|---|---|---|---|---|
| USBL | Sonardyne, Kongsberg | Sub-meter to meter | Kilometers | Ship-based tracking of ROV/AUV |
| LBL | Sonardyne, Oceaneering | cm–dm | Hundreds of meters | Fixed-area high-precision operations |
| Miniature USBL | Advanced Navigation, EvoLogics | Meter level | Hundreds of meters | Small ROV/AUV near-shore operations |
| Open source | Raspi2USBL, piUSBL | Meter level | Tens to hundreds of meters | Research, prototype validation |

## References

- Cestone, J. Precise Underwater Navigation. Quadripartite Meeting of the Institutes of Navigation, Rome, 1970.
- Watkins, W. A., & Schevill, W. E. Sound source location axes in array measurements. *Deep-Sea Research*, 1972, 19: 143–152.
- Davies, R. A Theoretical Comparison of Acoustic Systems for Near-Bottom Navigation. 1972.
- Milne, P. H. Underwater Acoustic Positioning Systems. E. & F.N. Spon, London, 1983.
- Yardim, S., et al. LBL System for Underwater Acoustic Positioning: Concept and Equations. arXiv:2204.08255, 2022. ([arXiv](https://arxiv.org/abs/2204.08255))
- Sonardyne. The evolution of Sparse LBL (range-aiding): less is more in subsea positioning. 2026.
- Raspi2USBL: An open-source Raspberry Pi-based passive inverted ultra-short baseline positioning system. ([GitHub](https://github.com/ethanjinhuang/Raspi2USBL), [arXiv](https://arxiv.org/html/2511.06998v1))
