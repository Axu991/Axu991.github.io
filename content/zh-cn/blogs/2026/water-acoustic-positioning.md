---
title: "水声定位技术原理：核心方法与系统分类"
date: 2026-11-01
description: "水下航行器如何用声波确定自身位置——d = v × t 的基本原理、TOA/TDOA/AOA/相位差四种方法、LBL/SBL/USBL 系统分类、技术发展历程与真实工程案例。"
tags: ["水声定位", "USBL", "LBL", "TDOA", "水下导航", "水声通信"]
categories: ["技术笔记"]
---

{{< katex >}}

## 基本原理

声波在海水中的传播速度约为 1500 m/s，远低于电磁波（光速 3×10⁸ m/s）。正是因为声速慢，测量时间差来推算距离才变得可行——如果声速和光速一样快，几毫秒的时间差对应几十公里，根本没法测。

核心公式：

$$d = v \times t$$

其中 $d$ 是距离，$v$ 是声速，$t$ 是传播时间。例如传播时间 10 ms，距离 = 0.01 × 1500 = 15 米。水声定位的所有方法，本质上都是在测量时间或相位，然后换算成距离，再通过多点交汇算出位置。

**定位的基本逻辑。** 二维空间中，知道一个距离，目标在一个圆上；知道两个距离，两个圆交汇得到两个点；知道三个距离，三个圆交汇唯一确定位置。三维空间则需要至少四个距离信息。

**声速的影响。** 声速不是固定值，随海水温度、盐度、深度变化，范围约 1450~1550 m/s。高精度定位需要用声速剖面（SSP）校正，低精度测距直接用 1500 m/s 近似即可。

## 主要定位方法

### TOA（到达时间）——测距

TOA 是最基本的测距方法：发射端在 $t_1$ 时刻发脉冲，接收端在 $t_2$ 时刻收到，时间差 $\Delta t = t_2 - t_1$，乘以声速就是距离：

$$d = v \times (t_2 - t_1)$$

原理简单，精度取决于时间测量精度；缺点在于要求收发双方时钟同步，否则时间差不准。实际系统中常通过应答方式解决：接收端收到脉冲后立刻回复一个应答脉冲，发射端测来回总时间，除以 2 再乘声速。应答模式（收发合置）可以规避时钟同步问题，但要求收发双方都能发脉冲。

### TDOA（到达时间差）——双曲定位

TDOA 不需要知道信号发射的绝对时间，只需要测同一个信号到达不同接收器的时间差。两个接收器收到信号的时间差对应一个距离差，距离差相同的点构成一条双曲线：

$$\Delta d = v \times (t_2 - t_1)$$

两个接收器 → 一条双曲线（位置在这条线上）；三个接收器 → 两条双曲线交汇 → 二维定位；四个接收器 → 三维定位。这就是 LBL（长基线）系统的核心原理。核心实现是多通道同步采样 + 互相关算法提取时间差；最大困难是多通道时间同步精度——时间差测量精度要到微秒级，对应距离精度毫米级。水听器间距越大，时间差越大，相对误差越小——这就是为什么叫「长基线」，基线越长精度越高。

### AOA（到达角度）——波束形成

AOA 通过测量信号从哪个方向来定位。用两个或多个水听器组成阵列，信号到达不同水听器有时间差（相位差），通过相位差算出入射角：

$$\sin\theta = \frac{v \times \Delta t}{d}$$

其中 $d$ 是两个水听器间距，$\Delta t$ 是时间差。知道角度和距离，就能算出目标位置。这就是 USBL（超短基线）系统的原理——水听器阵列间距很小，通过相位差测角度。

最大困难是角度误差被距离放大：角度差 1°，距离 10 米时位置偏差 17 厘米；距离 100 米时偏差 1.7 米。所以 USBL 在近距离精度不错，远距离误差很大。

### 相位差法

相位差法测的是载波信号的相位差，而不是脉冲到达时间。相位差和距离成正比：

$$\Delta\varphi = 2\pi \times \frac{d}{\lambda}$$

其中波长 $\lambda = v/f$。精度极高（毫米级）；但相位差是模 $2\pi$ 的，有模糊度——测出来 90°，不知道是走了 0.25 个波长还是 1.25 个波长。实际应用中通常和 TOA 结合：先用 TOA 粗测范围，再用相位差精测。最大困难是相位模糊度：70 kHz 波长约 2.1 cm，走 2.1 cm 相位就转一圈。要测几米远的距离，得知道走了几百个完整波长——这就是「整周模糊度」问题，GNSS 定位里也有同样的难题。

## 系统分类：LBL / SBL / USBL

水声定位系统按基线长度（水听器间距）分为三类：

| 类型 | 基线长度 | 原理 | 精度 | 特点 |
|---|---|---|---|---|
| LBL 长基线 | 几百~几千米 | 多个海底应答器，TDOA 双曲交汇 | 最高（米级） | 精度高但部署麻烦，需海底布放应答器 |
| SBL 短基线 | 几米~几十米 | 船底阵列，TDOA+AOA | 中等 | 部署在船上，需水下应答器配合 |
| USBL 超短基线 | 几厘米~几十厘米 | 紧凑阵列，相位差测角度 | 中等（米级） | 体积小、安装方便，精度受角度误差影响 |

**LBL：** 在海底布放 3~4 个应答器，组成基线阵列。水下航行器测量到每个应答器的距离，用三个距离圆交汇算出位置。精度最高，不受水深影响，但部署和回收麻烦，适合长期固定场地。

**SBL：** 水听器阵列安装在船底，间距几米到几十米。水下有一个应答器，船测到应答器的距离和角度，算出相对位置。比 LBL 灵活，但精度受船姿、水深影响。

**USBL：** 水听器阵列非常紧凑（厘米级），装在船底或 ROV 上。通过相位差测信号到达角度，结合测距得到位置。体积小、安装方便，是目前消费级和小型 ROV 最常用的方案。70 kHz 一发一收，就是 USBL 的基础模块。

## 技术发展历程

| 年代 | 事件 | 意义 |
|---|---|---|
| 1963 | 美国核潜艇 USS Thresher 沉没 | 推动水声定位系统发展，现代水声定位的起点，催生了 SBL 系统 |
| 1965 | Alvin 深潜器启用，配套 ALNAV 声学导航 | 第一个实用化的水下声学导航系统 |
| 1970 | Cestone 发表《Precise Underwater Navigation》 | 系统总结了早期水声导航技术原理 |
| 1972 | Watkins & Schevill 首次发表 TDOA 双曲定位方法 | 奠定了 TDOA 定位的数学基础 |
| 1972 | Davies 发表近底声学导航系统理论对比 | 系统分析了 LBL 类双曲定位系统 |
| 1980s | LBL 系统商业化 | 水声定位从军用走向民用 |
| 2000s | USBL 小型化、低成本方案兴起 | 消费级 ROV 和小型水下设备开始应用 |
| 2020s | Sparse LBL、单信标定位、多传感器融合 | 降低部署成本，走向小型化 |

## 技术实现要点与难点

### 各方法核心对比

| 方法 | 核心实现 | 最大困难 | 精度量级 |
|---|---|---|---|
| TOA 测距 | 脉冲边沿检测、高精度计时 | 时钟同步 | 厘米~分米级 |
| TDOA 定位 | 多通道同步采样、互相关提取时间差 | 多通道时间同步、基线几何精度 | 米级 |
| AOA 测角 | 小间距阵列、相位差测角 | 角度误差随距离放大 | 米级（近距离） |
| 相位差测距 | 连续载波、I/Q 正交解调 | 整周模糊度 | 毫米~厘米级 |

### 三大共性难题

**难题一：声速不均匀。** 海水温度、盐度、深度都会影响声速，范围约 1450~1550 m/s。声速剖面（SSP）随深度变化，声波会弯曲，不是直线传播。高精度定位需要做声线追踪修正，低精度测距直接用 1500 m/s 近似即可。

**难题二：多径效应。** 声波会在海面、海底反射，接收端收到的不只是直达波，还有反射波。反射波和直达波叠加，导致脉冲展宽、边沿模糊，时间测量误差变大。这就是为什么近岸浅水定位比深水难——浅水多径效应严重。

**难题三：带宽与距离的矛盾。** 高频声波（如 70 kHz）方向性好、精度高，但衰减快、距离短；低频声波（如 10 kHz）传得远，但方向性差、精度低。换能器的工作频率一旦选定，测距范围和精度就基本定了。

## 定位体制架构：USBL / piUSBL / LBL / DVL

除了按基线分类，实际平台还会在四种体制之间权衡精度、成本与部署难度：

| 体制 | 原理 | 精度 | 成本 | 适用场景 |
|---|---|---|---|---|
| USBL（船载） | 船上阵列测斜距 + 方位角 | 斜距的 0.1%~1% | 数万~数十万元 | 船载跟踪 ROV |
| piUSBL（机载） | 机器人阵列听信标 | 0.1% 距离、0.1°~1° 方位角 | BOM < 1000 元（开源） | 潜水员跟拍、AUV 导航 |
| LBL（海底） | 3 个以上海底应答器多点交汇 | 亚米级（与距离无关） | 数十万元以上 | 固定场地测量 |
| DVL | 多普勒测速、航位推算 | 数小时内漂移 | 数千元以上 | 与 USBL/LBL 组合 |

### piUSBL 工作原理

在倒置（机载）配置中，机器人携带一个紧凑的水听器阵列，潜水员佩戴一个定期发射短脉冲的小型声学信标。机器人测量每个脉冲到达各水听器的**到达时间差（TDOA）**，解算信标的相对方位角，再结合机载压力传感器得到深度。因为机器人只收不发，功耗极低，也不会暴露自身位置。

两篇里程碑论文奠定了这一方向：

- **Rypkema 等（MIT/IROS 2019）** 通过受控实验验证了 piUSBL 精度：约 0.1% 距离精度（100 米处误差 0.1 米）、约 1° 方位角精度，使用 5 水听器金字塔阵列，工作距离数百米。系统是广播式的——一个信标可同时为 N 台机器人定位。
- **Wang 等（浙江大学，IEEE RA-L 2022）** 在盘形 AUV 上工程化实现了完整 piUSBL 系统，并在南海完成海试——这是开源 Raspi2USBL 项目的直接工程前身。

开源 [Raspi2USBL](https://arxiv.org/abs/2511.06998)（2025）用树莓派 + 4 水听器复现了同一架构，BOM 成本不到 1000 元实现 1.3 km 跟踪。

**参考案例：iBubble。** 商用 iBubble 跟拍无人机正是采用这一架构：4 水听器 piUSBL + 压力传感器融合，1.5~6 米跟拍距离（最大 25 米）、60 米深度、90 分钟续航、9 kg 重量，以及 90 秒失联自动上浮的安全行为。

## 实际工程应用案例

**蛟龙号载人潜水器：USBL+LBL 组合导航。** 蛟龙号是中国首台 7000 米级载人潜水器，采用 USBL+LBL 组合定位方案。母船船底安装 USBL 换能器，实时跟踪潜水器位置；海底布放 LBL 应答器阵列，提供高精度位置校准。海试结果表明：LBL 精度优于 USBL，但 USBL 部署更灵活，两者互补使用。

**微型 USBL 换能器：消费级 ROV 的选择。** 随着 MEMS 和小型化技术发展，USBL 换能器已经做到手掌大小。Advanced Navigation 公司的 Subsonus 微型 USBL 系统，直径约 10 cm，重量不到 1 kg，可直接安装在小型 ROV 或 AUV 上，定位精度亚米级，适合浅水近岸作业。

**开源方案：Raspi2USBL。** 基于树莓派的开源 USBL 定位项目，专为水下机器人设计。海试结果：最大定位距离约 1300 米，斜距和方位角测量稳定。这个项目和 70 kHz 一发一收方案非常接近——都是用低成本硬件实现水声定位验证。

### 工业界主流方案对比

| 系统 | 厂商 | 精度 | 距离 | 适用场景 |
|---|---|---|---|---|
| USBL | Sonardyne、Kongsberg | 亚米~米级 | 数千米 | 船载跟踪 ROV/AUV |
| LBL | Sonardyne、Oceaneering | 厘米~分米级 | 数百米 | 海底固定区域高精度作业 |
| 微型 USBL | Advanced Navigation、EvoLogics | 米级 | 数百米 | 小型 ROV/AUV 近岸作业 |
| 开源方案 | Raspi2USBL、piUSBL | 米级 | 数十~数百米 | 科研、原型验证 |

### 产品全景

| 平台 | 类型 | 定位方式 | 深度 | 价格 |
|---|---|---|---|---|
| SoFi（MIT） | 科研原型 | BFSK 遥控 | 18 m | 科研 |
| iBubble evo | 跟拍无人机 | piUSBL | 60 m | 约 $2999 |
| FIFISH V-EVO | 缆控 ROV | AI 视觉锁定 | 100 m | $1399 |
| FIFISH V6 Plus | 缆控 ROV | 双声呐 + USBL | 100 m | 约 $2000 |
| Hydrus | 自主 ROV | DVL + USBL + INS | 300 m | 专业级 |
| Slocum 水下滑翔机 | 科研平台 | 航位推算 + 出水 GPS | 1000 m | 科研 |
| Raspi2USBL | 开源 | piUSBL | 实验室 | BOM < 1000 元 |

一个值得注意的趋势：FIFISH V-EVO 等消费级跟拍平台转向 AI 视觉锁定而非纯声学跟拍，说明纯声学跟拍在消费级价位尚未成为主流。

### 避障声呐

每个无线缆平台都必须具备短距离避障（0.5~6 米）：

| 产品 | 类型 | 距离 | 精度 |
|---|---|---|---|
| FIFISH Sonar Array | 单波束 | 0.5~6 m | ±5 cm |
| Chasing Distance Lock | 四向 | 数米 | ±25 mm |
| Chasing Omniscan 450FS | 前视成像 | 数十米 | 成像级 |
| Ping 360（BlueRobotics） | 360° 扫描 | 0.75~50 m | 2 m 处 1.6 mm |
| iBubble 避障声呐 | 近场 | 1~1.5 m | — |

## 参考文献

- Cestone, J. Precise Underwater Navigation. Quadripartite Meeting of the Institutes of Navigation, Rome, 1970.
- Watkins, W. A., & Schevill, W. E. Sound source location axes in array measurements. Deep-Sea Research, 1972, 19: 143-152.
- Davies, R. A Theoretical Comparison of Acoustic Systems for Near-Bottom Navigation. 1972.
- Milne, P. H. Underwater Acoustic Positioning Systems. E. & F.N. Spon, London, 1983.
- Yardim, S., et al. LBL System for Underwater Acoustic Positioning: Concept and Equations. arXiv:2204.08255, 2022.（[arXiv](https://arxiv.org/abs/2204.08255)）
- Sonardyne. The evolution of Sparse LBL (range-aiding): less is more in subsea positioning. 2026.
- Raspi2USBL: An open-source Raspberry Pi-based passive inverted ultra-short baseline positioning system.（[GitHub](https://github.com/ethanjinhuang/Raspi2USBL)、[arXiv](https://arxiv.org/html/2511.06998v1)）
- Rypkema N R, et al. Passive Inverted Ultra-Short Baseline (piUSBL) Localization: An Experimental Evaluation of Accuracy[C]. IEEE/RSJ IROS, 2019.
- Wang Y, et al. Passive Inverted Ultra-Short Baseline Positioning for a Disc-shaped Autonomous Underwater Vehicle: Design and Field Experiments[J]. IEEE RA-L, 2022.
- Katzschmann R K, et al. SoFi: Robotic Soft Fish for Untethered Observation[J]. Science Robotics, 2018.（[链接](https://www.science.org/doi/10.1126/scirobotics.aao6622)）
