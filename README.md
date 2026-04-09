![](./files/Embodied-AI-Guide-logo.png)

<h1 align="center">CPS Lab Embodied AI 入门指南</h1>

> 面向 CPS Lab 成员的具身智能入门资料。目标不是做“资讯汇总”，而是帮助新同学在较短时间内建立问题框架、了解技术栈，并找到合适的上手路径。  

## 🐣 (1) Start Here - 从这里开始

> 具身智能是指一种基于物理实体进行感知和行动的智能系统, 其通过智能体与环境的交互获取信息、理解问题、做出决策并实现行动, 从而产生智能行为和适应性。

### (1.1) 如何使用这份指南

建议按下面顺序阅读：

1. 先看一遍本页，建立对具身智能整体问题的基本认识。
2. 再按自己的方向阅读后续章节：算法、基础设施、控制、硬件。
3. 阅读时优先回答三个问题：任务是什么、输入输出是什么、系统瓶颈在哪里。
4. 最后结合一个小型实践任务，把数据、模型、评测和系统约束串起来理解。

### (1.2) 阅读目标

完成本指南后，至少应当能够回答这些问题：

- 具身智能和纯视觉、多模态模型、传统机器人分别是什么关系。
- 一个具身系统通常由哪些模块组成，模块之间如何耦合。
- 数据、仿真、策略学习、控制、评测分别解决什么问题。
- 自己更适合从算法、系统、仿真还是真机侧切入。

<section id="robotwin"></section>

## ⚒️ (2) 实践建议

这一部分不再绑定某一个特定平台。对入门者来说，更重要的是通过一个小任务理解完整闭环，而不是记住某个项目的使用细节。

### (2.1) 一个最小闭环应该包含什么

建议至少走通下面四步：

- 定义任务：明确场景、动作空间、观测、成功判据。
- 准备数据：可以是真机数据、仿真数据，或公开数据集子集。
- 训练或复现一个基线策略：先保证能跑通，再谈提升。
- 做评测与复盘：看成功率、失败模式、泛化性和系统瓶颈。

### (2.2) 建议的入门标准

如果你能完成以下事情，说明已经具备继续深入的基础：

- 能解释一个 manipulation 或 navigation 任务的输入输出定义。
- 能独立跑通一个公开基线或复现论文中的核心实验。
- 能从失败案例中区分是感知问题、策略问题还是控制/执行问题。
- 能读懂一篇具身论文中的任务设定、训练数据来源与评测方式。

### (2.3) 选题时优先关注什么

- 问题是否清晰：任务边界要明确，别一开始就追求“大而全”。
- 资源是否匹配：算力、设备、数据和时间要与题目规模匹配。
- 评测是否可信：没有可靠评测，结果就很难解释。
- 扩展性是否存在：题目最好能自然延伸到下一阶段研究。

<section id="info"></section>

## 💻 (3) 编程基础要求

具身智能不是纯论文阅读型方向。即使研究重点在算法，也会持续遇到环境配置、远程开发、训练脚本调试、日志排查、数据清洗和实验复现等工程问题。下面这些基础能力默认应当尽快补齐。

### (3.1) 基础工程能力

除了 Linux / SSH / Git / ROS / ROS 2 这些基础设施能力，入门阶段也建议尽快建立基本的 AI 辅助编程能力，用它来加速阅读、修改、调试和验证，但不要把工程判断外包给模型。

Linux / SSH / Git / ROS / ROS 2 / Vibe Coding 的系统化内容已经整理到：

- [Engineering Basics - Linux / SSH / Git / ROS / ROS 2 / Vibe Coding 基础](./topics/infrastructure.md#engineering-basics)

至少需要具备以下习惯：

- 能把任务拆成明确的小步骤，而不是一次性给出模糊大需求
- 能让模型先解释代码结构、调用链和关键模块，再要求修改
- 能要求模型给出具体改动点、潜在风险和验证方式
- 能在采纳代码前自己阅读 diff，而不是直接相信生成结果
- 能结合日志、报错和实验结果继续追问，而不是只贴一句“运行失败”

建议达到的标准：

- 能借助 AI 工具快速定位仓库入口、训练脚本、配置项和数据流。
- 能让 AI 协助写辅助脚本、排查报错、补实验记录，但不把研究判断外包给它。
- 能在使用 AI 产出代码后自行完成运行验证、结果检查和必要重构。

### (3.2) 为什么这些基础很重要

- 没有 Linux 基础，很多时间会浪费在环境问题上，而不是研究问题上。
- 没有 SSH 基础，几乎无法高效使用实验室算力资源。
- 没有 Git 基础，代码协作、实验管理和结果回溯会很混乱。
- 不理解 ROS / ROS 2，后续接仿真器、传感器和机器人系统时会比较吃力。
- 没有基本的 AI 辅助编程能力，阅读和迭代速度会明显落后。

如果以上三部分还不熟，建议先集中补一周到两周，把这些能力补到“能独立完成一次远程实验”的程度，再深入后面的研究内容。

## 📄 (4) Useful Info - 有利于搭建认知的资料

这一章只保留少量适合入门阶段反复使用的基础资料，不再罗列大规模社区、公众号或泛推荐清单。

---

**入门材料**  
- 具身智能基础技术路线（Yunlong Dong）：[PDF](./files/具身智能基础技术路线-YunlongDong.pdf) ｜ [bilibili](https://www.bilibili.com/video/BV1d5ukedEsi)
- 斯坦福机器人学导论：[website](https://www.bilibili.com/video/BV17T421k78T)
- Cyber Nachos（偏系统与工程思维）：[website](https://cybernachos.github.io/)

**论文检索时重点关注的会议与期刊**  
Science Robotics, TRO, IJRR, JFR, RSS, RAL, IROS, ICRA, ICCV, ECCV, ICML, CVPR, NeurIPS, CoRL, ICLR, AAAI, ACL

**使用建议**  
- 入门阶段先抓主线，不要把时间消耗在大规模“资料收藏”上。
- 每读一篇论文，都尽量回答任务、观测、动作、数据、训练、评测六个问题。
- 每个方向先找 2 到 3 篇代表工作建立坐标系，再往最新工作扩展。

## 🍎 (5) Algorithm - 算法篇

这一篇把具身智能中最常用的“算法能力栈”从下往上串了起来：底层是工程工具与几何/标定/控制这类决定系统能否稳定运行的基础；中层是视觉与多模态表征（2D/3D/4D、prompting、affordance），它们把复杂世界压缩成可泛化、可对齐、可被策略利用的中间表示；上层则是学习与决策（RL/IL、VLA、LLM+Planner、快慢系统），把感知与任务目标转成可执行动作，并逐步走向更长程、更通用、更可部署的系统形态。

- [Common Tools —— 常用工程工具](./topics/algorithm.md#common-tools)
- [Vision Foundation Models —— 视觉基础模型](./topics/algorithm.md#foundation-models)
- [Robot Learning —— 机器人学习](./topics/algorithm.md#robot-learning)
- [LLM for Robotics —— LLM+机器人](./topics/algorithm.md#llm_robot)
- [VLA —— Vision-Language-Action Models](./topics/algorithm.md#vla)
  - [5.0 参考与综述](./topics/algorithm.md#vla) 
  - [5.1 经典工作](./topics/algorithm.md#vla)
  - [5.2 分层双系统 VLA](./topics/algorithm.md#vla)
  - [5.3 最新 VLA 工作](./topics/algorithm.md#vla)
- [Computer Vision —— 计算机视觉](./topics/algorithm.md#cv)
  - [6.1 2D/3D/4D Vision](./topics/algorithm.md#cv)
  - [6.2 Visual Prompting & Affordance](./topics/algorithm.md#cv)
- [Computer Graphics —— 计算机图形学](./topics/algorithm.md#cg)
- [Multimodal Models —— 多模态模型](./topics/algorithm.md#mm)
- [Robot Navigation —— 机器人导航](./topics/algorithm.md#navigation)
- [Embodied AI for X —— 具身智能+X](./topics/algorithm.md#embodied-ai-4-x)
  - [10.1 Healthcare](./topics/algorithm.md#medical)
  - [10.2 UAV](./topics/algorithm.md#uav)
  - [10.3 Autonomous Driving](./topics/algorithm.md#ad)


## 🏋️‍♂️ (6) Infrastruture - 软件基础设施篇

这一章关注的不是“具体某个模型”，而是**支撑具身智能研究与系统落地的软件基础设施（Infrastructure）**。仿真器决定你能构建怎样的世界，基准集决定你如何比较方法优劣，数据集决定模型最终学到什么样的行为分布。它们共同构成了具身智能中**最容易被忽视、但最影响上限与复现性的部分**。

- [(0) Engineering Basics - Linux / SSH / Git / ROS / ROS 2 / Codex 基础](./topics/infrastructure.md#engineering-basics)
- [(1) Simulators - 仿真器](./topics/infrastructure.md#simulators)
- [(2) Benchmarks - 基准集](./topics/infrastructure.md#benchmarks)
- [(3) Datasets - 数据集](./topics/infrastructure.md#datasets)

## 🎮 (7) Control - 控制篇

这一章并不是为了让你“立刻跑一个模型”，而是为具身智能系统提供**稳定性、可解释性与工程底座**。控制论保证系统在高频下不崩溃，机器人学提供几何与动力学约束，SLAM 与状态估计让机器人“知道自己在哪里”，ROS 与工程库则把理论变成可复现的系统。

- [(1) Control and Robotics —— 控制论与机器人学基础](./topics/control.md#control-robotics)
  - [(1.1) 经典课程](./topics/control.md#control-courses)
- [(2) 控制理论基础（Control Foundations）](./topics/control.md#control-foundations)
  - [(2.1) 经典控制（Classical Control）](./topics/control.md#classical-control)
  - [(2.2) 现代控制（最优控制）](./topics/control.md#modern-control)
  - [(2.3) 先进控制（Advanced Control）](./topics/control.md#advanced-control)
- [(3) 机器人学导论（Robotics Foundations）](./topics/control.md#robotics-foundations)
  - [(3.1) 推荐教材与材料](./topics/control.md#robotics-books)
  - [(3.2) 运动学与动力学](./topics/control.md#kinematics-dynamics)
  - [(3.3) 里程计与 SLAM](./topics/control.md#slam)
  - [(3.4) 工程生态与工具](./topics/control.md#engineering-stack)

## 🦾 (8) Hardware - 硬件篇

具身智能硬件涵盖多个技术栈：嵌入式软硬件、机械设计、机器人系统集成与传感器等。它们知识面很杂，但共同目标只有一个：把“算法”变成真实世界里稳定可复现的系统。关于硬件学习，最有效的方式几乎永远是 **从实践出发**：先做出一个能跑起来的最小系统，再逐步扩展复杂度与可靠性。

- [(1) Embedded —— 嵌入式](./topics/hardware.md#embedded)
- [(2) Mechanical Design —— 机械设计](./topics/hardware.md#mechanical)
- [(3) Robot System Design —— 机器人系统设计](./topics/hardware.md#robosystem)
- [(4) Sensors —— 传感器](./topics/hardware.md#sensors)
  - [(4.1) 深度相机（Depth Camera）](./topics/hardware.md#sensors)
- [(5) Tactile Sensing —— 触觉感知](./topics/hardware.md#tactile)
  - [(5.1) 视触觉传感器](./topics/hardware.md#tactile)
  - [(5.2) 电子皮肤](./topics/hardware.md#tactile)
  - [(5.3) 触觉应用与算法](./topics/hardware.md#tactile)
  - [(5.4) 传感器购买](./topics/hardware.md#tactile)
- [(6) Data Collection —— 数据采集硬件](./topics/hardware.md#data_collection)
- [(7) Companies —— 公司与硬件生态](./topics/hardware.md#companies)


<section id="license"></section>

## 🏷️ License - 许可协议

本项目为 **非商业使用（Non-Commercial Use）** 协议：

- 允许：个人学习、学术研究、非盈利使用；
- 禁止：任何形式的商业使用，包括但不限于公司/企业内部使用、
 集成到收费产品或服务中、或用于任何营利目的。

详情请查看仓库中的 [LICENSE](./LICENSE) 文件。
