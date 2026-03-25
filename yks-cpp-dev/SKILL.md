---
name: yks-cpp-dev
description: 当我需要编写、重构或审查 YKS 机器人的 C++ 控制代码、硬件通信（Socket/通信协议）和传感器驱动(Bms/Sbus)时，请使用此技能为我提供架构与代码规范指导。
authors: Kerchat
version: 1.0.0
---

# YKS 机器人全局 C++ 开发与架构规范技能

当执行与机器人底层控制和硬件通讯有关的代码任务时，请必须遵循以下开发规范：

## 1. 架构与风格约束
- **命名规范**：类名使用 PascalCase (例: `BmsHandler`, `JoyStickHandler`)，成员变量建议使用下划线结尾或 `m_` 开头以便区分；处理物理单位的变量必须带后缀（例如 `voltage_mv`, `angle_rad`）。
- **指针与内存**：处理底层硬件通讯时（特别是网络及串口IO），严禁内存泄露。优先使用 `std::unique_ptr` 等智能指针管理生命周期。
- **错误处理**：凡是涉及外部输入的数据，在使用前必须进行边界校验和异常捕获，避免控制发散导致机器人失控。

## 2. 硬件模块协同 (Handlers)
- **手柄与遥控 (JoyStick / SBus)**：对于突发异常断连、信号丢失，必须有一套 Failsafe（故障安全）机制（如将电机速度指令归零）。
- **电池管理 (BMS)**：传感器数据读取应保证非阻塞，不要因为锁而影响全局定时循环(Control Loop)的实时性。
- **网络通讯 (Socket)**：需做好大端/小端的字节序处理，并考虑 TCP/UDP 粘包问题及数据拆分校验。

## 3. 编译与构建原则 (CMake)
- 指导用户新增源文件时，提醒他们务必在对应的 target (`CMakeLists.txt` 中的 `YKS_SDK` 或其他库) 中同步添加。
- 引导用户使用 Modern CMake 语法 (`target_link_libraries`) 链接第三方库。
