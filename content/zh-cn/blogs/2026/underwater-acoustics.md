---
title: "无线缆水下机器人的声学通信与定位技术"
date: 2026-10-27
description: "面向无线缆 ROV/AUV 系统的水声信道、通信波形、定位体制、换能器基础与代表性研究综述。"
tags: ["水下机器人", "声学", "USBL", "piUSBL", "AUV"]
categories: ["调研笔记"]
---

## 为什么采用声学通信？

电磁波（2.4 GHz Wi-Fi/蓝牙）在海水中穿透深度仅数厘米。光波/激光在清水中可达数十米、速率 Mbit/s 量级，但需要精确对准视距链路，且浑浊水失效。声波则能在海水中传播公里级距离、不需要视距（可绕射反射）、硬件简单。一旦水下机器人脱离线缆，控制、数据、定位都必须走声学信道——这是物理决定的，不是工程选择。

## 水声信道

### 声速与测距

海水中声速约 1500 m/s（随温度、盐度、深度在 1450–1550 m/s 间变化）。测距原理很简单：距离 = 声速 × 传播时间。声波往返 1 毫秒对应约 1.5 米。

### 吸收与传播损失

声波衰减由两部分组成：

- **扩展损失**：球面扩散，距离每翻一倍损失 6 dB。
- **吸收损失**：海水粘滞吸收，随频率急剧上升。

| 频率 | 吸收系数 α | 10 m 距离损失 |
|---|---|---|
| 10 kHz | ~3 dB/km | 可忽略 |
| 30 kHz | 10–20 dB/km | 0.2 dB |
| 100 kHz | ~100 dB/km | 1 dB |
| 200 kHz | ~300 dB/km | 3 dB |

这就是距离-带宽折中：高频段带宽大但传不远；低频段走得远但速率低。

### 多径与多普勒

声波经海面和海底反射后多路到达接收端。浅水（2–5 m 水深）场景下多径时延扩展可达 7 ms，造成码间串扰。这也是高速水声通信采用 OFDM 的原因——把高速率拆成很多并行窄带子载波，每个子载波不受多径影响。

平台相对运动产生多普勒频移：1 m/s 相对速度、30 kHz 载频下频移约 20 Hz，相干系统必须进行估计与补偿。

### 声呐方程

所有声呐系统（通信 Modem、测距声呐、定位阵列）都满足同一个能量平衡：

**SL − 2TL + TS − (NL − DI) = DT**

其中 SL 为声源级，TL 为传播损失，TS 为目标反射强度（通信链路中即接收换能器灵敏度），NL 为环境噪声，DI 为阵列指向性增益，DT 为检测阈值。要增大作用距离，要么提高发射功率（SL），要么用指向性阵列（DI），要么用更好的编码降低检测阈值（DT）。

## 通信波形

| 调制方式 | 原理 | 速率 | 抗多径 | 复杂度 |
|---|---|---|---|---|
| BFSK | 两个固定频率代表 0/1 | 20–1000 bit/s | 弱 | 极低 |
| FH-FSK | 跳频扩频 | 0.5–5 kbit/s | 中 | 低 |
| PSK | 相位调制 | 5–20 kbit/s | 中 | 中 |
| S2C 扩频 | 线性调频脉冲 | 7–62 kbit/s | 强 | 中 |
| OFDM | 多载波并行 | 15–60 kbit/s | 很强 | 高（DSP/FPGA） |

物理极限：公里级距离 ≈ kbit/s，百米级 ≈ 几十 kbit/s。即使 60 kbit/s 也只能在 300 m 距离上传 CIF 分辨率视频。无线高清视频在物理上做不到，所有无线水下机器人都是本地存储、出水后导出。

## 定位体制

所有水声定位均基于传播时间测量。四种主要体制：

| 体制 | 原理 | 精度 | 成本 | 适用场景 |
|---|---|---|---|---|
| USBL（船载） | 船底阵列测斜距+方位 | 斜距 0.1–1% | $10k–100k | 船载 ROV 跟踪 |
| piUSBL（机载） | 机器人阵列被动接收信标 | 斜距 0.1%、方位 0.1–1° | <¥1k（开源） | 潜水员跟随、AUV 导航 |
| LBL（海底基阵） | 海底 3+ 应答器多边交会 | 亚米级（不随距离衰减） | $50k+ | 固定海域高精度测绘 |
| DVL（多普勒计程仪） | 多普勒测速积分推算 | 航位推算（会漂移） | $5k+ | 与 USBL/LBL 组合使用 |

### piUSBL 工作原理

在倒置配置下，机器人本体携带一组小型水听器，潜水员佩戴一个周期性发射短脉冲的声学信标。机器人测量脉冲到达各水听器的**到达时间差（TDOA）**，解算信标相对自身的方位角，再结合机器人自带的压力传感器测得深度，得到目标相对位置。

由于机器人只接收、不发射，功耗极低，也不会暴露自身位置。

两篇奠基论文确立了这一技术路线：

- **Rypkema 等（MIT/IROS 2019）** 在受控实验中验证了 piUSBL 精度：斜距精度约 0.1%（100 m 距离误差 0.1 m），方位精度约 1°，工作距离达数百米，采用 5 水听器金字塔形阵列。该系统为广播式——一个信标可同时定位 N 个机器人。
- **王应强等（浙江大学，IEEE RA-L 2022）** 在碟形 AUV 上完成了 piUSBL 全栈工程实现，并在南海完成海试验证。这是开源项目 Raspi2USBL 的直接技术前身。

2025 年开源项目 [Raspi2USBL](https://arxiv.org/abs/2511.06998) 在树莓派上用 4 个水听器复现了这套架构，跟踪距离 1.3 km，BOM 不到 ¥1000。

### 参考案例：iBubble

商用跟随无人机 iBubble 采用的正是这套架构：4 水听器 piUSBL + 压力传感器融合，跟随距离 1.5–6 m（极限 25 m），最大深度 60 m，续航 90 分钟，重 9 kg，失联 90 秒自动上浮。

## 换能器基础

换能器是声学系统的核心硬件——负责电声可逆转换（发射时电转声，接收时声转电）：

- **水听器**：接收声波，把声压转换成电压。
- **发射换能器**：把电信号功率放大后辐射成声波。
- **压电陶瓷**：最常用的换能材料，利用压电效应可逆转换。
- **指向性指数**：波束集中程度。窄波束增益高但对不准；宽波束覆盖广但灵敏度低。
- **工作频率**：决定换能器体积与作用距离——高频换能器小但走不远。

## 避障声呐

近程障碍探测（0.5–6 m）是所有无线平台的安全标配：

| 产品 | 类型 | 量程 | 精度 |
|---|---|---|---|
| FIFISH Sonar Array | 单波束测距 | 0.5–6 m | ±5 cm |
| Chasing Distance Lock | 四方向测距 | 数 m | ±25 mm |
| Chasing Omniscan 450FS | 前视成像 | 数十 m | 成像级 |
| Ping 360（BlueRobotics） | 360° 扫描 | 0.75–50 m | 1.6 mm@2 m |
| iBubble 避障声呐 | 近程测距 | 1–1.5 m | — |

## 产品全景

| 平台 | 类型 | 定位方式 | 深度 | 价格 |
|---|---|---|---|---|
| SoFi（MIT） | 科研原型 | BFSK 遥控 | 18 m | 研究项目 |
| iBubble evo | 跟随无人机 | piUSBL | 60 m | ~$2999 |
| FIFISH V-EVO | 缆控 ROV | AI 视觉锁定 | 100 m | $1399 |
| FIFISH V6 Plus | 缆控 ROV | 双声呐+USBL | 100 m | ~$2000 |
| Hydrus | 自主 ROV | DVL+USBL+INS | 300 m | 专业级 |
| Slocum 滑翔机 | 科研平台 | 航位推算+卫星 | 1000 m | 科考级 |
| Raspi2USBL | 开源项目 | piUSBL | 实验级 | <¥1k BOM |

一个值得注意的趋势：消费级跟随平台（如 FIFISH V-EVO）已经转向 AI 视觉锁定而非纯声学跟随，说明纯声学跟随在消费价位尚未成为主流。

## 领域基石论文

理解这些论文就能掌握水声领域的发展脉络：

| 论文 | 年份 | 核心贡献 |
|---|---|---|
| Wenz, *Acoustic ambient noise in the ocean* | 1962 | 海洋环境噪声谱，所有声呐设计的噪声基准 |
| Kilfoyle & Baggeroer, *Underwater acoustic telemetry state of the art* | 2000 | 水声遥测领域综述 |
| Stojanovic, *High-speed underwater acoustic communications* | 1996 | 高速水声通信奠基 |
| Stojanovic, *Phase coherent digital communications* | 1994 | 现代相位相干 Modem 的技术源头 |
| Reis et al., *USBL System Design and Validation* | 2016 | 入门 USBL 设计的必读实验论文 |
| Rypkema et al., *Passive Inverted USBL* | 2019 | piUSBL 精度实验验证 |
| Wang et al., *piUSBL for Disc-shaped AUV* | 2022 | piUSBL 工程化海试 |
| Raspi2USBL | 2025 | 树莓派开源 piUSBL |

## 参考资料

1. Katzschmann, R. K. et al. [*SoFi: Robotic Soft Fish for Untethered Observation.*](https://www.science.org/doi/10.1126/scirobotics.aao6622) Science Robotics, 2018.
2. [EvoLogics S2C Modem 产品手册](https://www.evologics.de/en/products/acoustics/index.html).
3. Gallimore, C. et al. [*The WHOI Micro-Modem.*](https://www.whoi.edu/what-we-do/explore/instruments/instruments-sensors-and-sampling-operations/micro-modem/) OCEANS, 2010.
4. Sendra, S. et al. [*Underwater Acoustic Modems: a Review.*](https://www.mdpi.com/1424-8220/16/11/1821) Sensors, 2016.
5. Rypkema, N. R. et al. *Passive Inverted Ultra-Short Baseline (piUSBL) Localization: An Experimental Evaluation of Accuracy.* IEEE/RSJ IROS, 2019.
6. Wang, Y. et al. *Passive Inverted Ultra-Short Baseline Positioning for a Disc-shaped AUV: Design and Field Experiments.* IEEE RA-L, 2022.
7. [Raspi2USBL: 开源 piUSBL.](https://arxiv.org/abs/2511.06998) arXiv:2511.06998, 2025.
