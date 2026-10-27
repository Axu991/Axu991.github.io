---
title: "Acoustic Communication and Positioning for Untethered Underwater Robots"
date: 2026-10-27
description: "A technical overview of underwater acoustic channels, communication waveforms, positioning methods, transducer fundamentals, and landmark research for untethered ROV/AUV systems."
tags: ["Underwater Robotics", "Acoustics", "USBL", "piUSBL", "AUV"]
categories: ["Research Notes"]
---

## Why acoustics?

Radio waves (2.4 GHz Wi-Fi/Bluetooth) penetrate only a few centimeters in seawater. Optical links reach tens of meters at Mbit/s rates but require a clear line of sight and fail in turbid water. Sound, however, propagates kilometers through seawater without a line of sight and requires relatively simple hardware. Once an underwater robot operates untethered, command, telemetry, and navigation must all travel acoustically.

## The underwater acoustic channel

### Sound speed and ranging

Seawater sound speed is approximately 1500 m/s (range 1450–1550 m/s, depending on temperature, salinity, and depth). Ranging works on the simple principle: distance = speed × travel time. A one-way travel time of 1 millisecond corresponds to roughly 1.5 meters.

### Absorption and transmission loss

Two mechanisms attenuate sound:

- **Spreading loss:** spherical spreading, 6 dB loss per doubling of distance.
- **Absorption loss:** seawater viscosity absorption, which rises sharply with frequency.

| Frequency | Absorption α | Loss at 10 m |
|---|---|---|
| 10 kHz | ~3 dB/km | negligible |
| 30 kHz | 10–20 dB/km | 0.2 dB |
| 100 kHz | ~100 dB/km | 1 dB |
| 200 kHz | ~300 dB/km | 3 dB |

This is the fundamental range-bandwidth tradeoff: high frequencies carry more data but die quickly; low frequencies travel far but at low bit rates.

### Multipath and Doppler

Sound reflects off the sea surface and seabed, arriving at the receiver via multiple paths. In shallow water (2–5 m depth), multipath delay spread can reach 7 ms, causing inter-symbol interference. This is why high-data-rate systems use OFDM — splitting the high-rate stream into many parallel subcarriers, each narrow enough to avoid multipath distortion.

Platform motion causes Doppler shift: at 1 m/s relative speed and 30 kHz carrier, the shift is about 20 Hz, which coherent systems must estimate and compensate.

### The sonar equation

All sonar systems — communication modems, pingers, positioning arrays — obey the same energy balance:

**SL − 2TL + TS − (NL − DI) = DT**

where SL is source level, TL is transmission loss, TS is target strength (receiver sensitivity for comm links), NL is ambient noise, DI is array directivity gain, and DT is the detection threshold. To increase range, you either raise SL, improve processing (lower DT), or use a directional array (higher DI).

## Communication waveforms

| Modulation | Principle | Rate | Multipath resistance | Complexity |
|---|---|---|---|---|
| BFSK | two fixed frequencies for 0/1 | 20–1000 bit/s | weak | very low |
| FH-FSK | frequency hopping | 0.5–5 kbit/s | medium | low |
| PSK | phase modulation | 5–20 kbit/s | medium | medium |
| S2C chirp | linear sweep spread spectrum | 7–62 kbit/s | strong | medium |
| OFDM | multi-carrier parallel | 15–60 kbit/s | very strong | high (DSP/FPGA) |

The physical limit: kilometer-range ≈ kbit/s, 100 m-range ≈ tens of kbit/s. Even 60 kbit/s only transmits CIF-quality video over 300 m. Wireless high-definition video is not physically achievable; all untethered underwater platforms store data on board and offload at the surface.

## Positioning: USBL, piUSBL, LBL, DVL

All acoustic positioning measures travel time. Four architectures:

| Method | Principle | Accuracy | Cost | Use case |
|---|---|---|---|---|
| USBL (ship-mounted) | ship array measures slant range + bearing | 0.1–1% slant range | $10k–100k | ship-towed ROV tracking |
| piUSBL (on-board) | robot array listens to diver beacon | 0.1% range, 0.1–1° bearing | <¥1k BOM (open source) | diver follow-me, AUV navigation |
| LBL (seafloor) | 3+ seafloor transponders, multilateration | sub-meter (range-independent) | $50k+ | fixed-site survey |
| DVL | Doppler velocity log, dead reckoning | drifts over hours | $5k+ | coupled with USBL/LBL |

### How piUSBL works

In the inverted (on-board) configuration, the robot carries a compact hydrophone array while the diver wears a small acoustic beacon that periodically emits short pulses. The robot measures the **Time Difference of Arrival (TDOA)** of each pulse across its hydrophones, solves the beacon's relative bearing angle, and fuses the result with an on-board pressure sensor for depth. Because the robot only listens and never transmits, it consumes very little power and does not betray its position.

Two landmark papers established this approach:

- **Rypkema et al. (MIT/IROS 2019)** validated piUSBL accuracy in controlled experiments: ~0.1% range accuracy (0.1 m error at 100 m), ~1° bearing accuracy, and hundreds of meters of operating range using a 5-hydrophone pyramidal array. The system is broadcast-based — one beacon can localize N robots simultaneously.
- **Wang et al. (Zhejiang University, IEEE RA-L 2022)** engineered a full piUSBL system on a disc-shaped AUV and sea-tested it in the South China Sea. This is the direct engineering predecessor of the open-source Raspi2USBL project.

The open-source [Raspi2USBL](https://arxiv.org/abs/2511.06998) (2025) reimplements the same architecture on a Raspberry Pi with four hydrophones, achieving 1.3 km tracking for under ¥1000 in parts.

### Reference case: iBubble

The commercial iBubble follow-me drone used exactly this architecture: 4-hydrophone piUSBL + pressure sensor fusion, 1.5–6 m follow range (25 m max), 60 m depth rating, 90 min endurance, 9 kg mass, and a 90-second lost-signal auto-surface safety behavior.

## Transducer fundamentals

The transducer is the hardware core of any acoustic system — it converts electrical signals to sound waves (transmission) and back (reception):

- **Hydrophone:** receives sound pressure and converts it to voltage.
- **Projector (transmit transducer):** amplifies and radiates electrical signals into acoustic energy.
- **Piezoelectric ceramic:** the most common transducer material, exploiting the reversible piezoelectric effect.
- **Directivity index:** how focused the beam is. Narrow beams give high gain but require precise aiming; wide beams cover more area but are less sensitive.
- **Operating frequency:** determines both transducer size and range — high-frequency transducers are small but short-ranged.

## Obstacle-avoidance sonar

Short-range obstacle detection (0.5–6 m) is mandatory on every untethered platform:

| Product | Type | Range | Accuracy |
|---|---|---|---|
| FIFISH Sonar Array | single-beam | 0.5–6 m | ±5 cm |
| Chasing Distance Lock | 4-direction | several m | ±25 mm |
| Chasing Omniscan 450FS | forward imaging | tens of m | imaging-grade |
| Ping 360 (BlueRobotics) | 360° scanning | 0.75–50 m | 1.6 mm@2 m |
| iBubble obstacle sonar | near-field | 1–1.5 m | — |

## Product landscape

| Platform | Type | Positioning | Depth | Price |
|---|---|---|---|---|
| SoFi (MIT) | research prototype | BFSK remote | 18 m | research |
| iBubble evo | follow-me drone | piUSBL | 60 m | ~$2999 |
| FIFISH V-EVO | tethered ROV | AI vision lock | 100 m | $1399 |
| FIFISH V6 Plus | tethered ROV | dual sonar + USBL | 100 m | ~$2000 |
| Hydrus | autonomous ROV | DVL+USBL+INS | 300 m | professional |
| Slocum glider | research platform | dead reckoning + GPS surfacing | 1000 m | research |
| Raspi2USBL | open source | piUSBL | lab | <¥1k BOM |

A notable trend: consumer follow-me platforms like FIFISH V-EVO have moved toward AI vision lock rather than pure acoustic follow, suggesting that acoustic-only follow has not yet become mainstream at the consumer price point.

## Foundational papers

Understanding these works gives the full field's development arc:

| Paper | Year | Contribution |
|---|---|---|
| Wenz, *Acoustic ambient noise in the ocean* | 1962 | ocean ambient noise spectrum — the design baseline for all sonar |
| Kilfoyle & Baggeroer, *The state of the art in underwater acoustic telemetry* | 2000 | survey of acoustic telemetry evolution |
| Stojanovic, *Recent advances in high-speed underwater acoustic communications* | 1996 | high-speed comm foundations |
| Stojanovic, *Phase coherent digital communications for underwater acoustic channels* | 1994 | modern phase-coherent modem foundation |
| Reis et al., *Design and Validation of a USBL System* | 2016 | detailed USBL design reference |
| Rypkema et al., *Passive Inverted USBL Localization* | 2019 | piUSBL accuracy validation |
| Wang et al., *piUSBL for Disc-shaped AUV* | 2022 | piUSBL engineering implementation |
| Raspi2USBL | 2025 | open-source piUSBL on Raspberry Pi |

## References

1. Katzschmann, R. K. et al. [*SoFi: Robotic Soft Fish for Untethered Observation.*](https://www.science.org/doi/10.1126/scirobotics.aao6622) Science Robotics, 2018.
2. [EvoLogics S2C Modem Datasheet.](https://www.evologics.de/en/products/acoustics/index.html)
3. Gallimore, C. et al. [*The WHOI Micro-Modem.*](https://www.whoi.edu/what-we-do/explore/instruments/instruments-sensors-and-sampling-operations/micro-modem/) OCEANS, 2010.
4. Sendra, S. et al. [*Underwater Acoustic Modems: a Review.*](https://www.mdpi.com/1424-8220/16/11/1821) Sensors, 2016.
5. Rypkema, N. R. et al. *Passive Inverted Ultra-Short Baseline (piUSBL) Localization: An Experimental Evaluation of Accuracy.* IEEE/RSJ IROS, 2019.
6. Wang, Y. et al. *Passive Inverted Ultra-Short Baseline Positioning for a Disc-shaped Autonomous Underwater Vehicle: Design and Field Experiments.* IEEE RA-L, 2022.
7. [Raspi2USBL: Open-source piUSBL.](https://arxiv.org/abs/2511.06998) arXiv:2511.06998, 2025.
