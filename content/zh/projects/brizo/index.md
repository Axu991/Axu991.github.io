---
title: "仿生机器鱼平台—梭影（Brizo）"
date: 2026-05-11
description: "Brizo 是一个具备敏捷运动能力的仿生机器鱼平台，适用于科研和环境监测等多种应用场景，身长0.5米，可用2.4GHz无线控制和915MHz无线通信，巡航速度为0.5米/秒，常态运动续航时间约1小时。"
cover:
    image: "cover.jpg"
    alt: "项目封面图"
    caption: "模型与实物展示"
    relative: true
    hidden: false
tags: ["仿生机器鱼", "嵌入式系统", "运动控制"]
categories: ["Projects"]
editPost:
    URL: "https://github.com/Axu991"
    Text: "GitHub：Brizo"
---

## 项目简介

受自然界梭鱼的流线型体态与高效游动方式启发，我设计并制作了仿生机器鱼平台 **Brizo**。从概念草图到可运行原型，我独立完成了全过程开发。该平台长 0.5 米，集机械结构、嵌入式硬件与运动控制算法，为仿生推进与自主运动能力验证提供了完整的科研实验平台。

- **机械与建模**：基于 **SolidWorks** 完成多关节仿生结构及流线型外形设计，构建 **URDF** 模型用于仿真对齐；采用静密封技术实现整机 **IP68** 级防水，支持舱内系统在动态负载下连续稳定运行 ≥1 小时。

- **嵌入式与硬件开发**：以 **ESP32-S3** 为主控，完成原理图与 PCB 设计，构建电源分配、通信接口及执行器控制电路。系统采用 8.4V 航模锂电池供电，结合 DC-DC 稳压模块形成稳定供电链路。基于 **CAN（TWAI）** 通信网络实现多路 CAN 舵机统一控制，集成 **IMU** 获取实时状态。设计 **915MHz（CRSF）**远程控制链路与 **WiFi（AsyncUDP）**调试链路，实现控制与调试解耦及多链路并行通信。在系统集成中完成接口匹配、通信协同与任务调度，实现 50Hz 稳定运动控制，系统可在实验室与户外水域环境下切换可靠运行。

- **运动控制算法**：设计基于中央模式发生器（**CPG**）的多关节协同推进算法，通过参数辨识优化推进效率，使巡航速度达到 0.5 m/s，并完成算法的离线部署。结合 CFD 流体仿真器，开展深度强化学习控制策略训练，以及仿真到实机的迁移方法（**Sim2Real**）验证。

> **技术栈** 

**机械结构** : `SolidWorks` / `URDF` / `防水设计` / `3D打印` 

**硬件开发** : `ESP32-S3` / `PCB设计(嘉立创EDA)` / `CAN(TWAI)` 

**算法研究** : `CPG算法` / `CFD流体仿真` / `深度强化学习` / `Sim2Real` 

---

## 视频演示

分别展示了 Brizo 在户外环境和实验环境中的表现，可以更直观地了解 Brizo 的运动能力和交互体验。

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 20px 0;">
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_outside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo户外环境演示</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_inside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo实验环境演示</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_interact.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo与鱼群</p>
  </div>
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls>
      <source src="demo_cross.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo交叉演示</p>
  </div>
</div>

---

## 核心功能

- **多关节仿生推进**：基于CPG算法实现类鱼体波动，巡航速度达0.5 m/s，机动性与隐蔽性优于传统螺旋桨推进。

- **通信链路**：915MHz远程控制与WiFi调试链路并行，支持户外远程操控与室内实时调参，以50Hz频率运动控制。

- **IP68级防水**：静密封技术配合模块化舱体设计，连续运行≥1小时。

- **Sim2Real算法迁移**：基于CFD仿真环境训练深度强化学习策略，实现仿真到实机的强化学习策略部署。