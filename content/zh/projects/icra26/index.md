---
title: "基于生物启发强化学习的仿生机器鱼全向快速启动与敏捷机动控制"
date: 2024-09-01
# weight: 1
description: "本文提出了一种创新的数据驱动控制框架，打破了传统 CPG 的周期性束缚。通过结合高保真 CFD 仿真与生物学两阶段奖励机制，在自研机器鱼 Brizo 上实现了非周期、高爆发的全向快速启动机动，成功跨越瞬态流体动力学系统的 Sim2Real 鸿沟。"
cover:
    image: "cover.jpg"
    alt: "ICRA 2026 论文海报及核心架构"
    caption: "ICRA 2026 论文海报及核心架构"
    relative: true
    hidden: false
tags: ["仿生机器鱼", "深度强化学习", "ICRA 2026"]
categories: ["Projects， Publications"]
editPost:
    URL: "https://github.com/Axu991"
    Text: "GitHub：Brizo"
---

## 项目简介

自然界中的鱼类在逃避捕食或捕食猎物时，经常使用高加速度、爆发性的**快速启动（Fast-starts）**机动（通常在几毫秒内完成第一阶段弯曲，在几十毫秒内完成第二阶段释放）。然而，现有的传统控制方法难以重现这种高爆发且非周期性的全向敏捷动作。

针对这一痛点，本研究提出了一种全新的**基于生物启发强化学习的全向快速启动控制策略（BASS: Bio-inspired Agile Start-up Strategy）**。该策略直接在动作空间中映射多关节驱动，产生高度非线性的流固耦合推力，在宽航向的全向范围内实现高爆发的瞬态转向与向前加速，成功跨越了瞬态流体系统的 Sim2Real 迁移鸿沟。

- **算法与架构设计**：预先摒弃了依赖传统步态的中央模式发生器（CPG），将动作空间直接定义为驱动关节的期望位置。不设置任何周期性限制，允许算法在非线性流体中充分探索非周期性且高爆发的敏捷控制步态。

- **生物启发奖励机制**：结合高保真度 **CFD 流体仿真器**，将快速启动过程显式划分为生物学两阶段进行多目标奖励函数设计。第一阶段聚焦于角速度驱动以快速消除方向误差（转向优化），第二阶段聚焦于目标方向的线速度以引导爆发推进（加速度优化）。

- **策略部署与泛化**：构建由机器人运动学、关节状态及阶段标志位组成的高维状态特征空间。在训练中引入针对初始位姿、朝向和目标方向的域随机化（Domain Randomization）调度技术，有效克服了非线性流固耦合中的物理建模误差，最终成功实现了控制策略在真实复杂水域中的零样本（Zero-Shot）实机迁移。

> **技术栈** 

**算法框架** : `深度强化学习(DRL)` / `马尔可夫决策过程(MDP)` / `PyTorch`

**流体仿真** : `CFD 流体仿真器` 

**策略迁移** : `生物启发奖励设计` / `域随机化(Domain Randomization)` / `Sim2Real迁移`

---

## 视频演示

分别展示论文视频和在实物Brizo上展现快速启动视频。

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff; display: flex; flex-direction: column;">
    <div style="position: relative; width: 100%; padding-bottom: 56.25%; height: 0; overflow: hidden;">
      <video style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover;" controls>
        <source src="ICRA_video_1080P.mp4" type="video/mp4">
      </video>
    </div>
    <div style="padding: 12px; text-align: center; flex-grow: 1; display: flex; align-items: center; justify-content: center;">
      <p style="font-size: 14px; color: #666; margin: 0; line-height: 1.4;">ICRA 2026 成果总结视频</p>
    </div>
  </div>

  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff; display: flex; flex-direction: column;">
    <div style="position: relative; width: 100%; padding-bottom: 56.25%; height: 0; overflow: hidden;">
      <video style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover;" controls>
        <source src="faststarts.mp4" type="video/mp4">
      </video>
    </div>
    <div style="padding: 12px; text-align: center; flex-grow: 1; display: flex; align-items: center; justify-content: center;">
      <p style="font-size: 14px; color: #666; margin: 0; line-height: 1.4;">多关节仿生机器鱼快速启动实物演示</p>
    </div>
  </div>
</div>

---

## 核心功能

- **非周期全向爆发控制**：控制算法的动作空间直接定义为各驱动关节的期望位置，不设任何周期性约束，从而赋予系统极大自由度来探索和合成非周期的爆发动作，使机器人在大范围的目标朝向下均能展现出优秀的敏捷性。

- **生物启发式两阶段奖励**：构建耦合的阶段特异性多目标函数。在第一阶段（弯曲阶段）聚焦角速度驱动以极速削减方向误差，实现 C 形弯曲优化；在第二阶段（伸展阶段）聚焦目标方向的线速度，引导释放物理势能实现高加速度爆发推进，完成伸展释放优化。

- **高保真 Sim2Real 策略迁移**：构建由机器人运动学、关节状态及阶段标志位组成的高维状态特征空间。在固定的控制周期下，通过随机化调度技术克服了瞬态流体力学中由于复杂尾涡和离散多刚体结构导致的流固耦合建模误差，成功实现了零样本实机部署。

- **流体尾涡优化结构**：借由 CFD 仿真可视化可见，在本策略驱动下，机器人能够产生高度非线性的附着涡，并在释放阶段完美脱落并形成反卡门涡街，能量传递效率与轴向前向加速位移显著优于传统固定相位差的传统控制算法。

---

## 结论与未来

1. **非周期敏捷机动可行性验证**：本工作证明了基于 DRL 的控制架构能够有效合成多关节仿生机器人在非定常流体环境中的非周期、瞬态爆发性逃逸及自适应快速启动行为。
2. **生物启发特征融入的优越性**：实验表明，将经典的生物两阶段（C形弯曲和伸展释放）物理机制融入马尔可夫决策过程（MDP）的奖励设计中，配合高保真 CFD 仿真，能产生显著优于传统步态基线的方向精度与轴向前向加速位移。
3. **下一步研究方向**：
   * 进一步提升策略在带有环境流场强干扰、野外非定常湍流或复杂紊流等复杂水动力学场景下的泛化与自适应调节能力。
   * 探索高级柔性结构力学与材料力学在多关节平台上的协同设计（Co-design），进一步放大爆发机动下的瞬态机械能传递潜力。