# HLA仿真程序设计基础

HLA仿真程序设计即是在联邦设计的基础上通过合理地使用RTI服务来达成仿真联邦的目的。

要理解基于HLA的仿真程序设计，首先必须理解联邦执行的生命周期，同时还要掌握联邦执行的整个生命周期中联邦执行的各种状态以及联邦成员、联邦执行、RTI这三者间的关系。

## 联邦执行的生命周期

### 创建联邦执行

创建联邦执行时联邦执行生命周期的第一个阶段，联邦执行实际上是RTI根据FED文件的内容及有关的联邦细节数据，为联邦成员间的交互而创建的一个虚拟世界。它是一个动态的、有生存期的概念，联邦执行的管理和维护由RTI来完成。（我的理解，联邦执行和联邦的关系就如同进程和程序的关系）

![image-20220225150623065](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225150623065.png)

在这一状态下，RTI和联邦成员均已启动，但联邦执行并不存在，即联邦成员间进行交互的虚拟世界还没有建立。成员代码的启动仅仅标识仿真应用已经开始运行，但联邦成员和RTI之间并未产生联系。

联邦执行的创建是由联邦成员向RTI发送请求来完成的。RTI接收到请求后，首先判断需要创建的联邦执行是否存在。如果不存在，则RTI创建该联邦执行。否则RTI报告异常。正常情况下，联邦执行总是由第一个提出创建联邦执行请求的联邦成员创建。在RTI创建联邦执行时，RTI将根据系统配置在指定路径中搜索RTI初始化数据文件，即RID文件。

![image-20220225150906472](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225150906472.png)

请求创建联邦执行时，联邦成员需要将联邦执行数据文件告知RTI。联邦执行创建后，成员间交互的虚拟世界已经存在，但此时该虚拟世界中并没有成员实例，也没有对象实例。

联邦成员可能已经建立了构建仿真实体的对象，但各联邦成员的对象之间并没有交互，而且这些对象均没有在联邦执行中注册。

### 联邦执行存在

联邦执行创建后进入联邦执行存在阶段。

该阶段可分成三个子阶段。

1. 第一个成员加入
2. 仿真逻辑执行
3. 最后一个成员退出

1.第一个成员加入

联邦执行创建后，联邦成员将通过激活RTI服务，向RTI申请加入联邦执行，RTI根据联邦成员提出的申请，在相应的联邦执行中注册一个联邦成员的实例，并向联邦成员返回该成员实例的句柄。

![image-20220225151223931](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225151223931.png)

2.仿真逻辑执行

主要任务是参与联邦执行的联邦成员，根据仿真模型更新自身状态参与联邦交互。第一个成员加入后，根据联邦设计所确定的仿真逻辑，联邦执行中会不断有联邦成员加入，而且加入联邦执行的联邦成员将通过RTI服务完成公布对象类、注册对象类的实例、订购对象类等交互操作。

![image-20220225151245967](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225151245967.png)

3.最后一个成员退出

联邦执行的生命周期内，当某个联邦成员满足退出联邦执行的条件时，该联邦成员将申请退出联邦执行，RTI将在联邦执行中撤销该联邦成员的成员实例，并根据联邦执行当前的状态删除部分或全部与该成员实例相关的数据结构。当联邦执行中最后一个联邦成员退出联邦执行后，联邦执行将回到6-2的状态，即联邦执行为空。

### 撤销联邦执行

这是联邦执行生命周期的最后一个阶段，当联邦成员退出联邦执行后，它将激活RTI服务，请求撤销联邦执行。若联邦执行中仍有其他联邦成员的成员实例，则RTI将报告异常。正常情况下，联邦执行最终是由最后一个退出联邦执行的联邦成员来撤销。

## 联邦成员的程序框架

联邦成员的程序框架一般分为两类，一类是单线程结构，另一类是双线程结构。

![image-20220225152905926](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225152905926.png)

![image-20220225152957269](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225152957269.png)

### RTIambassador和FederateAmbassdor

这两个是RTI类的两个成员类。分别包含了两个头文件“RTIambServices.hh”和“federateAmbServices.hh”。

第一个定义了必须由RTI提供的所有服务的函数原型，这些服务由联邦成员调用，RTI提供服务。

第二个定义了所有回调函数的原型，但这些回调函数均定义为纯虚函数，回调函数必须由联邦开发人员根据联邦成员的具体交互需要有选择地实现，每个联邦成员实现的回调函数都是这个文件中定义的回调函数（RTI to Federation）的子集。

RTI 1.3-NG定义了一个空的成员大使类NullFederateAmbassador，该类继承了RTI::FederateAmbassador类的功能，它将RTI::FederateAmbassador类中的每一个纯虚成员函数都重载为空的虚函数。

![image-20220225153954283](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225153954283.png)

图6-7表明，在联邦运行过程中，每当联邦成员需要向联邦执行发送数据时，它需要调用由RTIambassador类的对象实例提供的服务。当联邦执行中有数据需要传给联邦成员时，RTI调用相应的回调函数并将需要传递的数据**以回调函数参数**的形式传递给指定的联邦成员。由于回调函数由联邦成员实现，因此联邦成员可以根据自己的需要决定对这些数据的处理方法。

## HLA仿真程序实例

### FightGame

一群学生分成两组完打仗的游戏。规则：每个学生首先用自己的午餐费（初始值为20）购买弹药（泥巴）。游戏过程中，每当自己被对方命中时，其污点数将增加10，同时其代表生命力的指数（清洁度）将减少10。每个学生生命力指数的初始值均为100，一旦某个学生的生命力指数降为0，那么他将推出游戏。在游戏过程中，允许学生叛变，而且在作战过程中，敌对双方在频道一致的情况下可以进行无线电通信。

为了模拟整个游戏过程，特设计了一个包含两个成员的联邦，联邦名称为FightGame，联邦中每个成员模拟一组10个学生。由于对抗双方的游戏规则相同，因此两个成员的仿真模型是一样的。我们只需设计一个联邦成员代码。仿真过程中，它以不同的身份加入联邦执行，即可代表对抗双方的两个联邦成员，从而简化了联邦开发。

### FigheGame的对象类

整个FightGame仿真涉及到的对象类只有一个，即Student。联邦执行中，需要用三个属性来标明其对象实例的状态，因此在FightGame的FED文件中，对象类Student的定义如下：

```c++
(class Student
	(attribute LunchMoney reliable timestamp);;午餐费
	(attribute AmmoAmount reliable timestamp);;弹药数量
	(attribute Cleanliness reliable timestamp);;清洁度
)
```

对象类详细内容略。

联邦执行创建时，联邦成员需要将FED文件名传递给RTI，RTI将根据FED文件的内容为每一个对象类、交互类、对象类属性以及交互参数分配句柄值。联邦成员可以通过RTI服务获取这些句柄值。这些句柄值一旦分配，则在整个联邦执行的生命周期内都将不会改变。

### FightGame的交互类

一个是代表开火设计的交互类Splat

另一个代表通信的交互类Communication

```c++
(class Splat reliable timestamp
	(parameter EnsuingMess)
 	(parameter Target)
)
(class Communication reliable timestamp FightGameRadioSpace
	(parameter Message)
)
```

Splat交互类有两个参数，EnsuingMess表示每次开火的价值（对方的污点数每增加10，同时生命力指数减少10），Target表示开火的目标。

Communication交互类只有一个参数，即表示通信内容的参数Message。

另外，在FED文件中，交互类Communication和路径空间FightGameRadioSpaces绑定在一起。

对HLA交互类的处理方式有两种，一种是为每个HLA交互类设计对应的C++类并建立C++对象。另一种是不设计。

程序执行过程中，C++类Splat和Communication都有自己的C++对象，它们分别完成了对HLA交互类Splat和Communication的公布、订购及发送任务。

从联邦执行的角度讲，HLA的对象类Student和交互类Splat、Communication是不一样的。联邦成员可以在联邦执行中注册一个或多个对象类Student的对象实例。但联邦成员不能再联邦执行中注册交互实例。

联邦执行中，交互实例代表一个瞬间的操作。RTI在收到一个成员发来的交互实例时，将建立一个临时的数据结构以保存交互实例的内容。

### FightGame的路径空间

FightGameRadioSpace

```c++
(spaces
 (space FightGameRadioSpace
 	(dimension Channel)
 )
)
```

该路径空间定义了一个维Channel，它表示无线电通信的频道。FightGame共设置了10个频道，编号为1~10.只有当通信双方使用一致的频道时才可以进行通信。

### FightGame的文件结构

MainLoop.cpp：主程序文件，定义了全局变量并实现了仿真循环

Student.h：Student对象类的头文件，定义了Student对象类的变量和成员函数

Student.cpp：Student对象类的实现文件，实现了Student对象类中的主要函数

Splat.h：Splat对象类的头文件，定义了Student对象类的变量和成员函数

Splat.cpp：Splat对象类的实现文件，实现了Student对象类中的主要函数

Communication.h：Communication对象类的头文件，定义了Student对象类的变量和成员函数

Communication.cpp：Communication对象类的实现文件，实现了Student对象类中的主要函数

FightGameFederateAmbassador.h：定义了FightGame中的成员大使类

FightGameFederateAmbassador.cpp：实现了FightGame中的成员大使所定义的回调函数

### FightGame的所有权管理

FightGame中学生的叛变是通过所有权管理来实现的。

FightGame联邦内，代表对抗双方的两个联邦成员各自维护着己方所有学生的状态，同时也保存着对方学生的实例，但它只能更新己方学生的状态。判断某个学生属于己方还是对方的依据是看该学生实例的属性的所有权是归谁所有。只有当学生的所有实例属性都属于己方时才认为该学生属于己方。如果只有部分属于己方，则认为该学生实例属性的所有权正在转移。

### FightGame的执行过程

![image-20220225162943317](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225162943317.png)

![image-20220225162950756](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220225162950756.png)

