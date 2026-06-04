---
title: "Agile and Controllable Omnidirectional Fast-start Maneuvers of Robotic Fish via Bio-inspired Reinforcement Learning"
date: 2024-09-01
# weight: 1
description: "This project introduces BASS, an innovative data-driven control framework that breaks free from the periodic constraints of traditional CPGs. By coupling a high-fidelity CFD environment with a biological two-stage reward mechanism, we achieve non-periodic, high-burst omnidirectional fast-start maneuvers on a multi-joint robotic fish, successfully bridging the Sim2Real gap in transient fluid dynamics."
cover:
    image: "cover.jpg"
    alt: "ICRA 2026 Paper Poster and Core Architecture"
    caption: "Deep Reinforcement Learning (DRL) control architecture embedded with biological two-stage characteristics"
    relative: true
    hidden: false
tags: ["Biomimetic Robotic Fish", "Deep Reinforcement Learning", "ICRA 2026"]
categories: ["Projects", "Publications"]
editPost:
    URL: "https://github.com/Axu991"
    Text: "GitHub Project"
---

## Project Overview

In nature, fish employ high-acceleration, explosive **fast-start** maneuvers—typically executing a primary body bend within several milliseconds and an expansive release within tens of milliseconds—to escape predators or strike prey. However, conventional control paradigms struggle to reproduce these high-burst, non-periodic, and omnidirectional agile trajectories due to the rigid phase constraints of synchronized gaits.

To address this challenge, this study introduces **BASS (Bio-inspired Agile Start-up Strategy)**, a novel omnidirectional fast-start control framework governed by deep reinforcement learning. By directly mapping joint actuation within a data-driven action space, the framework harnesses highly nonlinear fluid-structure interaction forces. This method enables the robot to execute explosive transient turns and forward bursts across a comprehensive range of target headings, effectively bridging the Sim2Real transfer gap inherent in unsteady, transient hydrodynamic environments.

- **Algorithm & Architecture Design**: The framework entirely bypasses traditional gait generators, such as Central Pattern Generators (CPGs), by mapping the action space directly to the target positions of the driving joints. This unconstrained, non-periodic formulation grants the policy maximum autonomy to explore and synthesize highly dynamic, agile gaits within non-linear fluid domains.

- **Bio-inspired Reward Mechanism**: Integrated with a high-fidelity **CFD fluid solver**, the complex physical process of a fast-start is explicitly partitioned into two distinct biological phases to guide multi-objective reward formulation. Stage I (the bending phase) concentrates on angular velocity adjustments to rapidly minimize heading errors for optimal turning, while Stage II (the stretching phase) focuses on linear velocity along the target vector to unlock stored potential energy for explosive acceleration.

- **Policy Deployment & Generalization**: The framework establishes a high-dimensional state space incorporating robot kinematics, real-time joint feedback, and phase markers. By incorporating domain randomization schedules across initial poses, orientations, and target headings during training, the network effectively accommodates modeling discrepancies in transient fluid-structure interactions, ultimately enabling zero-shot policy migration to physical aquatic environments.

> **Technical Stack**

**Algorithm Framework** : `Deep Reinforcement Learning (DRL)` / `BASS Policy` / `Markov Decision Process (MDP)` / `PyTorch`

**Fluid Simulation** : `CFD Fluid Solver` / `Unsteady Hydrodynamics` / `Fluid-Structure Interaction (FSI)` / `Vortex Visualization`

**Policy Transfer** : `Bio-inspired Reward Design` / `Domain Randomization` / `Sim2Real Migration`

---

## Video Demonstration

These videos demonstrate the comprehensive academic findings of this study alongside physical deployment clips validating explosive fast-start capabilities on a multi-joint robotic fish platform.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff; display: flex; flex-direction: column;">
    <div style="position: relative; width: 100%; padding-bottom: 56.25%; height: 0; overflow: hidden;">
      <video style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover;" controls>
        <source src="ICRA_video_1080P.mp4" type="video/mp4">
      </video>
    </div>
    <div style="padding: 12px; text-align: center; flex-grow: 1; display: flex; align-items: center; justify-content: center;">
      <p style="font-size: 14px; color: #666; margin: 0; line-height: 1.4;">ICRA 2026 Summary Video</p>
    </div>
  </div>

  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff; display: flex; flex-direction: column;">
    <div style="position: relative; width: 100%; padding-bottom: 56.25%; height: 0; overflow: hidden;">
      <video style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover;" controls>
        <source src="faststarts.mp4" type="video/mp4">
      </video>
    </div>
    <div style="padding: 12px; text-align: center; flex-grow: 1; display: flex; align-items: center; justify-content: center;">
      <p style="font-size: 14px; color: #666; margin: 0; line-height: 1.4;">Physical Fast-Start Validation on Multi-Joint Platform</p>
    </div>
  </div>
</div>

---

## Core Capabilities

- **Non-Periodic Omnidirectional Burst Control**: By specifying the action space directly as the desired target positions of each articulating joint, the algorithm liberates itself from the rigid cyclical constraints of standard locomotion. This configuration yields a highly flexible policy capable of discovering non-periodic, transient maneuvers, granting the robot exceptional agility across broad target heading angles.

- **Bio-inspired Two-Stage Optimization**: The network leverages a coupled, phase-specific multi-objective reward structure. During the initial bending phase (Stage I), the policy prioritizes angular velocity responses to minimize heading deviations, optimizing C-shape body deformations. In the subsequent stretching phase (Stage II), the objective shifts to maximizing linear velocity along the target path, channeling fluid momentum into explosive forward propulsion.

- **High-Fidelity Sim2Real Migration**: High-dimensional physical state vectors—capturing robot kinematics, joint feedback, and transient phase indicators—serve as the foundation for the reinforcement learning observation space. Evaluated at a fixed closed-loop control frequency, the framework leverages advanced domain randomization to mitigate fluid-structure modeling errors induced by complex, unsteady wake vortices and mechanical backlashes, achieving robust zero-shot physical deployment.

- **Optimized Fluidic Wake Structure**: High-fidelity CFD visualizations confirm that under the BASS framework, the robotic platform generates a prominent, non-linear attached vortex during the initial body-bend phase. Upon extension, these structures cleanly shed to form a structured reverse Kármán vortex street, yielding instantaneous energy transfer efficiencies and axial displacements that significantly outperform baseline periodic gaits with fixed phase differences.

---

## Conclusion & Future Work

1. **Feasibility of Non-Periodic Agile Maneuvers**: This work successfully demonstrates that deep reinforcement learning (DRL) architectures can autonomously synthesize transient, non-periodic escape responses and adaptive fast-start profiles for multi-joint robotic fish operating in unsteady fluid environments.
2. **Efficacy of Bio-inspired Reward Formulations**: Experimental results confirm that embedding biological two-stage (bend-and-stretch) mechanics into the Markov Decision Process (MDP) reward topology produces superior directional targeting accuracy and greater forward propulsion displacement compared to structured gait baselines.
3. **Future Research Directions**:
   * Enhancing policy robustness and online adaptation under severe fluidic disturbances, including unstructured environmental currents, unsteady turbulence, and wild field conditions.
   * Investigating the co-design of advanced flexible structural mechanics, compliant smart materials, and rigid multi-joint architectures to further expand peak mechanical energy transfer during transient agile maneuvers.