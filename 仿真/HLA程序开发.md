# HLA程序开发

使用pRTI所给starterkit

## 实例

![image-20220226093657689](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226093657689.png)

这个燃油经济联邦用于评估不同制造商的汽车使用有限的燃油可以行驶多远，每个汽车制造商都提供一个仿真器来模拟选定的模型，上述就是联邦的示意图。

A品牌模拟他们的319和440d模型，B品牌模拟他们的4-8和MountainCruiser模型。以后可能会有更多的汽车模拟器，所以设计不会被锁定在这些特定的汽车上。有一个称为 Master 的管理联邦成员，操作员可以从中设置场景并开始和停止模拟。还将有一个名为 MapViewer 的联邦成员，它显示带有汽车及其燃料状态的地图。

## 用于仿真的HLA模块

通常将 HLA 功能添加到单独模块中的新模拟或现有模拟中。我们选择将其称为 HLA 模块。

![image-20220226094033393](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226094033393.png)

开发适合所有模拟的单个 HLA 模块可能很诱人。这样的 HLA 模块将订阅 FOM 中的各种数据。这适用于较小的联邦，但不幸的是会限制您的联邦的可扩展性。最佳实践是开发一个 HLA 模块，该模块只关注一个特定联邦（或联邦类别）需要发布和订阅的数据。

本教程介绍如何开发 HLA 模块。 HLA 模块根据 HLA 标准执行对 Runtime Infrastructure (RTI) 的调用。您还需要编写将模拟内部与 HLA 模块连接起来的代码。请注意，对于某些模拟，您可以选择将 HLA 模块设为可选，这意味着您的模拟可以在有和没有 HLA 连接的情况下运行。

## CRC和LRC

RTI由两种类型的软件组成，如下图所示

![image-20220226094425426](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226094425426.png)

1. The Local RTI Component（LRC）：安装在每台计算机上的本地库。对于C++来说，这是一个dll文件。对java来说，这是一个jar包。
2. The Central RTI Component（CRC）：协调联邦执行并跟踪加入的联邦成员的应用程序。 CRC 是一个需要在具有已知地址的计算机上启动的程序。您需要在任何联邦成员加入联邦之前启动 CRC。 CRC 用户界面是了解联邦概况的好地方。

## 连接与加入

当仿真是联邦的一部分时，它被称为联邦成员。联邦成员需要做的**第一件事**是调用 RTI（实际上是 LRC）以连接到 RTI。

然后它需要**加入一个联邦执行**，它总是有一个唯一的名称，例如 MyAirSimulation 或 TrainingSession44 。可能同时存在多个联合执行，但典型的模拟只会加入一个联合执行。如果不存在联合执行，则可能需要创建它。这些是我们需要用来创建联合执行并加入它的服务：

```c++
rti = RTIambassadorFactory.createRTIambassador();
rti.connect(federateAmbassador, IMMEDIATE, “MySettingsDesignator”)
rti.createFederationExecution(“MyFederation”, “MyFOM”)
rti.joinFederationExecution(“MyName”, “MyFederateType”, “MyFederation”)
```

## 查看RTI

通过查看 RTI 的用户界面，很容易验证联邦执行是否已创建以及联邦是否已加入。

![image-20220226095225645](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226095225645.png)

## 退出和断开

在程序结束时需要退出联邦，销毁联邦执行，最后断开连接。将使用以下服务：

```c++
rti.resignFederationExecution(CANCEL_THEN_DELETE_THEN_DIVEST)
rti.destroyFederationExecution()
rti.disconnect()
```

## 调用和回调

将有对 RTI 的调用和从 RTI 到联邦成员的回调。您将使用一个名为 RTI 大使（RTIambassador）的对象来调用 RTI。它是使用 RTIambassadorFactory 创建的。您需要提供一个联邦大使，如上面的 Connect 调用中所示，RTI 将对其进行回调。

![image-20220226095413378](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226095413378.png)

有两种回调模型： 立即，这意味着 RTI 将在收到回调后立即在单独的线程中传递它们。另一个模型称为 Evoked，这意味着您显式调用 Evoke 方法来传递回调。我们建议使用立即模式。不太高级的开发人员可能会选择使用 Evoked 模型，因为它只使用一个线程进行调用和回调。

## HLA 服务：连接

该服务将您的模拟连接到 RTI。此连接的参数（如网络地址）通过称为本地设置指示符的参数（字符串）、配置文件、环境变量或这些参数的组合提供。为获得最佳灵活性，请避免将此参数硬编码到您的程序中。您还需要指定是否应立即执行来自 RTI 的回调，或者是否应显式调用它们。小心处理 Connection Failed 异常，这通常发生在指定网络地址没有可用的 RTI 时。如果 Connect 服务引发异常，则在成功连接之前尝试任何其他 HLA 服务是没有用的。

## HLA 服务：创建联合执行

此服务创建具有特定名称的联合执行。您可以每次都尝试创建联合执行，因为尝试创建已经存在的联合执行没有重大问题。唯一的结果将是一个表示它已经存在的异常，在许多情况下可以安全地忽略它。所以一定要捕获 The specified Federation Execution already exists 异常并静默处理。您还需要提供有效的 FOM，实际上是一个或多个 FOM 模块的列表。请务必注意无法找到 FOM 模块以及无效 FOM 模块的异常。还有一些额外的参数，例如将在后面的章节中描述的时间表示。

## HLA 服务：加入联合执行

此服务使您的模拟成为联合执行的成员。您需要提供要加入的联合执行的名称。您还应该提供您的联邦成员的姓名和类型。当联邦名称已被另一个联邦使用时，请务必处理异常。还有一些附加参数将在后面介绍。

## HLA 服务：退出联合执行

此服务从联邦中退出联邦，即您的模拟将不再是联邦执行的成员。它需要一个指令来指定应该发生什么，例如，联邦已创建但未删除的对象。除非您有特殊要求，否则建议您使用 CANCEL_THEN_DELETE_THEN_DIVEST 指令。

## HLA 服务：销毁联合执行

此服务破坏联合执行。每次辞职时，您都可以尝试破坏联合执行。如果联邦中还有其他联邦，则此操作将失败，并且将抛出联邦加入异常，在许多情况下可以安全地忽略该异常。

## HLA 服务：断开连接

此服务断开您的联邦与 RTI 的连接。这是您致电 RTI 的最后一项服务。在此呼叫之后，您无法进行任何 RTI 呼叫，直到您执行了连接呼叫。

## Connect，Create and Join

The following picture shows the most important part of the Pitch pRTI user interface:

![image-20220226150441750](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226150441750.png)

### Study and verify the start-up

遵循这些步骤 

1. 启动 Pitch RTI Free (CRC) 
2. 请注意，在 RTI 中没有联邦和可见的联邦
3. 启动主联邦。查看 RTI 用户界面。您应该会看到名为“Fuel Economy”的联邦执行以及“Master”联邦。
4. 启动其他三个联邦（CarSimC、CarSimJ 和 MapViewer）并验证它们是否按预期加入联邦。
5. 一个接一个地终止联邦，除了Master，并确认他们相应地从联邦中消失。
6. 最后终止主联邦并验证它从联邦中消失并且联邦执行消失。
7. 关闭 RTI。

### A look at source code and APIs

要研究燃油经济联邦的 C++ 和 Java 源代码，请使用以下开始菜单项打开源代码目录。请注意，不同联邦的源代码存储在不同的子目录中。

HLA Evolved -> Fuel Economy-> Source code

要研究 HLA Evolved 的 C++ 和 Java API，请使用以下开始菜单项：

Pitch pRTI Free -> Documentation -> HLA Evolved Doxygen
Pitch pRTI Free -> Documentation -> HLA Evolved JavaDoc

1. 打开 CarSim 源代码（您选择的 C++ 或 Java）。看看下面的类：

   CarSimJ: se.pitch.hlatutorial.carsim.hlamodule.HLAinterfaceImpl

   CarSimC: /source/HLAmodule.cpp

   找到执行 Connect、Create Federation 和 Join Federation Execution 调用的代码。请注意异常处理，为清楚起见，本教程正文的伪代码中未包含该异常处理。

2. 在 HLA Evolved API（上面的 Doxygen 和 JavaDoc）中找到并研究这三个服务。

3. 如果您有可用的 IEEE 1516-2010.1 规范，请阅读标准中有关这些服务的更多信息。

4. 对 Resign Federation Execution、Destroy Federation Execution 和 Disconnect 三个服务执行步骤 1-3。

## 为交互开发FOM

![image-20220226153701962](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226153701962.png)

在左侧窗格中，您可以打开和关闭项目中的不同 FOM 模块。通过双击左侧窗格中的项目，例如Identification Table，您将打开此表。

要打开 FOM，请启动 Pitch Visual OMT Free 并在开始对话框中选择 Fuel Economy FOM 项目。

### A look at interactions

1. 打开 FOM 并找到 Fuel Economy FOM 模块
2. 检查Identification table的内容
3. 检查interaction classes。使用小三角打开和关闭它们的参数列表。双击 LoadScenario 交互以检查它。

4. 转到数据类型，查看简单数据类型。在这里您应该检查 FuelInt32。
5. 现在进入 MIM FOM 模块。这个模块包含很多在 HLA 标准中预定义的东西。
6. 检查 MIM 中的各种数据类型。请注意，所有预定义项目都以 HLA 为前缀。
7. 现在尝试修改 FOM 的某些部分。使用参数 MaxMoneyAmount 添加您自己的交互 Refuel。添加数据类型 EuroType32。
8. 请注意，您无法使用免费版本的 Pitch Visual OMT 保存修改后的 FOM。
9. 关闭 Pitch Visual OMT

## 发送和接收交互

### Study and verify declarations

1. 启动 RTI (CRC)
2. 启动 Master federate。查看 pRTI 用户界面。选择 Master federate 并查看 Declarations
3. 您应该看到 Start、Stop 和 LoadScenario 交互已发布。应订阅 ScenarioLoaded 和 ScenarioLoadFailure 交互。
4. 启动 CarSimC federate。查看 pRTI 用户界面。选择 CarSimJ federate 并查看 Declarations
5. 您应该看到 ScenarioLoaded 和 ScenarioLoadFailure 交互已发布。应订阅 Start、Stop 和 LoadScenario 交互

### A look at source code and APIs

5. 打开 CarSim 源代码（您选择的 C++ 或 Java）。查看以下类： CarSimJ：se.pitch.hlatutorial.carsim.hlamodule.HLAinterfaceImpl CarSimC：/source/HLAmodule.cpp 找到执行 Get Interaction Class Handle 和 Send Interaction 调用以及实现 Receive Interaction 回调的代码。请注意异常处理，为清楚起见，本教程正文的伪代码中未包含该异常处理。
6. 在 HLA Evolved APIs（上面的 Doxygen 和 JavaDoc）中找到并研究这三个服务。
如果您有可用的 IEEE 1516-2010.1 规范，请阅读标准中有关这些服务的更多信息。

## 为对象类开发 FOM

### A look at object classes

1. 打开 FOM 启动 Pitch Visual OMT Free 并在启动对话框中选择 Fuel Economy FOM 项目。
2. 检查对象类树。实际上只有两个类：Object Root 和 Car。使用小三角形打开和关闭 Car 类的属性列表。您可能希望增加列的宽度以读取全名。双击 Car 类来检查它。
3. 转到数据类型并查看枚举数据类型。在这里您应该检查 FuelTypeEnum32。
4. 转到数据类型并查看固定记录数据类型。在这里您应该检查 PositionRec。请注意它是如何建立在 AngleFloat32 数据类型之上的。你能找到那个数据类型的定义吗？ 5. 现在尝试修改 FOM 的某些部分。添加您自己的具有 Name、Position、HasDiesel 和 HasPetrol 属性的类 FuelStation。添加更多数据类型。
6. 请注意，您无法使用免费版本的 Pitch Visual OMT 保存修改后的 FOM。
7. 关闭 Pitch Visual OMT

## 注册和发现对象实例

### Study and verify declarations

1. 启动 RTI (CRC) 
1. 启动 Master federate。然后启动 CarSimC 和 MapViewer 联合。查看 pRTI 用户界面。选择 CarSimC federate 并查看 Declarations
1. 您应该看到 Car 对象类（包括所有属性）已发布。
4. 启动 MapViewer 联盟。查看 pRTI 用户界面。选择 MapViewer federate 并查看 Declarations 5。您应该看到 Car 对象类已订阅

### 研究和验证注册和发现服务

1. 在 Master federate 中，给出一个 LoadScenario 命令。
2. 查看燃油经济性联合会的 pRTI GUI。查看联邦的对象实例。您现在应该看到已在联邦中注册的 Car 实例
2. 查看 CarSimC 联邦中的 pRTI GUI。查看 Known Instances 并验证汽车是否由该联邦成员知道（实际上是创建的）。
4. 查看 MapViewer federate 的 pRTI GUI。查看 Known Instances 并验证这些汽车是否已被该联邦成员知道（实际上已发现）。
5. 关闭联邦

### A look at source code and APIs

1. 打开 CarSim 源代码（您选择的 C++ 或 Java）。

   CarSimJ：se.pitch.hlatutorial.carsim.hlamodule.HLAinterfaceImpl

   CarSimC：/source/HLAmodule.cpp

   找到执行注册对象实例调用和实现发现对象实例回调的代码。请注意异常处理，为清楚起见，本教程正文的伪代码中未包含该异常处理。

2. 在 HLA Evolved APIs（上面的 Doxygen 和 JavaDoc）中找到并研究这三个服务。
3. 如果您有可用的 IEEE 1516-2010.1 规范，请阅读标准中有关这些服务的更多信息。

## 更新对象

研究用于发送对象属性更新以及在订阅联邦中反映这些属性的服务。

### Study and verify declarations

在第一个实验中，您学习了如何启动和运行联合。作为本实验室的一部分，您可以在 MapViewer（订阅者）中看到由 CarSims（发布者）模拟的汽车。

1. 启动 RTI 和所有的联邦。加载场景并开始模拟。
2. 确认您可以在 MapViewer 中看到汽车移动。
3. 在 pRTI 用户界面中：选择 CarSimC federate 并打开 RTI 调用和回调的跟踪。您可以考虑在几秒钟后关闭跟踪以限制跟踪打印输出的数量。
4. 查看 CarSimC 窗口。研究联邦对更新属性值服务的调用。
5. 在 pRTI 用户界面中：选择 MapViewer federate 并打开 RTI 调用和回调的跟踪。您可以考虑在几秒钟后关闭跟踪以限制跟踪打印输出的数量。
6. 查看 MapViewer 窗口。研究 RTI 对联邦进行的反映属性值回调。
7. 终止联合。

### A look at source code and APIs

1. 打开 CarSim 源代码（您选择的 C++ 或 Java）：

   CarSimJ: se.pitch.hlatutorial.carsim.hlamodule.HLAinterfaceImpl

   CarSimC: /source/HLAmodule.cpp

   找到执行更新属性值调用和反映属性值回调的代码。请注意异常处理，为清楚起见，本教程正文的伪代码中未包含该异常处理。

2. 在 HLA Evolved API（上面的 Doxygen 和 JavaDoc）中找到并研究这三个服务。

3. 如果您有可用的 IEEE 1516-2010.1 规范，请阅读标准中有关这些服务的更多信息。

## 运行分布式联邦

以下是对有兴趣尝试分布式联合的任何人的高级练习。它假定您在网络上至少有两台计算机。

### 用两台电脑运行

我们将调用计算机 1 和计算机 2。为简单起见，您可能需要禁用计算机上的 Windows/Linux 防火墙。
我们将按如下方式分发应用程序：

Computer 1: Central RTI Component, Master, CarSimC
Computer 2: CarSimJ, MapViewer

1. 确保在两台计算机上都安装了 Pitch pRTI Free 和 HLA Evolved Starter Kit。
2. 我们将使用计算机 1 作为中央 RTI 组件。在这台计算机上启动 Pitch pRTI Free。记住这台计算机的 IP 地址，例如 192.168.1.23。该地址可以使用“ipconfig”（Windows）或“ifconfig”（Linux）命令定位。
3. 启动计算机 1 上的 Master 和 CarSimC，并验证它们是否加入了 RTI。
4. 修改计算机 2 上的以下文件：
   1. CarSimJ 配置
   2. MapViewer config
5. 在上述两个文件中，将 CRChost 值修改为计算机 1 的 IP 地址。结果行可能是例如 
   1. CRChost=192.168.1.23
6. 在计算机 1 上启动 CarSimJ 和 MapViewer 联盟。
7. 签入所有四个联邦成员都加入的 Pitch pRTI Free 用户界面。
8. 使用 Master federate 开始模拟。

### 运行更多计算机

您可以将所有联邦移动到不同的计算机。唯一的要求是您必须安装 RTI，因为每个联邦都需要在该安装中使用 RTI 库。您还需要修改每个联邦成员的配置文件，以便它连接到 CRC。

### 使用 iPad/Android/iPhone 的 Web 视图

阅读 Pitch pRTI 用户指南，了解如何为 Pitch pRTI 启动 Web 视图 通过 Web 视图，您可以使用常规 Web 浏览器或平板电脑 (iPad/Android) 甚至手机连接到 RTI 并管理联盟。

## 寿司餐厅联合会

首次引入 HLA 时，其中一个更高级的样本是餐厅联盟。它使用了大量的 HLA 服务。该联盟最初是为 HLA 1.3 开发的，现已迁移到最新版本的 HLA。 Fred Kuhl、Judith Dahmann 和 Richard Weatherly 所著的《创建计算机模拟系统 - 高级架构简介》一书中提供了对联邦的完整描述，ISBN：9780130225115。
请注意，此联合最初是使用 Java 和 HLA 的旧版本开发的。这意味着某些代码可能不会使用我们今天认为的最佳实践。尽管如此，使用的丰富的 HLA 功能集仍然使它成为一个有趣的联盟。

### Overview of the federation

该联合会模拟了一家经典的寿司餐厅，厨师在那里准备寿司，并将它们放在运送给顾客的船上。

![image-20220226155152018](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220226155152018.png)

### How to run the federation

此联合作为 HLA Evolved 入门套件的一部分安装。

On the Start Menu, locate the following items:
Start -> HLA Evolved -> Sushi Restaurant -> Production
Start -> HLA Evolved -> Sushi Restaurant -> Transportation
Start -> HLA Evolved -> Sushi Restaurant -> Consumption
Start -> HLA Evolved -> Sushi Restaurant -> Viewer
Start -> HLA Evolved -> Sushi Restaurant -> Manager

要运行联邦，请启动 pRTI Free，然后启动上述联邦。请注意，Manager federate 应该是您启动的最后一个。
源代码位于使用以下命令打开的目录中：

Start -> HLA Evolved -> Restaurant -> Source Code

要检查 FOM，请启动 Pitch Visual OMT Free 并选择 Sushi Restaurant 项目。

### Production Federate

这个联邦管理一个产生寿司对象的 Chef 对象的集合。当船靠近厨师时，厨师可以将寿司送走并放在船上。

### Transportation Federate

运输管理船对象的集合。当一艘船靠近制作寿司的厨师时，生产联盟可以让运输联盟获得寿司的所有权。然后将寿司装载到船上并运送给潜在的客户

### Consumption Federate

消费联盟为许多消费者建模。当装有寿司的船靠近顾客时，它可以接管寿司的所有权并吃掉它。

### Manager Federate

管理者联盟跟踪联盟中的联盟并负责设置同步点（例如 ReadyToRun）。这也是保持联邦以所需速度前进的时间。

### Viewer Federate

查看器联盟以图形视图显示厨师、船只、顾客和寿司。联合会模拟餐厅的活动。