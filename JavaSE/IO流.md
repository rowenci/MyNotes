# IO流
##

IO流用于处理设备之间的数据传输

Java对于数据的操作是通过流的方式

Java用于操作流的对象都在IO包中

流按操作的数据分为两种：字节流与字符流

流按流向分为：输入流和输出流

字符流的由来：**字节流读取文字字节数据之后**，不直接操作，而是先通过指定的编码表，获取对应的文字之后，再对该文字进行操作。

### IO流常用基类

字节流的抽象基类：InputStream，OutputStream

字符流的抽象基类：Reader，Writer

上面四个类派生出来的子类的名称都是以父类名作为子类名的后缀

##
### 字符流

#### FileWriter

创建一个可以往文件中写入字符数据的字符输出流对象

实例化这个对象的时候，必须写明这个文件的地址+名称

如果文件不存在，就会创建一个文件

如果这个文件存在，就会覆盖这个文件（当然，创建对象的时候加入一个true参数，就是append，而不是覆盖）

	void close()//关闭流，先刷新。  
	void flush()//刷新流。  
	void write(char[] cbuf, int off, int len)//写入字符数组的一部分  
	void write(int c)//写一个字符  
	void write(String str, int off, int len)//写一个字符串的一部分

注意：写入字符的时候，并不是直接写到文件当中，会首先写入一个字符缓冲区当中，当调用flush()方法的时候，才会将字符缓冲区的数据写入到文件当中

当然，在调用close()方法关闭流的时候，会先刷新一下再关闭

异常处理：
	在try的外面创建流的引用对象
	在try的里面获取
	finally当中关闭流

#### FileReader

基本与FileWriter相同

使用的是父类Reader中的方法，调用一次读一个字符，读完之后会返回-1：

	int ch = 0;
	while((ch = fr.read()) != -1){
		System.out.println((char)ch);
	}

使用read(char[])来读取字符数据：

	int len = 0;
	char[] buf = new char[3];
	while((len = fr.read(buf)) != -1){
		System.out.println(new String(buf, 0, len));
	}

#### 字符流的缓冲区

缓冲区的出现提高了对数据的读写效率

BufferedWriter

将文本写入字符输出流，缓冲字符，以提供单个字符，数组和字符串的高效写入。 
可以指定缓冲区大小，或者可以接受默认大小。 默认值足够大，可用于大多数用途。 

提供了一个newLine（）方法，它使用平台自己的系统属性line.separator定义的行分隔符概念。 并非所有平台都使用换行符（'\ n'）来终止行。 因此，调用此方法来终止每个输出行，因此优选直接写入换行符。

	FileWriter fw = new FileWriter("asd");
	BufferedWriter bufw = new BufferedWriter(fw);
	bufw.write("asd");
	bufw.flush();
	bufw.close();

**不需要关闭fw，因为在关闭缓冲区的时候，会将fw也给关闭**

BufferedReader

从字符输入流读取文本，缓冲字符，以提供字符，数组和行的高效读取。 
可以指定缓冲区大小，或者可以使用默认大小。 默认值足够大，可用于大多数用途

	FileReader fr = new FileReader("asd");
	BufferedReader bufr = new bufferedReader(fr);
	String line = null;
	while((line = bufr.readLine()) != null){
		System.out.println(line);
	}
	bufr.close();

LineNumberReader

缓冲字符输入流，跟踪行号。 该类定义方法setLineNumber(int)和getLineNumber()用于分别设置和获得当前行号

这个类是BufferedReader的子类

##
### 字节流

不仅可以操作字符，还可以操作其他媒体文件等

#### FileOutputStream

文件输出流是用于将数据写入到输出流File或一个FileDescriptor 。 文件是否可用或可能被创建取决于底层平台。 特别是某些平台允许一次只能打开一个文件来写入一个FileOutputStream （或其他文件写入对象）。 在这种情况下，如果所涉及的文件已经打开，则此类中的构造函数将失败

他只能写二进制数据

	FileOutputStream fos = new FileOutputStream("asd");
	fos.write("asd".getBytes());
	fos.close();

#### FileInputStream

	FileInputStream fis = new FileInputStream("asd");
	int ch = 0;//一次读取一个字节
	while((ch = fis.read()) != -1){
		System.out.println((char) ch);
	}
	fis.close();



	byte[] buf = new byte[1024];
	int len = 0;
	while((len = fis.read(buf)) != -1){
		System.out.println(new String(buf, 0, len));
	}
	fis.close();
	
**当然，与字符流相同，字节流也可以使用BufferedInputStream和BufferedOutputStream**

##
### 转换流

#### InputStreamReader

InputStreamReader是从字节流到字符流的桥：它读取字节，并使用指定的charset将其解码为字符 。 它使用的字符集可以由名称指定，也可以被明确指定，或者可以接受平台的默认字符集

#### OutputStreamWriter

OutputStreamWriter是从字符流到字节流的桥：向其写入的字符编码成使用指定的字节charset 。 它使用的字符集可以由名称指定，也可以被明确指定，或者可以接受平台的默认字符集

需要指定编码的时候就在参数后面加上即可

##
### 流的操作规律

1.明确源和目的

源：InputStream Reader
目的：OutputStream Writer

2.明确数据是否是纯文本数据

源：
是：Reader
否：InputStream

目的：
是：Writer
否：InputStream

3.明确具体的设备

原设备：
硬盘：File
键盘：System.in
内存：数组
网络：Socket流

目的设备：
硬盘：File
控制台：System.out
内存：数组
网络：Socket流

4.是否需要其他额外功能

如果需要高效，就加上缓冲区

##
### File类

用来将文件或者文件夹封装成对象

方便对文件与未剪辑啊的属性信息进行操作

File对象可以作为参数传递给流的构造函数

File file = new File("asd");

常见功能：

1.获取

获取文件名称、路径、大小、修改时间

2.创建与删除

3.判断

4.重命名

##
### 过滤器

#### FilenameFilter

用于实现此接口的类的实例用于过滤文件名。 这些实例都用在过滤目录列表list类的方法File ，并通过抽象窗口工具包的文件对话框组件

	boolean accept(File dir, String name)//测试指定文件是否应包含在文件列表中

##
### Properties

该集合中的键和值都是字符串类型

集合中的数据可以保存到流中，或者从流中获取

通常该集合用于操作以键值对为形式存在的配置文件

线程安全

##
### 其他类

打印流：PrintWriter与PrintStream可以直接操作输入流和文件
	为了保证输出的是原本的字符

序列流：SequenceInputStream对多个流进行合并

操作对象：ObjectInputStream与ObjectOutPutStream，被操作的对象需要时间Serializable接口

##
### Serializable接口

SerializableL用于给被序列化的类加入ID号

用于判断类和对象是否是同一个版本

transient：瞬态关键字，非静态数据不想被序列化可以使用这个关键字来修饰

##
### 序列化与反序列化

如果需要将对象保存到文件当中，需要将对象进行序列化

反序列化也就相反了

对象中包含的不仅仅是字符，所以需要使用字节流

ObjectOutputStream就是对象的序列化流

ObjectInputStream就是对象的反序列化流

##
### RandomAccessFile

随机访问文件，自身具备读写的方法

通过skipBytes(int x)，seek(int x)连麦达到随机访问

1.该对象既能读，又能写

2.该对象内部维护了一个byte数组，并通过指针可以操作数组中的元素

3.可以通过getFilePointer方法获取指针的位置，通过seek方法设置指针的文职

4.其实该对象就是将字节输入流和输出流进行了封装

5.该对象的源或者目的只能是文件

**作用：对文件进行指定位置的读取和写入**

##
### 管道流

PipedInputStream和PipedOutputstream

管道输入流应连接到管道输出流; 管道输入流然后提供写入管道输出流的任何数据字节。 典型地，数据被从一个读PipedInputStream对象由一个线程并且数据被写入到对应的PipedOutputStream通过一些其它线程。 不建议尝试从单个线程使用这两个对象，因为它可能会使线程死锁。 管道输入流包含一个缓冲区，在读取操作中将读取操作与限制内的操作相分离。 的管道被认为是broken如果正在提供的数据字节到连接的管道输出流中的线程不再存活

输入输出可以直接进行连接，通过结合线程来使用

##
### 操作基本数据类型的流对象

1.操作基本数据类型

DataInputStream与DataOutputStream

2.操作字节数组

ByteArrayinputStream与ByteArrayOutputStream

3.操作字符数组

CharArrayReader与CharArrayWrite

4.操作字符串

StringReader与StringWriter

##
### 编码表

自行了解吧