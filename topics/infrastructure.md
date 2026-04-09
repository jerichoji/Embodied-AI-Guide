<h1 align="center">Embodied-AI-Guide<br>软件基础设施篇</h1>

> 这一章关注的不是“具体某个模型”，而是**支撑具身智能研究与系统落地的软件基础设施（Infrastructure）**。  
> 仿真器决定你能构建怎样的世界，基准集决定你如何比较方法优劣，数据集决定模型最终学到什么样的行为分布。它们共同构成了具身智能中**最容易被忽视、但最影响上限与复现性的部分**。

软件部分可以理解为三层：  
**Simulators（仿真环境）** 决定你能“跑什么物理世界”；**Benchmarks（评测基准）** 决定你用什么任务衡量方法优劣；**Datasets（数据集）** 决定你能训练出怎样的策略分布。建议优先跑通“一个仿真器 + 一个基准 + 一个数据集”的最小闭环，再逐步扩展到多平台与多模态。

<section id="engineering-basics"></section>

## (0) Engineering Basics - Linux / SSH / Git / ROS / ROS 2 / Codex 基础

具身智能不是只会“跑模型”就够了。  
真正进入实验室或项目后，你几乎每天都会遇到几类基础设施问题：**Linux 环境管理、SSH 远程开发、Git 协作管理、ROS 系统通信、ROS 2 工程化部署，以及 AI 辅助编程工具的正确使用**。这些内容本身不直接提升模型指标，但决定了你能否稳定复现、调试和部署一个系统。

| 模块 | 为什么重要 | 至少需要掌握什么 |
|---|---|---|
| Linux | 大多数训练、仿真、部署都运行在 Linux 环境 | 文件系统、环境变量、进程管理、日志排查 |
| SSH | 实验室 GPU 服务器与远程机器人几乎都靠 SSH 访问 | 远程登录、免密配置、传文件、端口转发 |
| Git | 代码协作、实验版本管理、复现实验都离不开版本控制 | clone、commit、branch、pull、push、diff |
| ROS | 经典机器人软件生态，资料与历史项目很多 | topic / service / launch / package 基本概念 |
| ROS 2 | 当前更主流的新一代工程栈，适合长期维护与部署 | DDS、workspace、colcon、节点通信与调试 |
| Codex | 加速读代码、改代码、排错和整理文档 | 给清晰任务、审查 diff、验证输出结果 |

### (0.1) Linux 基础

Linux 是具身项目最基本的工作环境。  
你不一定一开始就需要很强的系统能力，但至少要做到：能在服务器上独立创建环境、定位报错、查看日志、维护长期运行任务。

| 类别 | 常用命令 / 概念 | 说明 |
|---|---|---|
| 文件与目录 | `ls` `cd` `pwd` `cp` `mv` `rm` `mkdir` `find` | 最基本的路径与文件管理 |
| 文本查看与搜索 | `cat` `less` `head` `tail` `grep` `rg` | 查日志、看配置、找代码入口 |
| 权限与进程 | `chmod` `ps` `kill` `top` `htop` | 管理脚本权限与后台任务 |
| 环境与包管理 | `bash` `zsh` `conda` `pip` `PATH` | 处理依赖与解释器环境 |
| 后台任务 | 重定向、`nohup`、`tmux`、`screen` | 保证远程断开后任务继续跑 |

在 Linux 环境里，`~/.bashrc` 也是一个很常见、很重要的文件。  
它通常用于保存你每次打开 shell 时都希望自动生效的配置，例如：

- 环境变量：`PATH`、`CUDA_HOME`、`LD_LIBRARY_PATH`
- 命令别名：`alias ll='ls -alh'`
- conda 初始化
- ROS / ROS 2 的 `setup.bash`
- 常用项目路径或代理配置

一个常见直觉是：**临时在命令行里 `export` 的东西，只对当前 shell 生效；写进 `~/.bashrc` 后，后续新开的 shell 才会自动带上这些设置。**

常见操作：

```bash
vim ~/.bashrc
source ~/.bashrc
```

例如，一个很常见的 `~/.bashrc` 片段可能是：

```bash
source /opt/ros/humble/setup.bash
export ROS_DOMAIN_ID=10
alias ll='ls -alh'
```

这表示：

- 打开终端时自动加载 ROS 2 Humble 环境。
- 默认把当前机器放到 `domain 10`，用于和同组设备通信，或与其他项目隔离。
- 顺手加一个常用命令别名。

对于刚入门的人，至少要知道两件事：

- `~/.bashrc` 配错后可能影响终端启动、Python 路径、conda、ROS 环境。
- 改完 `~/.bashrc` 后，如果想立刻在当前终端生效，通常需要执行一次 `source ~/.bashrc`。

建议达到的标准：

- 能独立在 Linux 服务器上创建环境、安装依赖并运行训练或评测脚本。
- 能区分报错来自 Python 代码、CUDA、依赖冲突还是路径配置问题。
- 能用命令行快速定位数据、配置文件、日志和 checkpoint。

### (0.2) SSH 基础

具身智能实验通常依赖远程 GPU 服务器、实验室工作站或板载计算机，因此 SSH 是日常工作流的一部分，而不是附加技能。

| 任务 | 常见命令 / 配置 | 说明 |
|---|---|---|
| 登录远程机器 | `ssh user@host` | 最基础的远程连接方式 |
| 免密登录 | `ssh-keygen` / `authorized_keys` | 减少频繁输入密码 |
| 简化连接 | `~/.ssh/config` | 管理多台服务器别名与端口 |
| 文件传输 | `scp` / `rsync` | 同步代码、数据、模型与日志 |
| 端口转发 | `ssh -L` / `ssh -R` | 访问远程 `Jupyter`、`TensorBoard`、Web UI |

建议达到的标准：

- 能独立完成本地开发、远程训练、结果回传的完整流程。
- 能在远程连接断开后通过 `tmux` 让任务继续运行。
- 能区分“连不上服务器”“权限不足”“端口未开放”“磁盘满了”等常见问题。

### (0.3) Git 基础

Git 是研究协作和实验复现的基本工具。  
无论是跟同学协作、维护自己分支上的实验代码，还是回溯某次改动引入的 bug，都离不开版本控制。不会 Git，工程节奏通常会很快失控。

| 任务 | 常见命令 | 说明 |
|---|---|---|
| 拉取仓库 | `git clone` | 获取代码与提交历史 |
| 查看状态 | `git status` | 判断当前有哪些改动 |
| 查看差异 | `git diff` | 检查自己到底改了什么 |
| 提交版本 | `git add` / `git commit` | 保存一组可解释的改动 |
| 分支协作 | `git branch` / `git checkout` / `git switch` | 隔离功能开发与实验尝试 |
| 同步远程 | `git pull` / `git push` | 与 GitHub / GitLab 保持一致 |

入门阶段建议先掌握这些习惯：

- 提交前先看 `git status` 和 `git diff`，确认没有把无关改动一起提交。
- 一个 commit 只做一件相对清晰的事，便于回滚和 review。
- 做新实验或新功能时尽量开分支，不要长期直接在主分支上堆改动。
- 遇到冲突时先理解冲突来自谁改了什么，而不是机械地“全选自己版本”。

建议达到的标准：

- 能独立完成 clone、修改、commit、push、pull 这一套基本工作流。
- 能看懂分支、commit、merge request / pull request 的基本协作关系。
- 能用 `git diff` 和提交历史定位是哪次修改引入了行为变化。

### (0.4) ROS 基础

ROS（通常指 ROS 1）是机器人软件开发里最经典的中间件生态。  
很多历史项目、开源包、课程和硬件驱动都围绕 ROS 构建，所以即使你未来主要用 ROS 2，也建议先理解 ROS 的基本抽象。

| 核心概念 | 一句话理解 | 典型命令 / 工具 |
|---|---|---|
| Node | 一个独立运行的功能模块 | `rosnode` |
| Topic | 节点间异步消息流 | `rostopic` |
| Service | 请求-响应式通信 | `rosservice` |
| Message | 通信数据格式 | `msg` / `srv` |
| Launch | 一次启动多个节点与参数 | `roslaunch` |
| Package | 代码与配置的组织单元 | `catkin` |

入门时建议先理解三件事：

- 一个机器人系统如何被拆成多个节点，以及节点之间如何通过 topic 通信。
- 传感器数据、控制命令、状态估计分别通过什么消息流在系统中传递。
- launch、参数文件、rviz、bag 这些工具如何帮助你调试完整链路。

常见入门资源：ROS1 入门教程：[link](http://www.autolabor.com.cn/book/ROSTutorials/)，Open Robotics：[link](https://openrobotics.org/)。

### (0.5) ROS 2 基础

ROS 2 可以理解为 ROS 的工程化升级版本，更强调**分布式通信、实时性、跨平台支持和长期维护**。  
如果你的目标是新项目、真实机器人部署、与现代仿真器或中间件对接，通常更建议优先学习 ROS 2。

| 维度 | ROS 1 | ROS 2 |
|---|---|---|
| 通信基础 | 自定义通信机制 + ROS Master | 基于 DDS，去中心化发现 |
| 构建方式 | `catkin` | `colcon` + `ament` |
| 生命周期 / 工程化 | 相对弱 | 更适合复杂系统与部署 |
| 适用场景 | 历史项目、教程多 | 新项目、工业部署、更长期维护 |

ROS 2 入门阶段至少需要掌握：

- Workspace、package、`colcon build`、`source install/setup.bash` 这套基本开发流程。
- topic、service、action 三种通信方式分别适合什么问题。
- `launch`、参数、日志、`rviz2`、`ros2 bag` 的基本调试方法。
- 常见发行版和生态差异，例如 Humble / Jazzy，以及与 Gazebo、MoveIt 2 的关系。

在真实机器人或实验室网络里，还经常会遇到**同一 Wi-Fi 下多台机器互相“串台”**的问题。  
ROS 2 基于 DDS 做自动发现；如果几台机器在同一局域网、运行相同中间件配置，默认就可能彼此看到对方的 topic 和节点。最常见的隔离方法是设置 `ROS_DOMAIN_ID`。

| 场景 | 现象 | 常见处理方式 |
|---|---|---|
| 同一实验室多台机器人共用 Wi-Fi | topic 列表里出现别人的节点 / 话题 | 给每套系统分配不同的 `ROS_DOMAIN_ID` |
| 自己的笔记本连机器人热点或路由器 | `rviz2` / `ros2 topic list` 看不到设备 | 确认双方在同一网段，且 `ROS_DOMAIN_ID` 一致 |
| 仿真机和真机并行开发 | 消息混在一起，调试困难 | 仿真和真机使用不同 domain |

最常见的设置方式：

```bash
export ROS_DOMAIN_ID=10
```

如果两台机器需要互相通信，它们通常需要满足：

- 处于同一局域网或能互相路由。
- 使用相同的 `ROS_DOMAIN_ID`。
- 使用兼容的 ROS 2 发行版和 DDS 配置。

如果你想让不同项目彼此隔离，则给它们设置不同的 `ROS_DOMAIN_ID` 即可。  
工程上很常见的做法是：**一台机器人 / 一个项目 / 一组实验使用一个固定 domain**，并把它写进对应的启动脚本或 `~/.bashrc`。

常见入门资源：ROS2 入门：[link](https://zhangzhiwei-zzw.github.io/ROS2%E5%AD%A6%E4%B9%A0/ROS2/)，ROS2 Humble 3h 教程：[link](https://discourse.openrobotics.org/t/ros2-humble-3h-tutorial-for-beginners/28500/)，MoveIt 2：[link](https://moveit.picknik.ai/main/index.html)，Gazebo：[link](https://gazebosim.org/)。

### (0.6) Codex - Vibe Coding 基础

在具身智能项目里，很多工作并不是“从零写算法”，而是**读仓库、接脚本、改配置、查报错、补实验记录、梳理数据流**。  
这类工作非常适合交给 Codex 这类 AI 编码助手加速，但前提是你把它当成**工程助手**，而不是替你做技术判断的“自动驾驶”。

| 用法 | 适合让 Codex 做什么 | 你自己必须负责什么 |
|---|---|---|
| 读代码 | 找入口、解释模块关系、梳理调用链 | 判断解释是否准确 |
| 改代码 | 按明确需求修改脚本、补参数、加日志、修小 bug | 审查 diff，确认改动没有副作用 |
| 排查问题 | 根据报错和日志给出排查路径 | 区分根因和表面现象，做最终判断 |
| 写辅助内容 | 生成 README、实验记录、数据处理脚本、命令模板 | 确认内容和项目实际一致 |

比较有效的使用方式通常是：

- 先给清楚上下文，例如仓库路径、报错信息、你希望达到的目标。
- 让它先解释代码结构或问题定位，再要求它修改。
- 把任务拆小，例如“先定位入口”“再改 launch 文件”“最后补验证命令”。
- 要求它说明改了什么、为什么这样改、还有什么风险。

不推荐的使用方式包括：

- 只说一句“帮我把这个项目跑起来”，不给环境和报错信息。
- 不看 diff，直接接受生成代码。
- 把实验设计、研究判断、结果解释也一起外包给模型。

建议达到的标准：

- 能用 Codex 快速定位仓库入口、训练脚本、配置项和数据流。
- 能让 Codex 协助写辅助脚本、排查报错、补实验记录，但不把研究判断外包给它。
- 能在使用 AI 产出代码后自行完成运行验证、结果检查和必要重构。

### (0.7) 这部分学到什么程度算够用

如果你已经能做到下面这些事，说明 Linux / SSH / Git / ROS / ROS 2 / Codex 的基础已经足以支撑后续具身项目：

- 能在远程 Linux 服务器上独立搭环境、跑脚本、看日志和维护后台任务。
- 能通过 SSH 完成本地开发与远程实验的闭环。
- 能用 Git 管理自己的一组实验改动，并和他人协作同步代码。
- 能读懂一个 ROS 或 ROS 2 项目的 workspace 结构、launch 文件和节点通信关系。
- 能把 Codex 当成工程助手使用，并对生成结果做必要审查与验证。
- 能把传感器、控制器、仿真器和可视化工具接成一个最小系统，并定位基本故障。

---

<section id="simulators"></section>

## (1) Simulators - 仿真器

常见仿真器 wiki：[link](https://simulately.wiki/)

| 仿真器 | 典型生态 / 对应基准与工具链 |
|---|---|
| IsaacGym | legged-gym：[link](https://github.com/leggedrobotics/legged_gym)<br>parkour（含蒸馏与真机部署）：[link](https://github.com/ZiwenZhuang/parkour)<br>extreme-parkour：[link](https://github.com/chengxuxin/extreme-parkour) |
| IsaacSim | BEHAVIOR-1K：[link](https://behavior.stanford.edu/behavior-1k) + OmniGibson（工具链）：[link](https://behavior.stanford.edu/omnigibson/)<br>ARNOLD：[link](https://arnold-benchmark.github.io/)<br>GarmentLab：[link](https://garmentlab.github.io/) / DexGarmentLab：[link](https://wayrise.github.io/DexGarmentLab/) |
| MuJoCo | robosuite：[link](https://robosuite.ai/docs/overview.html) + robomimic（工具链）：[link](https://robomimic.github.io/)<br>LIBERO：[link](https://libero-project.github.io/main.html)<br>MetaWorld：[link](https://meta-world.github.io/)<br>Gymnasium-Robotics：[link](https://robotics.farama.org/)<br>RoboCasa：[link](https://github.com/robocasa/robocasa?tab=readme-ov-file)<br>RoboHive：[link](https://github.com/vikashplus/robohive) |
| SAPIEN | ManiSkill：[link](https://maniskill.readthedocs.io/en/latest/index.html)<br>RoboTwin：[link](https://github.com/TianxingChen/RoboTwin) |
| CoppeliaSim | RLBench：[link](https://github.com/stepjam/RLBench)<br>PerAct2：[link](https://bimanual.github.io/)<br>COLOSSEUM：[link](https://robot-colosseum.github.io/) |
| PyBullet | CALVIN：[link](https://github.com/mees/calvin?tab=readme-ov-file)<br>Ravens：[link](https://github.com/google-research/ravens)<br>VimaBench：[link](https://github.com/vimalabs/VimaBench) |
| Genesis | 入口：[link](https://genesis-embodied-ai.github.io/) |
| SOFA | 框架：[link](https://github.com/sofa-framework/sofa/)<br>常用于软体机器人仿真 |
| GenieSim | 框架：[link](https://github.com/AgibotTech/genie_sim)<br>评测与文档：[link](http://agibot-world.com/sim-evaluation/docs) |
| Gazebo | 平台：[link](https://gazebosim.org)<br>Open Robotics 维护：[link](https://openrobotics.org/)<br>与 ROS / ROS 2 深度集成，适合移动机器人、仓储物流等场景 |

教程：Isaac 101（Blog）：[link](https://axi404.top/tags/isaac%20101)

---

<section id="benchmarks"></section>

## (2) Benchmarks - 基准集

基准集通常定义了：**任务集合 + 评测协议 +（可选）参考实现**。它们的价值是让不同方法在同一套任务与指标上可复现对比。下面列的是你当前条目中最常见、且各自定位清晰的基准。

| 基准 | 链接 | 一句话定位 |
|---|---|---|
| RoboTwin 2.0 | [link](https://github.com/robotwin-Platform/RoboTwin) | 程序化生成双臂任务数据与 50 个双臂评测任务（偏“双臂+规模化生成”） |
| SimplerENV | [link](https://github.com/simpler-env/SimplerEnv) | 轻量化、可快速对比策略在操作任务上的表现 |
| LIBERO | [link](https://github.com/Lifelong-Robot-Learning/LIBERO)<br>[link](https://libero-project.github.io/intro.html) | 程序化生成管道 + 视觉运动策略架构与终身学习设置（偏“终身/顺序学习”） |
| CALVIN | [link](https://github.com/mees/calvin)<br>[link](http://calvin.cs.uni-freiburg.de/) | 语言条件 + 多模态输入 + 长视野操纵（偏“长程任务与规划”） |
| Meta-World | [link](https://meta-world.github.io/) | 50 操作任务，经典多任务/元强化学习基准（偏“多任务泛化”） |
| Embodied Agent Interface | [link](https://embodied-agent-interface.github.io/) | 评测 LLM 在具身决策链路（理解/分解/序列化），不强调低层执行 |
| RoboGen | [link](https://github.com/Genesis-Embodied-AI/RoboGen)<br>[link](https://robogen-ai.github.io/) | 生成任务/场景/带标注数据（偏“生成数据而非直接生成 policy”） |

---

<section id="datasets"></section>

## (3) Datasets - 数据集

数据集决定了策略的“经验分布”。阅读数据集时建议关注四件事：  
**(1) 真实 vs 仿真**、**(2) 机器人同构 vs 异构**、**(3) 模态（RGB/RGB-D/语言/触觉/声音等）**、**(4) 是否附带训练代码与硬件搭建/采集流程**。下面把你的条目统一成一个紧凑表，避免过长的散点描述。

| 数据集 | 链接 | 关键特点（紧凑版） |
|---|---|---|
| Open X-Embodiment（RT-X） | [link](https://robotics-transformer-x.github.io/) | 22 种机器人平台、百万级真实轨迹，覆盖大量技能与任务（大规模、跨本体） |
| AgiBot World Datasets（智元） | [link](https://agibot-world.com/) | 百万级轨迹、同构机器人采集、多级质检与人工在环流程（工业化采集流程） |
| RoboMIND | [link](https://x-humanoid-robomind.github.io/) | 10.7 万真实演示、96 类物体、四种协作臂、任务按类别组织（真实多任务） |
| ARIO（All Robots in One） | [link](https://imaei.github.io/project_pages/ario/) | 2D/3D/文本/触觉/声音五模态；操作+导航；仿真+真实；统一格式且规模大 |
| MimicGen | [link](https://github.com/NVlabs/mimicgen)<br>[link](https://mimicgen.github.io/) | 基于 robosuite+MuJoCo 的数据生成框架；少量真人演示扩增为大量仿真数据 |
| RoboCasa | [link](https://github.com/robocasa/robocasa)<br>[link](https://robocasa.ai/) | MuJoCo 厨房高保真平台；多环境多物体；原子任务+组合任务（偏家居厨房） |
| DexMimicGen | [link](https://github.com/NVlabs/dexmimicgen/)<br>[link](https://dexmimicgen.github.io/) | 面向双臂桌面操作；增强版 real2sim2real 数据生成；少量演示生成大量轨迹 |
| FUSE Dataset | [link](https://fuse-model.github.io/) | 远程操控轨迹；语言指令 + 复杂遮挡；多任务设置（多传感器融合研究友好） |
| BiPlay Dataset | [link](https://dit-policy.github.io/) | 双臂轨迹；随机物体与背景；长视频切片成带语言描述的剪辑（泛化导向） |
| DROID | [link](https://droid-dataset.github.io/) | 7.6 万轨迹、350 小时、564 场景、86 任务；附硬件与训练代码（真实大规模） |
| BridgeData V2 | [link](https://rail-berkeley.github.io/bridgedata/) | 6 万轨迹；多环境多技能；目标图像/语言指令；包含远程操控与脚本执行 |
| Ego4D Sounds | [link](https://ego4dsounds.github.io/) | 第一人称视频 + 环境声音；强调动作与声音对齐（声音模态很有价值） |
| RH20T | [link](https://rh20t.github.io/) | 人机交互数据；含人脸与语音等敏感信息；体量大且提供缩减版（注意隐私与合规） |
| 白虎数据集 | [link](https://www.openloong.org.cn/cn/dataset) | 异构机器人；多场景多任务；面向跨平台评估与训练（本体覆盖面广） |
