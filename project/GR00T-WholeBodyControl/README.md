<h1 align="center">GR00T-WholeBodyControl 机器人使用手册</h1>

> 面向已经完成机器人端部署后的日常使用者。目标不是教大家重新安装，而是让同学能安全地连接机器人、启动已部署好的 GR00T-WholeBodyControl 程序、运行低风险测试动作。

## 🐣 (1) Start Here - 从这里开始

> 真机使用的第一原则是：不确定就先停机。不要在机器人运动过程中临时猜参数、切换未知模型或测试高风险动作。

### (1.1) 使用前安全要求

真机运行前必须确认：

- 机器人周围没有人和障碍物。
- 急停按钮可用，安全员在场。
- 操作员知道如何停止程序。
- 第一次运行只测试低风险动作。
- 不确定机器人状态时，先停止，不要边运行边改参数。

建议每次实验至少两个人：一人操作电脑，一人负责观察机器人和急停。

### (1.2) 本手册适合谁

- 已经知道机器人 IP 和 SSH 账号的使用者。
- 只需要启动和使用现成部署环境的新同学。
- 需要记录实验日志、复现实验流程的同学。
- 临时接手机器人操作，但不负责维护部署环境的同学。

如果你需要重新安装、编译、更新模型或修改底层配置，请先联系维护者。

<section id="robot-info"></section>

## 🤖 (2) Robot Info - 机器人信息

使用前先把下面信息补全：

| 项目 | 内容 |
|---|---|
| 机器人名称 | `Unitree_G1a/b` |
| 机器人 IP | `192.168.1.137(Unitree_G1a)`, `192.168.1.112(Unitree_G1b)` |
| SSH 用户名 | `unitree` |
| SSH 端口 | `22` |
| 项目目录 | `/home/unitree/workspace/sonic` |
| 部署目录 | `/home/unitree/workspace/sonic/GR00T-WholeBodyControl` |


<section id="ssh"></section>

## 🔐 (3) SSH - 连接机器人

### (3.1) 前置检查

1. 给机器人上电
2. 检查是否与机器人处于同一网络
可以使用 `ping <robot_ip>` 来确认网络连接。
```bash
ping 192.168.1.137 # 此处以G1a为例，请确认连接到正确的机器人IP地址
``` 
如果成功，会看到类似下面的输出:

```bash
PING 192.168.1.112 (192.168.1.112): 56 data bytes
64 bytes from 192.168.1.112: icmp_seq=0 ttl=64 time=568.537 ms
64 bytes from 192.168.1.112: icmp_seq=1 ttl=64 time=88.915 ms
64 bytes from 192.168.1.112: icmp_seq=2 ttl=64 time=5.350 ms
64 bytes from 192.168.1.112: icmp_seq=3 ttl=64 time=5.484 ms
64 bytes from 192.168.1.112: icmp_seq=4 ttl=64 time=6.537 ms
```
> `ping` 是一个网络工具，用于测试计算机与目标主机之间的网络连接是否正常。它通过发送 ICMP（Internet Control Message Protocol）回显请求报文，并等待目标主机回复回显响应报文来判断网络连接的状态。


### (3.2) 连接机器人

使用以下命令在电脑终端中连接机器人 并按照提示输入密码：

```bash
ssh <username>@<robot_ip>
```
例如：

```bash
ssh unitree@192.168.1.137
```
>如果是第一次连接，可能会提示是否信任该主机，输入 `yes` 并回车即可。

<section id="run"></section>

## 🦾 (4) Run - 启动机器人端程序

### (4.1) Terminal 1， 启动XRoboToolkit-PC-Service
在 SSH 连接的终端中，执行以下命令启动 XRoboToolkit-PC-Service：
```bash
bash /opt/apps/roboticsservice/runService.sh
```

### (4.2) Terminal 2， 启动GR00T-WholeBodyControl
新建SSH连接的终端中，进入部署目录并执行部署脚本：
```bash
cd /home/unitree/workspace/sonic/GR00T-WholeBodyControl/gear_sonic_deploy
bash deploy.sh --input-type zmq_manager real
```
> 输入密码后，等到终端出现 `Init done` 的提示后。

> `--input-type zmq_manager` 表示使用 ZMQ Manager 作为输入源，`real` 表示在真机上运行。


### (4.3) Terminal 3， 启动 PICO Teleop Streamer
在另一个 SSH 终端中，进入部署目录并执行：

```bash
cd /home/unitree/workspace/sonic/GR00T-WholeBodyControl
source .venv_teleop/bin/activate
python gear_sonic/scripts/pico_manager_thread_server.py --manager
```

<section id="PICO"></section>

## 🎮 (5) PICO 端设置
> 如有疑问请参考 [PICO 官方文档](https://nvlabs.github.io/GR00T-WholeBodyControl/getting_started/vr_teleop_setup.html) 或者联系维护者。

### (5.1) 连接PICO到局域网 （默认已经连接）
1. 打开PICO，进入设置界面。
2. 选择“Wi-Fi”，连接到与机器人相同的网络。

### (5.2) 佩戴并且设置tracker
1. 佩戴PICO以及tracker，进入VR界面。
2. 打开PICO 资源库点击人体追踪传感器
3. 根据提示完成tracker的配对和校准。

### (5.3) 启动PICO Teleop Client
1. 打开PICO资源库，选择未知来源
2. 点击 xrobotoolkit 启动远程控制界面
3. 在PC service下输入机器人IP地址（例如：192.168.1.137）
4. 勾选`Head`， `Controller`，PICO Motion Tracker下`mode`选择 `Full-body`
5. Data & Control下，勾选`Send`
6. 点击connect，等待连接成功提示，status显示working即可。

<section id="Control"></section>

## 🕹️ (6) Control - 控制机器人
> 确认机器人周围环境安全后，再开始后续步骤
> 
### (6.1) 使能机器人
1. 尽量保证和机器人同向站立，双手自然下垂。
2. 同时按下控制器上的 `A+B+X+Y` 按钮, 会启动whole-body control po licy，机器人使能并进入站立状态。
3. 同时按下控制器上的 `A+X` 按钮, 会进入到遥控模式，此时可以通过PICO控制机器人的运动。

> 在遥操模式下，同时再次按下 `A+X` 按钮会退出遥控模式，尽量在机器人静止时切换模式，避免在运动过程中切换，最好将连接在支架上进行模式切换。

> ❗️ 注意：如果在遥控模式下，机器人出现异常动作或失控，立即按下`A+B+X+Y`急停按钮停止机器人，此时机器人会失能并砸向地面，一定要在连接支架的情况下进行此操作，并检查周围环境安全。

### (6.2) 停止机器人
1. 将机器人连接在支架上，确保周围环境安全。
2. 同时按下控制器上的 `A+B+X+Y` 按钮, 会停止机器人并失能，机器人会自然倒地。


<section id="Info"></section>

## 📄 Useful Info - 推荐资料

**官方入口**

- GitHub 仓库：[NVlabs/GR00T-WholeBodyControl](https://github.com/NVlabs/GR00T-WholeBodyControl)
- 官方文档：[GR00T-WholeBodyControl Docs](https://nvlabs.github.io/GR00T-WholeBodyControl/)
- GEAR-SONIC 项目页：[Website](https://nvlabs.github.io/GEAR-SONIC/)
- GEAR-SONIC 模型：[Hugging Face](https://huggingface.co/nvidia/GEAR-SONIC)
- SONIC 论文：[arXiv](https://arxiv.org/abs/2511.07820)
- Live web demo：[GEAR-SONIC Demo](https://nvlabs.github.io/GEAR-SONIC/demo.html)

**官方文档中优先看的页面**

- Installation Guide：[Deployment Installation](https://nvlabs.github.io/GR00T-WholeBodyControl/getting_started/installation_deploy.html)
- Quick Start：[Sim2Sim and Real Robot](https://nvlabs.github.io/GR00T-WholeBodyControl/getting_started/quickstart.html)
- Training Installation：[Installation Training](https://nvlabs.github.io/GR00T-WholeBodyControl/getting_started/installation_training.html)
- Training Guide：[SONIC Training](https://nvlabs.github.io/GR00T-WholeBodyControl/user_guide/training.html)
- VR Teleoperation：[VR Teleoperation Setup](https://nvlabs.github.io/GR00T-WholeBodyControl/getting_started/vr_teleop_setup.html)

<section id="credits"></section>

## 🏷️ Credits - 致谢与来源

本手册基于 NVIDIA 官方开源仓库与文档整理，用于 CPS Lab 内部入门导读：

- [NVlabs/GR00T-WholeBodyControl](https://github.com/NVlabs/GR00T-WholeBodyControl)
- [GR00T-WholeBodyControl Documentation](https://nvlabs.github.io/GR00T-WholeBodyControl/)
- [GEAR-SONIC](https://nvlabs.github.io/GEAR-SONIC/)

具体安装命令、版本要求和安全提示请以官方文档为准。真机实验必须遵守实验室安全规范，并在具备急停和现场监督的条件下进行。

### Maintainer - 维护者

>本文档由 `Evan` 整理和维护，用于 CPS Lab 内部 GR00T-WholeBodyControl 机器人使用说明。
> - Institution: CPS Lab, The University of Hong Kong
> - Last Updated: `2026-04-15`
> 
> 如有任何疑问或需要帮助，请联系维护者或参考官方文档。祝大家使用愉快，安全第一!


