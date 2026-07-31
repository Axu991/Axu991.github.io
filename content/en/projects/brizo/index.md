---
title: "Bionic Robotic Fish Platform — Brizo"
date: 2026-05-11
description: "Brizo is an agile bionic robotic fish platform designed for research and environmental monitoring. Features: 0.5m length, 0.5m/s cruise speed, and 1-hour battery life."
cover:
    image: "cover.jpg"
    alt: "Project Cover"
    caption: "CAD Model and Physical Prototype"
    relative: true
    hidden: false
tags: ["Bionic Robotics", "Embedded Systems", "Motion Control"]
categories: ["Projects"]
editPost:
    URL: "https://github.com/Axu991/robotic-fish"
    Text: "GitHub: Brizo"
---

## Project Introduction

Inspired by the streamlined body and efficient swimming patterns of the barracuda, I designed and developed the **Brizo** bionic robotic fish platform. I independently managed the entire development cycle, from conceptual sketching to a fully functional prototype. Measuring 0.5 meters in length, this platform integrates mechanical structures, embedded hardware, and advanced motion control algorithms, providing a comprehensive experimental platform for validating bionic propulsion and autonomous movement.

- **Mechanical & Modeling**: Designed the multi-joint bionic structure and streamlined hull using **SolidWorks**; constructed **URDF** models for simulation alignment. Utilized static sealing technology to achieve **IP68** waterproofing, ensuring stable operation of internal systems under dynamic loads for ≥1 hour.

- **Embedded & Hardware Development**: Built around the **ESP32-S3**, I completed the schematic and PCB designs, including power distribution and actuator control circuits. Powered by an 8.4V LiPo battery with DC-DC regulation. Implemented a **CAN (TWAI)** network for unified servo control and integrated an **IMU** for real-time attitude feedback. Designed a dual-link system with **915MHz (CRSF)** for long-range control and **WiFi (AsyncUDP)** for real-time debugging, achieving stable 50Hz motion control.

- **Motion Control Algorithms**: Developed a multi-joint coordinated propulsion algorithm based on **Central Pattern Generators (CPG)**, optimizing efficiency through parameter identification to reach a cruise speed of 0.5 m/s. Conducted **Deep Reinforcement Learning (DRL)** training within a **CFD** (Computational Fluid Dynamics) simulator and validated **Sim2Real** transfer methods for deploying simulation-trained policies onto the physical robot.

> **Technical Stack**

**Mechanical** : `SolidWorks` / `URDF` / `IP68 Waterproofing` / `3D Printing` 

**Hardware** : `ESP32-S3` / `PCB Design (LCEDA)` / `CAN (TWAI)`  

**Algorithms** : `CPG Algorithm` / `CFD Fluid Simulation` / `Deep Reinforcement Learning` / `Sim2Real` 

---

## Video Demonstrations

The following videos demonstrate Brizo's performance in both outdoor and laboratory environments, showcasing its mobility and interaction capabilities.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_outside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Outdoor Field Test</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_inside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Laboratory Environment</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_interact.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Interaction with Fish Shoal</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_cross.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Maneuverability Showcase</p>
  </div>
</div>

---

## Core Features

- **Multi-joint Bionic Propulsion**: Implements bio-mimetic undulation via the CPG algorithm. Reaches a cruise speed of 0.5 m/s, offering superior maneuverability and stealth compared to traditional propellers.
- **Dual-Link Communication**: Parallel 915MHz long-range control and WiFi debugging links support both outdoor deployment and real-time indoor parameter tuning.
- **IP68 Rated Sealing**: Static sealing combined with modular hull design ensures reliable underwater operation for over 1 hour.
- **Sim2Real Transfer**: Successfully bridges the gap between CFD simulation environments and physical hardware for Reinforcement Learning policy deployment.