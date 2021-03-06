# 联邦开发和执行过程模型（FEDEP）

美国国防部建模与仿真办公室（DMSO）提出了开发分布交互仿真系统的软件工程方法，即联邦开发和执行过程模型FEDEP（Federation Development and Execute Process Model）。它是指导HLA分布仿真系统设计开发的基本方法。

## 引言

从高层来看，HLA开发步骤可以抽象为以下几个必须遵循的、最基本的步骤：

1. 联邦发起人（用户）和联邦开发人员必须确定联邦开发的目标，并确定为达到这些目标必须完成的工作。
2. 确定仿真应用的边界和范围，并描述仿真应用中的对象以及对象间的交互。
3. 决定仿真应用中联邦成员的组成，开发联邦对象模型（FOM），以明确联邦中各联邦成员之间信息交换的需求和各自的责任。
4. 运用程序设计语言编程实现，并进行测试以保证互操作要求得到满足。
5. 运行联邦，对输出结果进行分析。

## FEDEP模型概述

FEDEP 1.5将联邦的开发和执行过程抽象为六个基本步骤：

1. 定义联邦目标，即定义联邦开发所要达到的目标。
2. 开发联邦概念模型，即对所要仿真的真实世界进行抽象性的描述。
3. 设计联邦，这一过程确定联邦组成，并给各个联邦成员分配功能。
4. 开发联邦，这一阶段的目的是开发联邦对象模型（FOM）。
5. 集成和测试联邦，即检查和测试FOM是否达到了仿真的目标。
6. 运行联邦和分析结果。

![image-20220225145155946](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225145155946.png)

## 联邦开发和执行过程的自动化

FEDEP详细内容不再叙述。

支持FEDEP自动化的工具体系有：

1. 对象模型库（Object Model Library）：基于Web的可重用的FOM和SOM模型库。
2. 对象模型数据词典系统（Object Model Data Dictionary System）：基于Web的，用于构造FOM/SOM的标准组件库。
3. 对象模型开发工具（Object Model Development Tool）：创建FOM和SOM，并产生FED文件。
4. 联邦运行计划者工作手册工具：帮助用户计划联邦运行环境，确定硬件和网络环境，明确哪些成员需要向其他成员提供数据，哪些成员需要消耗其他成员提供的数据。
5. 联邦校核工具（Federation Verification Tool）：FVT支持联邦开发人员校验每一个联邦成员是否达到了FEPW要求的数据交换功能。
6. 联邦管理工具（Federation Manage Tool）：FMT支持联邦运行操作，它将提供关于联邦运行时的监控数据。
7. 数据收集工具（Data Collection Tool）：DCT允许用户从联邦执行中收集用户感兴趣的数据。

