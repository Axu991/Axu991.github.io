---
title: "Underwater Robot Hull Form Survey: A Decade of Progress"
date: 2026-10-28
description: "A technical overview of underwater vehicle hull form design — drag components, slenderness ratio, profile shapes, surface treatment, thruster layout, and the design trade-offs between torpedo-shaped AUVs and open-frame ROVs."
tags: ["Underwater Robotics", "Hydrodynamics", "AUV", "ROV", "Hull Design", "Biomimetics"]
categories: ["Engineering Notes"]
---

## Why hull form matters

![HUGIN Superior, a commercial deep-water torpedo-shaped AUV (Kongsberg)](/images/blog/hull-form/hugin-superior.jpg)

*Figure 1. HUGIN Superior, a 6.6 m torpedo-shaped AUV with L/D ≈ 7.5. Note the smooth revolve body, hemispherical nose, tapered stern, and flush-mounted sensor windows. Image: Kongsberg.*

An underwater vehicle's resistance has three components:

- **Skin friction drag** — proportional to wetted surface area and boundary layer state.
- **Form drag / pressure drag** — depends on whether the shape avoids flow separation.
- **Wave-making drag** — only significant near the surface.

For an AUV cruising at 2–5 kn, form drag and friction drag are comparable. For an ROV moving below 2 kn with lots of appendages, the absolute drag is small, but the thruster power budget is tight enough that every streamlining decision still matters.

A streamlined hull directly affects three performance numbers:

1. **Endurance** — lower drag means more range per battery. MBARI Tethys reshaped its tail to delay separation and stretched AUV endurance from days to weeks; Slocum Sentinel Redwing plans 15,000 km single-segment crossings in 2025.
2. **Speed** — VideoRay Pro 5, with a compact torpedo hull and rear main thruster, reaches >4.4 kn in its class, far above multi-vectored-rotor ROVs (~2 kn).
3. **Noise** — a smooth hull with controlled separation lowers self-noise, which matters for survey AUVs carrying multibeam sonar and magnetometers. Kongsberg HUGIN Superior explicitly markets itself as a "very stable, low-noise hydrodynamic platform."

## Engineering metrics of a "streamlined" hull

| Metric | Definition | Typical values |
|---|---|---|
| Slenderness L/D | Overall length / max diameter | Biological optimum 4–6; commercial AUV 8–12; multi-rotor ROV not applicable |
| Profile shape | Axial longitudinal section | Myring body, NACA 66-67 laminar profile, ellipsoid |
| Max diameter location | Max diameter aft of nose / total length | Biology 35–45%; engineered AUV ~40–50% |
| Parallel mid-body fraction | Parallel mid-section length / total length | ≤30%, otherwise form drag rises sharply |
| Appendages / openings | Number of protrusions, sensor fairings | Flush-mount, filleted edges is standard |
| Surface treatment | Fairing smoothness, riblets, compliant coating | Smooth fairing baseline; riblets optional (+7–10% drag reduction) |
| Strouhal number | St = f · A / U (tailbeat · amplitude / speed) | 0.2–0.4 for all efficient swimmers |

## Five design routes

The 2015–2025 product landscape splits into five distinct philosophies:

| Category | Typical shape | L/D | Speed | Design priority |
|---|---|---|---|---|
| True streamlined AUV | Torpedo revolve body (Bluefin, REMUS, HUGIN) | 7–13 | 3–5 kn | Low drag, low noise, long endurance |
| Underwater glider | Slender cylinder + wings (Slocum, Seaglider) | 6–8 | 0.3–1 kn | Ultra-low power, month-long missions |
| Streamlined ROV (consumer) | Ellipsoid / rounded box + multi-thruster (FIFISH, Chasing) | L/W 1.1–2.0 | 1.5–4 kn | 6-DOF hover, tool carrying |
| Industrial transitional ROV | Compact torpedo short hull (VideoRay) or cylinder + frame (Boxfish) | — | >4 kn | High-speed inspection, modular payload |
| Bio-inspired soft robot | Tuna / dolphin / manta / boxfish shape (MIT, Festo, SJTU, ZJU) | Biological 3–6 | 0.5–5 kn | High maneuverability, low disturbance, biological efficiency |

The two most visually different philosophies sit at opposite ends of this table. A torpedo-shaped AUV (Bluefin-12S below left) is a smooth revolve body with no protrusions at all. An open-frame ROV (BlueROV2 below right) is an aluminum frame with every thruster, sensor, and battery exposed.

![Two design philosophies: Bluefin-12S torpedo AUV vs BlueROV2 open-frame ROV](/images/blog/hull-form/bluefin-12s.jpg)
![BlueROV2 open-frame ROV in operation](/images/blog/hull-form/bluerov2.jpg)

*Figure 2 (top). Bluefin-12S: a 3.66 m yellow torpedo revolve body, L/D ≈ 11.3, designed for modular mine-countermeasure and ocean survey. Image: General Dynamics.*

*Figure 3 (bottom). BlueROV2 in the field: an open aluminum frame with six vectored thrusters, two plastic pressure housings, and an exposed camera. This is the reference open-frame platform used by universities and makers worldwide. Image: Blue Robotics.*

## Profile selection

The profile choice follows mission:

- **Long-endurance torpedo AUV / glider:** Myring body (analytic fore-and-aft curves) or NACA 66/67 laminar profile revolve. Max diameter at 35–45% of length, parallel mid-body ≤30%.
- **Bio-inspired BCF robot fish:** NACA 63-015A / 67-021 profile-like body, max thickness at ~20% length, tail "necking" into caudal fin with reinforcing keels.
- **Bio-inspired MPF robot (manta, penguin):** Low-camber wing-like pectoral fins; body flattened or rounded. Key is lift efficiency of the fins, not the body.
- **Boxfish-style payload hull:** Do not force water streamlining; use fins / vectored thrusters to actively compensate for inherent instability.

## Slenderness ratio: biology vs engineering

- **Optimal low-drag range:** L/D ≈ 4–6. Penguins 3–4, tuna 4–5.6, dolphins 4–5. Literature puts L/D = 5 as the point of minimum drag with parallel section ≤30%.
- **Commercial AUV range:** L/D ≈ 8–12 (longer than biology because payload and batteries demand internal volume). Bluefin-12S reaches 11.3, REMUS 600 10–13, HUGIN Endurance 10.9.
- **Multi-rotor ROVs:** L/D is not constrained; L/W ≈ 1.1–2.0 suffices because hover and tool carrying dominate.

Engineering L/D trades biological optimum (4–6) for internal volume. This is the "battery and payload constraint" overriding the hydrodynamic optimum.

## Surface treatment

| Method | Drag reduction | Maturity | Recommendation |
|---|---|---|---|
| Smooth fairing + flush-mount sensors + filleted openings | Baseline, removes protrusion drag | Must-do | All sensor ports, bolts, hatches flush |
| Shark-skin riblet film | 7–10% | Available off-the-shelf | Apply along flow direction, tune spacing to Re |
| Dolphin compliant coating | Delays transition to Re ~2×10⁷ | Not mature (compliance matching unresolved) | Watch literature |
| Tuna mucus / smooth lubricant coating | ~15% cruise-speed gain in biology | Process simple, gain small | Optional, not critical path |

## Tail and propulsion matching

| Tail type | Use case | Efficiency |
|---|---|---|
| High-aspect-ratio lunate tail (tuna, marlin, mako) | High-speed cruise | Lift-based thrust, low induced drag |
| Fork / square tail (barracuda, boxfish) | High maneuver, acceleration | Drag-based, agile but inefficient |
| MPF pectoral oscillation (manta) | Low-speed long-endurance gliding | High lift efficiency, can turn in place |
| Undulating long-based fin (cutlassfish, BionicFinWave) | Pipeline / fine intervention | Low disturbance, full maneuverability |
| Single propeller on revolve body (AUV standard) | Medium-high speed cruise | Axisymmetric, requires stern taper |

All efficient biological swimmers share a Strouhal window **St = 0.2–0.4**. The mechanical design should land in this band.

## Appendage penalty: three-layer compensation

Consumer ROVs represent a compromise: a rounded ellipsoid shell that streamlines the body but leaves six vectored thrusters protruding. FIFISH V6 below is the canonical example — a fluorescent-yellow boll-shell with corner thrusters, designed for 6-DOF hover rather than minimum drag.

![FIFISH V6, a consumer ROV with a streamlined boll-shell and six vectored thrusters](/images/blog/hull-form/fifish-v6.jpg)

*Figure 4. FIFISH V6 (QYSEA): a 383×331×143 mm boll-shell ROV with four corner and two side vectored thrusters. The rounded shell reduces drag compared with a square frame, but the thruster barrels remain protrusions. Image: QYSEA / Underwater Australasia.*

Payload openings, sensor windows, and thruster barrels inevitably break the clean hull. Engineering compensates in three layers:

1. **Flush-mount** — every sensor window, bolt, and hatch sits coplanar with the shell. Gliders even put GPS/Iridium antennas inside the wing to eliminate external dome drag.
2. **Fillet all openings** — avoid sharp corners that trigger separation.
3. **Drag budget decomposition** — use towing tank / CFD to identify each drag source and fix them one by one. REMUS is the textbook case: successive towing-tank tests identified sources, and sealed-propeller thrusters were the final improvement.

## What the last decade changed

Four methodological generations:

| Era | Years | Methods | Mark |
|---|---|---|---|
| Empirical revolve body | Pre-2015 | Myring (1976) / Hoerner formulas, hand-picked NACA profiles | L/D 5–7 by rule of thumb |
| CFD era | 2015–2018 | RANS (k–ε / k–ω SST), 2D/3D unstructured mesh, towing-tank validation | Parametric profiles, still hand-tuned |
| Optimization era | 2019–2022 | Bezier / Myring parametrization + GA / Bayesian / NSGA-II + surrogate model; GAN-generated hulls from 2022 | Multi-objective (drag + volume + stability) auto-searched |
| Digital twin | 2023–2025 | CFD (LES/DNS explaining transition), pool + sea trials, DRL trained in simulation then deployed | Sim-to-real closed loop, morphology self-adapts |

Hardware trends:

- **3D printing (FDM/SLS):** PLA/PA12 outer skins shrink complex hull iterations from months to days. Harbin Engineering University printed an 85 cm AUV hull for under ¥10,000.
- **Composite monocoque:** Gliders (e.g. SeaWing 7000 m) use carbon/epoxy shells that hold 6000 m pressure while staying hydrodynamically smooth.
- **Bio-inspired soft bodies:** Tunabot Flex (2021) showed body flexibility cuts cost of transport 53% over rigid multi-joint fish; Festo BionicFinWave (2020) commercialized undulating fin propulsion.

## Reference products by mission

- **Long endurance / high speed:** Bluefin-9 (L/D 6.9), REMUS 100 (L/D 8.4), MBARI Tethys (L/D 7.7, week-long missions).
- **Near-bottom / high maneuver:** VideoRay Pro 5 (>4.4 kn), Deep Trekker Revolution (rounded ellipsoid carbon hull), Boxfish Luna (1000 m cinematic ROV).
- **Bio-inspired agile:** Tunabot Flex, Festo Airacuda, SJTU robotic dolphin, HEU eel-like soft robot.

## Further reading

- [Myring body equations](https://www.ing.iac.es/~docs/external/raydata/raydata_html/chapter10s.html) — the analytic revolve-body parametric form.
- [Bluefin Robotics / General Dynamics AUVs](https://gdmissionsystems.com/products/underwater-vehicles/bluefin-robotics) — product line.
- [Kongsberg HUGIN AUV series](https://www.km.kongsberg.com/ks/web/nokbg0238.nsf/AllWeb/6E5A3D2E4F7E4F13C125821200493C99) — commercial deep-water standard.
- [Slocum Glider, Teledyne Webb](https://www.webb.whoi.edu/products/slocum-glider/) — long-endurance platform.
- [Tunabot Flex, Science Robotics 2021](https://www.science.org/doi/10.1126/scirobotics.abf3425) — soft-body fish efficiency study.
- [Festo BionicFinWave](https://www.festo.com/cms/en-corp/15222.htm) — undulating fin ROV.
- [Blue Robotics BlueROV2](https://www.bluerobotics.com/store/rov/bluerov2/) — the open-frame reference platform.
