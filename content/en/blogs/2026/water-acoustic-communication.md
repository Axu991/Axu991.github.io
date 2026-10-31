---
title: "Underwater Acoustic Communication: An Introductory Guide to Modulation"
date: 2026-10-31
description: "How digital data is carried by sound underwater — channel characteristics, OOK/FSK/PSK/OFDM modulation, a side-by-side comparison, the technology roadmap, and the foundational papers of the field."
tags: ["Underwater Acoustics", "Communication", "Modulation", "OFDM", "Sonar", "piUSBL"]
categories: ["Engineering Notes"]
---

## Why acoustic communication?

Underwater acoustic communication is the technology that uses sound waves to carry information through water. Electromagnetic waves (radio, Wi-Fi, Bluetooth) attenuate extremely fast in seawater — practical ranges are usually a few meters at most. Sound, by contrast, propagates hundreds of meters to tens of kilometers in the ocean, making it the only practical wireless medium underwater. The field has been developing for over a century, from early underwater bell navigation to modern underwater sensor networks.

### The underwater acoustic channel

The underwater acoustic channel is one of the most challenging in communications:

| Characteristic | Description | Consequence |
|---|---|---|
| Slow propagation | ~1500 m/s, 1/200,000 of the speed of light | Large propagation delay, poor real-time performance |
| Severe attenuation | Higher frequencies attenuate faster | Narrow usable bandwidth, usually a few kHz |
| Multipath | Reflections from surface and seabed | Signal distortion, inter-symbol interference |
| Doppler | Relative motion shifts frequency | Receiver must track frequency |
| Ambient noise | Waves, marine life, ship noise | Low SNR, high bit-error rate |

### System structure

A typical system consists of: transmitter (signal source → modulation → power amplifier → transducer), the acoustic channel, and receiver (transducer → preamplifier → demodulation → data processing). The core of the whole chain is modulation — how digital bits are encoded into sound.

## OOK (On-Off Keying)

**Principle:** the simplest scheme. Presence of a carrier means 1, absence means 0. Analogy: a flashlight — on is 1, off is 0.

**Transmitter:** for an input bitstream such as `10111001` — bit 1 turns on the carrier and emits a 75 kHz sine burst; bit 0 turns it off.

**Receiver:** samples the signal, estimates energy; above threshold → 1, below → 0.

- Pros: simplest to implement, low hardware cost, low power.
- Cons: poor noise immunity, vulnerable to interference, low data rate.
- Use case: low-speed short-range links, simple remote control, ranging pulses.

## FSK (Frequency Shift Keying)

**Principle:** different carrier frequencies represent bits. Frequency f1 means 0, frequency f2 means 1. Analogy: a whistle — low note is 0, high note is 1.

**Transmitter:** bit 0 emits f1 (e.g. 70 kHz), bit 1 emits f2 (e.g. 75 kHz).

**Receiver:** samples the signal, passes it through two band-pass filters for f1 and f2; the stronger output decides the bit.

- Pros: better noise immunity than OOK; no precise phase synchronization needed.
- Cons: wide bandwidth, frequency switching takes time, medium data rate.
- Use case: medium/low-speed links where interference resistance matters.

## PSK (Phase Shift Keying)

**Principle:** phase changes represent bits. In basic BPSK: phase 0° is 0, phase 180° is 1. Analogy: clapping — clap with the left hand for 0, right hand for 1; frequency and amplitude stay unchanged, only the starting direction flips.

**Transmitter:** bit 0 emits a normal-phase carrier; bit 1 emits an inverted (180°) carrier.

**Receiver:** needs a local reference carrier; multiplies it with the received signal — same phase gives a positive voltage (0), opposite phase gives a negative voltage (1).

- Pros: high spectral efficiency (higher rate than OOK/FSK at the same bandwidth), strong noise immunity.
- Cons: complex to implement, needs precise carrier synchronization, sensitive to Doppler.
- Use case: medium/high-speed links, bandwidth-limited scenarios.

## OFDM (Orthogonal Frequency Division Multiplexing)

**Principle:** splits a high-speed data stream into many low-speed streams, modulated onto a large set of mutually orthogonal subcarriers transmitted in parallel. Analogy: a wide road with dozens of lanes running cars simultaneously — each lane is slow, but the total throughput is high.

**Characteristics:**

1. Many subcarriers — typically dozens to hundreds.
2. Each subcarrier may use BPSK/QPSK/QAM.
3. Strong multipath resistance — each subcarrier's low rate means little inter-symbol interference.
4. High spectral efficiency — orthogonal subcarriers waste no bandwidth.

- Pros: highest data rate, strongest multipath resistance, the mainstream of modern underwater communication.
- Cons: most complex, needs DSP or FPGA processing, demanding synchronization.
- Use case: high-speed links, underwater sensor networks, real-time video.

## Comparison and selection

| Scheme | Controls | Spectral efficiency | Noise immunity | Complexity | Typical rate |
|---|---|---|---|---|---|
| OOK | Signal presence | Low | Weak | Simplest | Hundreds of bps |
| FSK | Signal frequency | Medium-low | Medium | Simple | Thousands of bps |
| PSK | Signal phase | Medium-high | Strong | Complex | Tens of thousands of bps |
| OFDM | Parallel subcarriers | Highest | Strongest | Most complex | Hundreds of thousands of bps |

Selection from simple to complex, by need:

- Ranging / simple on-off control → OOK is enough.
- Low-speed data with moderate interference → FSK.
- Medium/high-speed, limited bandwidth → PSK.
- High-speed with DSP capability → OFDM.

## Technology roadmap

| Era | Milestone | Significance |
|---|---|---|
| Late 19th c. | Underwater bell navigation | Earliest acoustic communication — a bell near a lighthouse |
| 1914 | Fessenden echo ranging | First use of sound to detect icebergs; the beginning of underwater acoustics |
| 1940s | WWII active sonar | Anti-submarine detection; sonar matured |
| 1981 | DPSK underwater communication | Digital modulation enters the field |
| 2000s | OFDM acoustic modems | High-speed underwater communication takes off |
| 2010s | Software-defined acoustic modems | Flexible, reconfigurable, multi-band |
| 2020s | Underwater sensor networks | Multi-node networking, the underwater Internet of Things |

Trend: from analog to digital, from non-coherent to coherent, from single-carrier to multi-carrier, from point-to-point to networked. Modern systems widely adopt OFDM + MIMO; rates have grown from tens of bps to tens of kbps and beyond.

## Foundational papers

1. **Akyildiz, Pompili & Melodia (2005)** — *Underwater acoustic sensor networks: research challenges*. The foundational survey of underwater acoustic sensor networks: systematically reviews channel characteristics (attenuation, multipath, Doppler), proposes the network architecture, and identifies MAC, routing, and localization as key challenges. It established the framework of the whole research field. ([DOI](https://doi.org/10.1016/j.adhoc.2005.01.002))

2. **Stojanovic (2009)** — *Underwater acoustic communications: Design considerations on the physical layer*. Analyzes how the channel constrains physical-layer design, compares OOK/FSK/PSK performance in underwater channels, and discusses equalization and synchronization. The classic reference for modem design.

3. **Kilfoyle & Baggeroer (2000)** — *The evolution of underwater acoustic communication*. A historical review from WWII sonar to digital modulation, tracing the analog→digital, non-coherent→coherent evolution. The standard reference for the field's development. ([DOI](https://doi.org/10.1109/48.820738))

4. **Stojanovic & Preisig (2009)** — *Underwater acoustic communication channels: Propagation models and statistical characterization*. Establishes propagation models and statistical characterization — multipath structure, delay spread, Doppler spread — the standard channel model for simulation and design. ([DOI](https://doi.org/10.1109/MCOM.2009.4752682))

5. **WHOI piUSBL (2015)** — *Passive inverted ultra-short baseline (piUSBL) localization*. A surface beacon transmits periodically; the underwater vehicle carries only a receiving array and never transmits, measuring range and angle. Low power and cost on the underwater side — the mainstream low-cost localization scheme, later validated by MIT's SandShark AUV.

6. **Raspi2USBL (2025)** — *An open-source Raspberry Pi-based passive inverted ultra-short baseline positioning system*. A fully open-source piUSBL implementation — hardware, firmware, and host software — dropping commercial USBL costs to the hundreds of yuan level. ([GitHub](https://github.com/ethanjinhuang/Raspi2USBL), [arXiv](https://arxiv.org/html/2511.06998v1))

7. **Jensen et al. (2016)** — *Software-defined underwater acoustic modems: Historical review and the NILUS approach*. Proposes software-defined modems using general-purpose hardware plus configurable software, supporting multiple bands and modulation schemes, moving the field from dedicated hardware to a reconfigurable platform.

## References

- Akyildiz I F, Pompili D, Melodia T. Underwater acoustic sensor networks: research challenges. *Ad Hoc Networks*, 2005, 3(3): 257–279.
- Stojanovic M. Underwater acoustic communications: Design considerations on the physical layer. *Proc. WUWNet*, 2009.
- Kilfoyle D, Baggeroer A B. The evolution of underwater acoustic communication. *IEEE Journal of Oceanic Engineering*, 2000.
- Stojanovic M, Preisig J. Underwater acoustic communication channels: Propagation models and statistical characterization. *IEEE Communications Magazine*, 2009, 47(1): 84–89.
- 水声通信与组网技术综述. 电子与信息学报, 2023.
- Jensen S, et al. Software-defined underwater acoustic modems: Historical review and the NILUS approach. *IEEE Journal of Oceanic Engineering*, 2016.
- Sherman D H, et al. Underwater acoustic positioning systems: A review. *Marine Geophysical Researches*, 2016.
