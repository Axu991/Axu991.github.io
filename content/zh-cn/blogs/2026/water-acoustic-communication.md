---
title: "水声通信技术入门：调制方式与系统原理"
date: 2026-10-31
description: "声波如何在水下承载数字信息——水声信道特点、OOK/FSK/PSK/OFDM 四种调制方式、横向对比与选型思路、技术发展历程以及该领域的基石论文。"
tags: ["水声通信", "调制方式", "OFDM", "水声信道", "piUSBL"]
categories: ["技术笔记"]
---

## 为什么要用水声通信

水声通信是利用声波在水中传递信息技术。电磁波（无线电、WiFi、蓝牙）在海水中衰减极快，传播距离通常不超过几米；而声波在海水中可以传播几百米到几十公里，因此成为水下无线通信的唯一可行手段。从早期水下钟导航，到现代水下传感器网络，这项技术已发展超过一百年。

### 水声信道的特点

水声信道是通信领域最复杂的信道之一：

| 特点 | 说明 | 影响 |
|---|---|---|
| 传播速度慢 | 约 1500 m/s，是光速的 20 万分之一 | 传输延迟大，实时性差 |
| 衰减大 | 频率越高衰减越快 | 可用带宽窄，通常只有几 kHz |
| 多径效应 | 声波碰到水面、水底反射 | 信号失真，码间干扰 |
| 多普勒效应 | 收发双方移动导致频率偏移 | 接收端需要频率跟踪 |
| 环境噪声 | 海浪、鱼虾、船舶噪声 | 信噪比低，误码率高 |

### 通信系统基本结构

一个典型的水声通信系统包括：发射端（信号源 + 调制 + 功放 + 换能器）、水声信道、接收端（换能器 + 前放 + 解调 + 数据处理）。核心在于调制解调技术——如何把数字数据编进声波里。

## OOK（开关键控）

**基本原理：** 最简单的调制方式，用信号的有无代表 0 和 1：有载波信号 = 1，没有载波信号 = 0。打个比方：像手电筒照明，亮一下代表 1，灭代表 0。

**发射端：** 对于输入比特流（如 `10111001`）——比特 1 打开载波，发射一段 75 kHz 正弦波；比特 0 关闭载波，不发射。

**接收端：** 采样接收信号，计算信号能量；能量超过阈值判为 1，低于阈值判为 0。

- 优点：实现最简单，硬件成本低，功耗低。
- 缺点：抗噪声能力差，容易受环境干扰，传输速率低。
- 适用场景：低速短距离通信、简单遥控、测距脉冲。

## FSK（频移键控）

**基本原理：** 用不同的载波频率代表 0 和 1。频率 f1 代表 0，频率 f2 代表 1。打个比方：像吹口哨，吹低音代表 0，吹高音代表 1。

**发射端：** 比特 0 发射频率 f1（比如 70 kHz），比特 1 发射频率 f2（比如 75 kHz）。

**接收端：** 采样接收信号，用两个带通滤波器分别滤出 f1 和 f2；哪个频率能量大，就判断是哪个比特。

- 优点：抗噪声能力比 OOK 强，不需要精确的相位同步。
- 缺点：占用带宽大，频率切换需要时间，传输速率中等。
- 适用场景：中低速通信、抗干扰要求较高的场景。

## PSK（相移键控）

**基本原理：** 用载波的相位变化代表 0 和 1。最基本的 BPSK：相位 0° 代表 0，相位 180° 代表 1。打个比方：像拍手，先拍左手代表 0，先拍右手代表 1——波形起点方向反过来了，但频率和幅度都没变。

**发射端：** 比特 0 发射正常相位的载波，比特 1 发射反相 180° 的载波；频率和幅度不变，只改相位。

**接收端：** 需要一个本地参考载波，把接收信号与参考信号相乘——相位相同输出正电压（判 0），相位相反输出负电压（判 1）。

- 优点：频谱利用率高，相同带宽下传输速率比 OOK/FSK 高，抗噪声能力强。
- 缺点：实现复杂，需要精确的载波同步，对多普勒效应敏感。
- 适用场景：中高速通信、带宽受限的场景。

## OFDM（正交频分复用）

**基本原理：** 把高速数据流拆成很多路低速数据流，分别调制在大量互相正交的子载波上，同时传输。打个比方：像一条很宽的马路，不是只走一条车道，而是分成几十条车道同时跑车——每条车道走得慢，但加起来总速度快很多。

**技术特点：**

1. 子载波数量多：通常几十到几百个。
2. 每个子载波上可以用 BPSK/QPSK/QAM 等调制。
3. 抗多径效应强：每个子载波速率低，码间干扰小。
4. 频谱利用率高：子载波正交，不浪费带宽。

- 优点：传输速率最高，抗多径能力强，是现代水声通信的主流技术。
- 缺点：实现最复杂，需要 DSP 或 FPGA 处理，对同步要求极高。
- 适用场景：高速水声通信、水下传感器网络、实时视频传输。

## 四种调制方式对比

| 调制方式 | 控制什么 | 频谱效率 | 抗噪声 | 复杂度 | 典型速率 |
|---|---|---|---|---|---|
| OOK | 信号有无 | 低 | 弱 | 最简单 | 几百 bps |
| FSK | 信号频率 | 中低 | 中 | 简单 | 几千 bps |
| PSK | 信号相位 | 中高 | 强 | 复杂 | 几万 bps |
| OFDM | 多载波并行 | 最高 | 最强 | 最复杂 | 几十万 bps |

怎么选——从简单到复杂，按需选择：

- 只做测距、简单开关控制 → OOK 就够了。
- 需要低速传数据、抗干扰一般 → FSK。
- 需要中高速、带宽有限 → PSK。
- 需要高速通信、有 DSP 能力 → OFDM。

## 技术发展历程

| 年代 | 里程碑 | 意义 |
|---|---|---|
| 19 世纪末 | 水下钟导航 | 最早的水声通信，灯塔附近放水下钟 |
| 1914 年 | Fessenden 回声测距 | 第一次用声波探测冰山，水声开端 |
| 1940 年代 | 二战主动声纳 | 反潜探测，声纳技术成熟 |
| 1981 年 | DPSK 水声通信 | 数字调制开始应用 |
| 2000 年代 | OFDM 水声 modem | 高速水声通信兴起 |
| 2010 年代 | 软件定义水声 modem | 灵活可配置，多频段支持 |
| 2020 年代 | 水声传感器网络 | 多节点组网，水下物联网 |

发展趋势：从模拟到数字、从非相干到相干、从单载波到多载波、从点对点到网络化。现代水声通信系统普遍采用 OFDM + MIMO 技术，速率从最早的几十 bps 提升到现在的几十 kbps，甚至更高。

## 基石论文详解

1. **Akyildiz、Pompili & Melodia（2005）**——《Underwater acoustic sensor networks: research challenges》。水声传感器网络领域的奠基性综述，系统梳理了水下信道特点（衰减、多径、多普勒），提出水下网络体系结构，指出 MAC、路由、定位等关键挑战，奠定了整个研究方向的框架，是入门水声通信必读的第一篇综述。（[DOI](https://doi.org/10.1016/j.adhoc.2005.01.002)）

2. **Stojanovic（2009）**——《Underwater acoustic communications: Design considerations on the physical layer》。详细分析水声信道对物理层设计的约束，对比 OOK、FSK、PSK 等调制方式在水声信道下的性能，讨论均衡、同步等关键技术问题，是水声通信调制解调设计的经典参考。

3. **Kilfoyle & Baggeroer（2000）**——《The evolution of underwater acoustic communication》。回顾水声通信从二战声纳到数字调制的发展历程，梳理从模拟到数字、从非相干到相干的技术演进路线，是了解技术发展脉络的经典文献。（[DOI](https://doi.org/10.1109/48.820738)）

4. **Stojanovic & Preisig（2009）**——《Underwater acoustic communication channels: Propagation models and statistical characterization》。建立水声信道的传播模型和统计特性，包括多径结构、时延扩展、多普勒扩展等关键参数的数学描述，是系统仿真和设计的标准信道模型参考。（[DOI](https://doi.org/10.1109/MCOM.2009.4752682)）

5. **WHOI piUSBL（2015）**——《Passive inverted ultra-short baseline (piUSBL) localization》。提出 piUSBL 技术——水面放一个信标定期发信号，水下机器人带接收阵列，只收不发，测距离和角度。水下端功耗低、成本低，适合微型 AUV，是低成本水下定位的主流方案，后续 MIT 用 SandShark AUV 验证了实际性能。

6. **Raspi2USBL 开源实现（2025）**——《An open-source Raspberry Pi-based passive inverted ultra-short baseline positioning system》。用树莓派实现开源的 piUSBL 系统，硬件设计、固件、上位机软件全套开源，把昂贵的商用 USBL 系统成本降到几百元级别，让普通研究者和爱好者也能复现高精度水下定位，是开源水声定位的里程碑。（[GitHub](https://github.com/ethanjinhuang/Raspi2USBL)、[arXiv](https://arxiv.org/html/2511.06998v1)）

7. **Jensen 等（2016）**——《Software-defined underwater acoustic modems: Historical review and the NILUS approach》。提出软件定义水声 modem 的概念，用通用硬件 + 可配置软件，支持多频段、多调制方式，推动了水声 modem 从专用硬件走向软件定义的通用平台。

## 参考文献

- Akyildiz I F, Pompili D, Melodia T. Underwater acoustic sensor networks: research challenges[J]. Ad Hoc Networks, 2005, 3(3): 257-279.
- Stojanovic M. Underwater acoustic communications: Design considerations on the physical layer[C]. Proceedings of the Fourth ACM International Conference on Underwater Networks & Systems, 2009.
- Kilfoyle D, Baggeroer A B. The evolution of underwater acoustic communication[J]. IEEE Journal of Oceanic Engineering, 2000.
- Stojanovic M, Preisig J. Underwater acoustic communication channels: Propagation models and statistical characterization[J]. IEEE Communications Magazine, 2009, 47(1): 84-89.
- 水声通信与组网技术综述[J]. 电子与信息学报, 2023.
- Jensen S, et al. Software-defined underwater acoustic modems: Historical review and the NILUS approach[J]. IEEE Journal of Oceanic Engineering, 2016.
- Sherman D H, et al. Underwater acoustic positioning systems: A review[J]. Marine Geophysical Researches, 2016.
