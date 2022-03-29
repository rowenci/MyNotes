# RTI（Run-time Infrastructure）

## 概述

### 主要作用

1. 具体实现了HLA接口规范。为了实现联邦内部各个联邦成员之间进行高效的信息交换，HLA接口规范用文字定义了各种标准服务和接口，而RTI则用程序设计语言将这些标准的服务和接口转成了标准的RTI API函数，使基于HLA的仿真开发成为可能。
2. 它为仿真应用提供了仿真运行管理功能，比如仿真过程的开始、暂停、恢复、时间同步、仿真时钟推进等。
3. 提供了底层通信传输服务，屏蔽了网络通信程序实现的复杂性，开发人员可以很容易地实现数据的发送和接收，从而降低了分布交互仿真程序设计的复杂程度。而且这种传输机制允许各个联邦成员进行不同级别的数据过滤，可以极大地减少网络数据流量，提高仿真系统的运行速度。
4. 它是仿真功能与仿真运行管理、底层通信传输三者分离的基础，它使仿真系统具有较好的扩充性，便于实现仿真系统中各个组成部分的“即插即用”，因此各个组成部分的编程实现可以相对独立地进行，很适合于团队开发。

### RTI的体系结构模型

1. 集中式：只有一个全功能的中心节点
2. 分布式：不存在中心节点，每个仿真节点上都有自己的局部RTI，如果本地RTI不能完成联邦成员的响应请求，则会向外部的RTI服务进程协同完成
3. 层次式：集中式和分布式结合

### 通信方式

按照寻址方式划分，可以分成：点对点、组播、广播

按传输质量和效率，可以分成：Reliable（TCP）、Best effort（UDP）

### 配置文件

1. 联邦执行数据文件（Federation Execution Data，FED文件）
2. RTI初始化文件（RTI Initialization Data，RID文件）

FED文件中包含了来源于FOM中的信息，包括联邦中各个联邦成员的对象类、交互类、对象类属性、交互类参数和路径空间等数据结构信息。在使用“Create Federation Execution”服务时，需要指定FED文件所在的路径和文件名。当调用该服务后，FED文件中的信息被读取、解析，并存储在一个内部数据库中、

RID文件包含了控制RTI运行的配置参数，因此可以通过配置RID文件，使RTI满足特定的仿真应用。RTI使用环境变量RTI_RID_FILE来确定RID文件所在路径名称。

## RTI 1.3-NG组成

![image-20220224103940644](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220224103940644.png)

RtiExec是一个全局进程。主要功能是管理联邦的创建、结束以及管理多个不同的联邦。每个联邦成员通过与RtiExec的通信进行初始化，加入到相应的联邦中，并确保每一个FedExec进程拥有一个唯一的联邦名称。Rtiexec实际是一个运行程序，在运行联邦执行之前，首先必须运行它。

FedExec管理联邦成员的加入和退出。为联邦成员之间进行数据通信和协调运行提供支持。FedExec进程由第一个成功调用“Create Federation Execution”服务的联邦成员创建，每一个加入联邦的联邦成员将被分配一个唯一的句柄。FedExec也是一个运行程序。在第一个联邦成员成功加入联邦后，自动启动。

LibRTI是一个接口函数库，它为开发者提供HLA接口规范中所描述的服务，该类库包括两个主要类：RTIambassador（RTI大使）和FederateAmbassdor（联邦成员大使）RTIambassador类捆绑并实现了由RTI提供的所有服务；FederateAmbassador是一个抽象类，它定义了HLA接口规范中所有的回调函数，联邦成员通过这些回调函数从RTI中接收数据（包括其他成员传来的数据和RTI自身的状态数据）。



RTI 1.3-NG还包括两个执行文件Launch和RtiConsole。当Rtiexec和Fedex不在同一台机器上运行时，为了使RTI能正常运行，要求launch在创建Fedex进程的计算机上运行。

RtiConsole为用户提供了一个检查联邦和联邦成员的状态的应用程序，用户可以通过它来了解正在运行的RTI仿真系统的状态。其具有以下功能：

1. 列出当前所有正在运行的联邦执行的名称
2. 检查当前所有正在运行的联邦执行的状态
3. 从Rtiexec进程中注销一个联邦执行
4. 列出所有已加入联邦执行的所有联邦成员
5. 检查所有已加入联邦执行的所有联邦成员的状态
6. 强制从联邦执行中“剔除”某个联邦成员

## 联邦执行数据文件（FED）

FED文件是FOM开发的结果，是所有联邦成员间为交互目的而达成的协议。它记录了在联邦运行期间所有参与联邦交互的对象类、交互类及其属性、参数和相关的路径空间信息，另外FED文件中还记录了HLA预定义的MOM和其他一些联邦执行细节数据。

仿真运行期间，RTI将根据FED文件提供的联邦执行的细节数据创建相应的联邦直接，并在整个联邦执行生命周期内以FED文件为蓝本，协调联邦成员间的监护。

### FED文件的结构

FED文件以“Fed”开始。根据功能和作用，FED文件一般可分为五节，每一节用括号作为起止标记

1. Fedversion：RTI的版本号
2. Federation：联邦的名称
3. Objects：联邦中所有对象和管理对象模型中的对象类的声明
4. spaces：给定的联邦将使用的所有路径空间
5. interactions：联邦中所有的交互类以及管理对象模型中的交互类的声明

双分号;;右边的内容是注释

### 语法

#### Federation

(Federation <Name>)

#### Fedversion

(Fedversion v1.3)

#### spaces

(

​	(space <Name>

​		(dimension <Name>)

​		(dimension <Name>)

​		...

​	)

​	(space <Name>

​		(dimension <Name>)

​		(dimension <Name>)

​		...

​	)

)

#### Objects



(objects

​	(class objectRoot

​		(attribute privilegeToDelete reliable timestamp A)

​		(class RTIprivate)

​		(class Manager

​		...

​		)

​		(class <Name>

​			(attribute <Name> <delivery> <time order> <space>)

​			(attribute <Name> <delivery> <time order> <space>)

​			...

​		)

​		(class <Name>

​			(attribute <Name> <delivery> <time order> <space>)

​			(attribute <Name> <delivery> <time order> <space>)

​			...

​		)

​		...

​	)

)

Object Root是联邦中所有对象的基类，只有一个属性“PriviledgeToDelete”

RTIPrivate是FED文件固有的，不能被修改

Manager包括对管理对象模型中所有的对象类定义

用户定义的对象类都继承于基类“objectRoot”，而且放在基类的属性“PriviledgeToDelete”和管理对象模型所有对象类的后面。

#### interactions

(interactions

​	(class interactionRoot

​		(class RTIprivate best_effort receive)

​		(class Manager best_effort receive)

​		...

​	)

​	(class <Name> <delivery> <time order> <space>

​		(parameter <Name>)

​		(parameter <Name>)

​		...

​	)

​	(class <Name> <delivery> <time order> <space>

​		(parameter <Name>)

​		(parameter <Name>)

​		...

​	)

​	...

)

interactionRoot为所有交互类的基类

manager为MOM中的所有交互类

自定义交互类都必须继承于基类，通常放在MOM中的交互类后面

## RTI初始化文件（RID）

用来设置RTI运行的初始化参数。

按照RTI内部组件的粒度分成三类，分别是：

1. ProcessSection：设置RTI进程启动时的有关参数
2. FederationSection：设置联邦有关参数
3. FederateSection：设置联邦成员有关参数

(RTI

​	(ProcessSection

​		...

​	)

​	(FederationSection

​		...

​		(FederateSection

​			...

​		)

​	)

)

通过设置RID文件中的有关参数，可以再一个RTI运行环境中支持多个联邦的运行。其方法是在RID文件的FederationSection或FederateSection中增加与联邦名称或联邦成员名称一致的字段。

具体参数含义不再详述

