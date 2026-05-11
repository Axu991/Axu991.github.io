---
title: "仿生机器鱼平台—梭影（Brizo）"
date: 2026-05-11
description: "Brizo 是一个具备敏捷运动能力的仿生机器鱼平台，适用于科研和环境监测等多种应用场景，身长0.5米，可用2.4GHz无线控制和915MHz无线通信，巡航速度为0.5米/秒，常态运动续航时间约1小时。"
cover:
    image: "cover.jpg" # 封面图片路径，建议放在同文件夹下
    alt: "项目封面图"
    caption: "模型与实物展示" # 封面下的说明文字
    relative: true # 必须为 true 才能读取同文件夹下的图片
    hidden: false  # 设为 false，详情页顶部也会显示封面
# 标签与分类

tags: ["视频编辑", "交互设计", "Python"]
categories: ["Projects"]
# 增加外部链接按钮
editPost:
    URL: "https://github.com/Axu991" # 比如你的 GitHub 或 Demo 地址
    Text: "GitHub"
---

## 项目简介
Brizo 是一个仿生机器鱼平台，其设计灵感来源于自然界中的梭鱼，我完成了从概念设计到原型制作的全过程，通过模仿其流线型结构和游动方式，实现了卓越的机动性和稳定性。该平台不仅适用于科学研究，还可应用于环境监测、水下探测和娱乐等领域。

> **技术栈：** `SolidWorks` / `3D打印` / `防水设计` / `ESP32`/ `Arduino` / `CAN总线` / `C language`
---

## 视频演示
分别展示了 Brizo 在户外环境和实验环境中的表现，可以更直观地了解 Brizo 的运动能力和交互体验。

<div style="
    display: grid; 
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); 
    gap: 20px; 
    margin: 20px 0;
">
  <!-- 第一个视频 -->
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls poster="cover1.jpg">
      <source src="demo_outside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo户外环境演示</p>
  </div>

  <!-- 第二个视频 -->
  <div style="border-radius: 12px; overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.1); background: #fff;">
    <video width="100%" controls poster="cover2.jpg">
      <source src="demo_inside.mp4" type="video/mp4">
    </video>
    <p style="text-align: center; font-size: 14px; color: #666; margin: 8px 0;">Brizo实验环境演示</p>
  </div>
</div>

---

## 户外环境细节展示
利用网格布局展示多张图片，避免页面拉得太长。

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 10px; margin: 20px 0;">
  <img src="detail-1.jpg" style="border-radius: 8px; width: 100%;" alt="细节图1">
  <img src="detail-2.jpg" style="border-radius: 8px; width: 100%;" alt="细节图2">
  <img src="detail-3.jpg" style="border-radius: 8px; width: 100%;" alt="细节图3">
  <img src="detail-4.jpg" style="border-radius: 8px; width: 100%;" alt="细节图4">
</div>

---

## 核心功能
*   **功能点一：** 描述你的第一个亮点。
*   **功能点二：** 描述你的第二个亮点。
*   **交互体验：** 解释视频中展示的交互逻辑。

---

## 如何运行 (可选)
如果你希望别人也能跑你的 Demo，可以放一段代码：

```bash
# 克隆仓库
git clone [https://github.com/user/repo.git](https://github.com/user/repo.git)

# 安装依赖
pip install -r requirements.txt

# 运行
python main.py