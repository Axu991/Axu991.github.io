---
title: "Fundamentals of Acoustics: An Introductory Guide"
date: 2026-10-30
description: "A beginner-friendly primer on acoustic physics — sound speed, wavelength, sound pressure level, acoustic impedance, wave propagation, attenuation, and typical applications from sonar to medical ultrasound."
tags: ["Acoustics", "Underwater Robotics", "Physics", "Sonar", "Ultrasound"]
categories: ["Engineering Notes"]
---

{{< katex >}}

## What is sound?

Sound is a mechanical disturbance propagating through an elastic medium. When a source vibrates, it compresses and expands adjacent medium particles periodically, forming alternating regions of compression and rarefaction that travel outward as longitudinal waves. Sound requires a medium — there is no sound propagation in vacuum.

### Sound speed

Sound speed is determined by the elasticity and density of the medium:

$$c = \sqrt{K/\rho}$$

where $c$ is the sound speed (m/s), $K$ the bulk modulus (Pa), and $\rho$ the density (kg/m³).

| Medium | Temperature | Speed (m/s) | Ratio to air |
|---|---|---|---|
| Air | 20 °C | 343 | 1.0× |
| Water | 20 °C | 1482 | 4.3× |
| Seawater | 20 °C, 3.5% salinity | 1520 | 4.4× |
| Steel | Room temp | ~5000 | 14.6× |

A useful note: in gases, sound speed changes noticeably with temperature (about +0.6 m/s per 1 °C); in liquids and solids the variation is much smaller.

### Frequency classification

| Category | Frequency | Typical use |
|---|---|---|
| Infrasound | < 20 Hz | Seismic monitoring, typhoon warning, elephant communication |
| Audible | 20 Hz – 20 kHz | Speech, music |
| Ultrasound | > 20 kHz | Medical imaging, underwater sonar, ultrasonic cleaning |
| Hyper-sound | > 100 MHz | High-frequency acoustic microscopy, non-destructive testing |

## Frequency, wavelength, and wavenumber

Frequency $f$ is the number of vibration cycles per second (Hz). Wavelength $\lambda$ is the distance between adjacent in-phase points, related by:

$$\lambda = c / f$$

The wavenumber $k = 2\pi/\lambda$ describes the rate of spatial phase change.

### Wavelength and diffraction

Diffraction ability depends on the relative size of the obstacle and the wavelength:

| Obstacle size vs λ | Acoustic behavior | Everyday example |
|---|---|---|
| Obstacle << λ | Sound almost fully bends around | Subwoofer audible in the next room |
| Obstacle ≈ λ | Partial diffraction, partial reflection | Hearing part of the sound around a corner |
| Obstacle >> λ | Sound is blocked | High-pitched notes need an open door |

Analogy: low-frequency long-wavelength sound behaves like a fog lamp — light scatters in all directions; high-frequency short-wavelength sound behaves like a flashlight beam — directional but poor at bending around obstacles.

## Sound pressure level and decibels

Sound propagation causes pressure perturbations $p$ (Pa). The human ear perceives a huge dynamic range (roughly 10⁻⁵ to 10² Pa), so acoustics uses the logarithmic sound pressure level (SPL):

$$SPL = 20 \cdot \log_{10}(p/p_{ref}) \text{ dB}$$

where the reference pressure $p_{ref} = 20\ \mu$Pa (the threshold of hearing in air).

### Common SPL references

| Environment | SPL (dB) | Subjective feeling |
|---|---|---|
| Quiet recording studio | 20 | Almost silent |
| Quiet residence | 40 | Peaceful |
| Normal conversation (1 m) | 60 | Comfortable |
| Busy street | 80 | Noisy |
| Chainsaw / motorcycle | 100 | Uncomfortable; long exposure damages hearing |
| Front row at concert | 110–120 | Pain threshold; can damage hearing instantly |
| Rocket launch pad | 160+ | Risk of physical damage |

### The logarithmic nature of decibels

| SPL change | Pressure ratio | Energy ratio | Subjective feeling |
|---|---|---|---|
| +3 dB | 1.41× | 2× | Barely noticeable |
| +10 dB | 3.16× | 10× | Perceived as roughly twice as loud |
| +20 dB | 10× | 100× | Significantly louder |
| +40 dB | 100× | 10000× | Entirely different magnitude |

## Acoustic impedance and interface transmission

Acoustic impedance is the product of density and sound speed:

$$Z = \rho \cdot c \text{ (Rayl)}$$

It determines how much of a wave reflects versus transmits at a boundary between two media.

| Medium | Density (kg/m³) | Speed (m/s) | Impedance (Rayl) | Ratio to air |
|---|---|---|---|---|
| Air (20 °C) | 1.21 | 343 | 415 | 1 |
| Water (20 °C) | 998 | 1482 | 1.48×10⁶ | 3566 |
| Human soft tissue | ~1060 | ~1540 | ~1.63×10⁶ | 3928 |
| Steel | 7850 | 5000 | 3.93×10⁷ | 94700 |

### Impedance matching and transmission coefficient

At the interface between two media, the sound intensity transmission coefficient is approximately:

$$T = 4Z_1Z_2 / (Z_1 + Z_2)^2$$

When $Z_1 \approx Z_2$, $T \approx 1$ and nearly all energy passes through. When the impedances differ greatly, $T \approx 0$ and nearly everything reflects.

### Why medical ultrasound uses coupling gel

Air and human soft tissue differ in impedance by roughly 4000×. If an air layer sits between the probe and skin, transmission drops below 0.1%. The coupling gel applied before an ultrasound exam exists precisely to eliminate the air layer and match impedance.

## Wave propagation behaviors

- **Reflection** — at a sudden impedance change, part of the energy returns to the original medium. Hard surfaces (walls, rock, water surface) reflect strongly; soft absorbing materials (foam, fiber, carpet) reflect weakly. Echo is the perceivable reflected sound.
- **Refraction** — a wave changes direction when its speed changes across a medium interface, like light entering water. In the ocean, sound speed varies with depth, temperature, and salinity, forming a sound-speed profile that can trap waves in a deep channel (the SOFAR channel).
- **Diffraction** — sound bends around obstacle edges. Low-frequency long wavelengths diffract strongly, which is why infrasound can travel thousands of kilometers.
- **Interference** — two coherent waves superpose: in-phase positions reinforce, anti-phase positions cancel. Active noise cancellation (ANC) uses exactly this: a speaker emits a wave of equal amplitude and opposite phase to cancel the noise.

## Attenuation

Sound intensity weakens during propagation through two mechanisms:

### Spherical spreading loss

A point source radiates a spherical wavefront. As distance $r$ grows, energy spreads over $4\pi r^2$, so intensity falls as $1/r^2$. In dB terms:

$$\Delta SPL = 20 \cdot \log_{10}(r_2/r_1) \text{ dB}$$

Every doubling of distance costs about 6 dB (in free field).

### Medium absorption

Viscosity and thermal conduction convert acoustic energy into heat; the absorption coefficient rises with frequency.

| Frequency | Air absorption (dB/100 m) | Typical range |
|---|---|---|
| 1 kHz | ~1 | Several km |
| 10 kHz | ~10 | Hundreds of m |
| 100 kHz | ~100 | Tens of m |
| 1 MHz | ~1000 | A few m |

This is why underwater sonar operates in the tens-to-hundreds of kHz band: it balances propagation range against angular resolution.

## Typical applications

| Domain | Core principle | Key parameters |
|---|---|---|
| Medical B-mode ultrasound | Ultrasound reflection + impedance matching | 3.5–15 MHz |
| Underwater sonar / ranging | Ultrasound reflection and propagation | 20–200 kHz |
| Active noise-cancelling headphones | Wave interference cancellation | 20–1000 Hz |
| Ultrasonic cleaning / lithotripsy | High-frequency cavitation | 20–50 kHz |
| Architectural acoustics | Reflection and absorption control | Reverberation time 0.5–2 s |
| Infrasound early warning | Long-wavelength infrasound propagation | < 20 Hz |

## References

- 杜功焕, 朱哲民, 龚秀芬. 声学基础 (第三版). 南京大学出版社, 2001.
- 张海澜. 理论声学. 高等教育出版社, 2007.
- [MIT OpenCourseWare 6.542J: Laboratory on the Physiology, Acoustics, and Perception of Speech](https://ocw.mit.edu/courses/hst-710j-laboratory-on-the-physiology-acoustics-and-perception-of-speech-fall-2005/), Fall 2005.
- Blackstock D T. *Fundamentals of Physical Acoustics*. Wiley, 2000.
- Kinsler L E, Frey A R, Coppens A B, Sanders J V. *Fundamentals of Acoustics*, 4th ed. Wiley, 2000.
