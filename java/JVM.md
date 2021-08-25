# JVM

## Java运行时数据区域

![image-20200611171050225](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611171050225.png)



1.程序计数器（Program Counter Register）

当前线程所执行的字节码的行号指示器。

字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，它是程序控制流的指示器，分支、循环、跳转、异常处 理、线程恢复等基础功能都需要依赖这个计数器来完成。

每条线程都需要有一个独立的程序计数器，各条线程 之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地 址；如果正在执行的是本地（Native）方法，这个计数器值则应为空（Undefined）。

没有规定任何OOM（OutOfMemory Error）的情况的区域。

2.Java虚拟机栈（Java Virtual Machine Stack）

Java虚拟机栈（Java Virtual Machine Stack）也是线程私有的，它的生命周期 与线程相同。

虚拟机栈描述的是Java方法执行的线程内存模型：每个方法被执行的时候，Java虚拟机都 会同步创建一个栈帧[1]（Stack Frame）用于存储**局部变量表**、**操作数栈**、**动态连接**、**方法出口等信息**。每一个方法被调用直至执行完毕的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

局部变量表存放了编译期可知的各种Java虚拟机基本数据类型（boolean、byte、char、short、int、 float、long、double）、对象引用（reference类型，它并不等同于对象本身，可能是一个指向对象起始 地址的引用指针，也可能是指向一个代表对象的句柄或者其他与此对象相关的位置）和returnAddress 类型（指向了一条字节码指令的地址）。

如果线程请求的栈深度大于虚 拟机所允许的深度，将抛出StackOverflowError异常；如果Java虚拟机栈容量可以动态扩展，当栈扩 展时无法申请到足够的内存会抛出OutOfMemoryError异常。

3.本地方法栈（Native Method Stacks）

与虚拟机栈所发挥的作用是非常相似的，其区别只是虚拟机 栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的本地（Native） 方法服务。

与虚拟机栈一样，本地方法栈也会在栈深度溢出或者栈扩展失 败时分别抛出StackOverflowError和OutOfMemoryError异常。

4.Java堆（Java Heap）

Java堆（Java Heap）是虚拟机所管理的内存中最大的一块。Java堆是被所 有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，Java 世界里“几乎”所有的对象实例都在这里分配内存。在《Java虚拟机规范》中对Java堆的描述是：“所有 的对象实例以及数组都应当在堆上分配”

Java堆是垃圾收集器管理的内存区域，因此一些资料中它也被称作“GC堆”（Garbage Collected Heap，幸好国内没翻译成“垃圾堆”）。

如果从分配内存的角度看，所有线程共享的Java堆中可以划分出多个线程私有的分配缓冲区 （Thread Local Allocation Buffer，TLAB），以提升对象分配时的效率。不过无论从什么角度，无论如 何划分，都不会改变Java堆中存储内容的共性，无论是哪个区域，存储的都只能是对象的实例，将Java 堆细分的目的只是为了更好地回收内存，或者更快地分配内存。

Java堆既可以被实现成固定大小的，也可以是可扩展的，不过当前主流的Java虚拟机都是按照可扩 展来实现的（通过参数-Xmx和-Xms设定）。如果在Java堆中没有内存完成实例分配，并且堆也无法再 扩展时，Java虚拟机将会抛出OutOfMemoryError异常。

5.方法区（Method Area）

与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载 的类型信息、常量、静态变量、即时编译器编译后的代码缓存等数据。虽然《Java虚拟机规范》中把 方法区描述为堆的一个逻辑部分，但是它却有一个别名叫作“非堆”（Non-Heap），目的是与Java堆区 分开来。

《Java虚拟机规范》对方法区的约束是非常宽松的，除了和Java堆一样不需要连续的内存和可以选 择固定大小或者可扩展外，甚至还可以选择不实现垃圾收集。相对而言，垃圾收集行为在这个区域的 确是比较少出现的，但并非数据进入了方法区就如永久代的名字一样“永久”存在了。这区域的内存回 收目标主要是针对常量池的回收和对类型的卸载，一般来说这个区域的回收效果比较难令人满意，尤 其是类型的卸载，条件相当苛刻，但是这部分区域的回收有时又确实是必要的。以前Sun公司的Bug列 表中，曾出现过的若干个严重的Bug就是由于低版本的HotSpot虚拟机对此区域未完全回收而导致内存 泄漏。

根据《Java虚拟机规范》的规定，如果方法区无法满足新的内存分配需求时，将抛出 OutOfMemoryError异常。

6.运行时常量池（Runtime Constant Pool）

是方法区的一部分。Class文件中除了有类的版本、字 段、方法、接口等描述信息外，还有一项信息是常量池表（Constant Pool Table），用于存放编译期生 成的各种字面量与符号引用，这部分内容将在类加载后存放到方法区的运行时常量池中。

Java虚拟机对于Class文件每一部分（自然也包括常量池）的格式都有严格规定，如每一个字节用 于存储哪种数据都必须符合规范上的要求才会被虚拟机认可、加载和执行，但对于运行时常量池， 《Java虚拟机规范》并没有做任何细节的要求，不同提供商实现的虚拟机可以按照自己的需要来实现 这个内存区域，不过一般来说，除了保存Class文件中描述的符号引用外，还会把由符号引用翻译出来 的直接引用也存储在运行时常量池中

运行时常量池相对于Class文件常量池的另外一个重要特征是具备动态性，Java语言并不要求常量 一定只有编译期才能产生，也就是说，并非预置入Class文件中常量池的内容才能进入方法区运行时常 量池，运行期间也可以将新的常量放入池中，这种特性被开发人员利用得比较多的便是String类的 intern()方法。 
既然运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存 时会抛出OutOfMemoryError异常。

7.直接内存（Direct Memory）

直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是《Java虚拟机规范》中 定义的内存区域。但是这部分内存也被频繁地使用，而且也可能导致OutOfMemoryError异常出现，所 以我们放到这里一起讲解。

在JDK 1.4中新加入了NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区 （Buffer）的I/O方式，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆里面的 
DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了 在Java堆和Native堆中来回复制数据。

显然，本机直接内存的分配不会受到Java堆大小的限制，但是，既然是内存，则肯定还是会受到 本机总内存（包括物理内存、SWAP分区或者分页文件）大小以及处理器寻址空间的限制，一般服务 器管理员配置虚拟机参数时，会根据实际内存去设置-Xmx等参数信息，但经常忽略掉直接内存，使得 各个内存区域总和大于物理内存限制（包括物理的和操作系统级的限制），从而导致动态扩展时出现 OutOfMemoryError异常。



8.类加载器（Class Loader）

加载Class文件



总结



## 对象创建过程

Java类加载原理解析 https://blog.csdn.net/justloveyou_/article/details/72217806

加载时机与加载过程 https://blog.csdn.net/justloveyou_/article/details/72466105

类的初始化与实例化 https://blog.csdn.net/justloveyou_/article/details/72466416

## 对象怎么定位

1. 句柄方式

   引用指向堆内存中的实例数据指针和类型数据指针

   实例数据指针指向对象

   类型数据指针指向方法区的数据

2. 直接指针（HotSpot）

   引用指向堆内存中的对象

   通过在堆中的对象当中的类型数据指针，找到方法区当中的数据

![image-20200619194544816](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200619194544816.png)



## 对象怎么分配

new之后，首先会尝试在栈上分配（好处，对象回收不需要gc）

如果对象大，栈上分配不下，如果特别大，直接分配到老年代

如果对象不太大，但也在栈上分配不下，就分配到线程本地缓存当中（Thread Local Allocation Buffer），或者直接分配到eden区，当然线程本地缓存中的也会放到eden区



## GC

### What is garbage?

> C语言内存申请： malloc  free
>
> C++： new delete
>
> Java： new ？
>
> 自动内存回收，编程上简单，系统不容易出错，手动释放内存，容易出现两种类型的问题：
>
> 1.忘记回收：内存泄漏
>
> 2.多次回收：非法访问

没有任何引用指向的一个对象或者多个对象（循环引用）被称为是垃圾

### How to locate garbage?

1.reference count（引用计数法）无法解决循环引用的对象

2.root searching（根可达算法）

### which instances are root?

JVM stack, native method stack, run-time constant pool, static references in method area, clazz

### GC Algorithm

1. Mark-Sweep（标志清除算法）

![image-20200611155125042](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611155125042.png)

将找到的垃圾进行标记，并对其进行清除

缺点是位置不连续，产生碎片

2. Copying（拷贝）算法



![image-20200611155339990](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611155339990.png)

只使用一半的内存，当进行GC之后，将原来的对象拷贝到另一半内存中的连续区域当中

缺点是浪费空间

3. Mark-Compact（标记压缩）

![image-20200611155630863](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611155630863.png)

在做标记清除的过程当中，将存活对象放置到连续的被回收的内存空间当中，相当于进行了一次压缩

缺点是效率低

### JVM内存分代模型（用于分代垃圾回收算法）

1. 部分垃圾回收器使用的模型

2. 新生代、老年代、永久代（1.7）/元数据区（1.8）Metaspace
   1. 永久代 元数据 - Class
   2. 永久代必须指定大小限制，元数据可以设置，也可以不设置，无上限（受限于物理内存）
   3. 字符串常量 1.7 - 永久代，1.8 - 堆
   4. MethodArea是一个逻辑概念 - 1.7永久代、1.8元数据

![image-20200611161247096](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611161247096.png)

3. 新生代 = Eden + 2个survivor区 
   1. YGC回收之后，大多数的对象会被回收，活着的对象进入survivor0中
   2. 再次YGC，活着的对象eden+s0放入s1
   3. 同上1,2
   4. 年龄足够（未被回收的次数够多 15或6 看垃圾回收器），进入老年代
   5. s区装不下，就进入老年代
4. 老年代满了
   1. FGC Full GC
   2. 顽固分子
5. GC Tuning（Generation）
   1. 尽量减少FGC，因为FGC是new和old一起GC
   2. MinorGC = YGC
   3. MajorGC = FGC

### Garbage Collectors

![image-20200611162342385](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611162342385.png)

1. Serial年轻代 串行回收

![image-20200611162621567](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611162621567.png)

eden满了，stw，回收

2. Parallel Scavenge年轻代并行回收

![image-20200611162801443](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611162801443.png)

3. ParNew年轻代配合CMS进行回收

![image-20200611162817045](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611162817045.png)

4. SerialOld老年代

![image-20200611163116564](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611163116564.png)

5. ParallelOld老年代

![image-20200611163057060](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611163057060.png)

6. ConcurrentMarkSwap 老年代 并发的 垃圾回收和应用程序同时运行 降低STW的时间（200ms）

![image-20200611163042157](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200611163042157.png)

7. G1（10MS）

8. ZGC（1MS）

9. Shenandoah
10. Eplison

大多数调优调的是12,45

因为1.8默认的GC是PS+ParallelOld

### JVM调优第一步，了解生产环境下的垃圾回收器组合

JVM的命令行参数参考：https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html

JVM命令参数分类

> 标准： - 开头，所有的HotSpot都支持
>
> 非标准： -X 开头，特定版本HotSpot支持特定命令
>
> 不稳定：-XX 开头，下个版本可能取消

-XX:+PrintCommandLineFlags

-XX:+PrintFlagsFinal最终参数值

-XX:+PrintFlagsInitial默认参数值

