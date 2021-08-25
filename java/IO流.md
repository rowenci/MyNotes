# IO流

<img src="http://p1.pstatp.com/large/pgc-image/88e8dc8113e64cf7850d1d4950518383" alt="Java åºç¡ä¹è¯¦è§£ Java IO" style="zoom:67%;" />

上面是IO流的一个整体结构

IO流分为字节流与字符流

1.字节流：以 8 位（即 1 byte，8 bit）作为一个数据单元，数据流中最小的数据单元是字节。

2.字符流：以 16 位（即 1 char，2 byte，16 bit）作为一个数据单元，数据流中最小的数据单元是字符， Java 中的字符是 Unicode 编码，一个字符占用两个字节。

Java 的 IO 主要包含两个部分：

1.流式部分：是 IO 的主体部分，也是本文介绍的重点， 

流式部分根据流向分为输入流（InputStream/Reader）和输出流（OutputStream/Writer），

根据数据不同的操作单元，分为字节流（InputStream/OutputStream）和字符流（Reader/Writer），

依据字节流和字符流，Java 定义了用来操作数据的抽象基类InputStream/OutputStream 和 Reader/Writer，

再根据不同应用场景（或功能），在这两种抽象基类上基于数据载体或功能派上出很多子类，用来满足文件，网络，管道等不同场景的 IO 需求，从而形成了 Java 的基本 IO 体系。

2.非流式部分：主要包含一些辅助流式部分的类，如： SerializablePermission 类、File 类、RandomAccessFile 类和 FileDescriptor 等；

节点流和处理流

Java io 分类方式有很多，根据是否直接处理数据，Java io又分为节点流和处理流，节点流是真正直接处理数据的；处理流是装饰加工节点流的。

节点流

- 文件流：FileInputStream，FileOutputStrean，FileReader，FileWriter，它们都会直接操作文件，直接与 OS 底层交互。因此他们被称为节点流 ，注意：使用这几个流的对象之后，需要关闭流对象，因为 java 垃圾回收器不会主动回收。不过在 Java7 之后，可以在 try() 括号中打开流，最后程序会自动关闭流对象，不再需要显示地 close。
- 数组流：ByteArrayInputStream，ByteArrayOutputStream，CharArrayReader，CharArrayWriter，对数组进行处理的节点流。
- 字符串流：StringReader，StringWriter，其中 StringReader 能从 String 中读取数据并保存到 char 数组。
- 管道流：PipedInputStream，PipedOutputStream，PipedReader，PipedWrite，对管道进行处理的节点流。

处理流

处理流是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写。如 BufferedReader。

处理流的构造方法总是要带一个其他的流对象做参数。

常用处理流（通过关闭处理流里面的节点流来关闭处理流）

- 缓冲流 ：BufferedImputStrean，BufferedOutputStream，BufferedReader ，BufferedWriter，需要父类作为参数构造，增加缓冲功能，避免频繁读写硬盘，可以初始化缓冲数据的大小，由于带了缓冲功能，所以就写数据的时候需要使用 flush 方法，另外，BufferedReader 提供一个 readLine( ) 方法可以读取一行，而 FileInputStream 和 FileReader 只能读取一个字节或者一个字符，因此 BufferedReader 也被称为行读取器。
- 转换流：InputStreamReader，OutputStreamWriter，要 inputStream 或 OutputStream 作为参数，实现从字节流到字符流的转换，我们经常在读取键盘输入（System.in）或网络通信的时候，需要使用这两个类。
- 数据流：DataInputStream，DataOutputStream，提供将基础数据类型写入到文件中，或者读取出来。

https://www.cnblogs.com/CQqf/p/10795656.html

https://www.cnblogs.com/fysola/p/6123947.html

