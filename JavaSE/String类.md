# String类
##

String类的特点：字符串对象一旦被初始化就不会被改变

字符串定义的两种方法：

	String s = "abc";
	String s1 = "abc";
	System.out.println(s == s1);

	result:
		true


	String s = "abc";//在常量池中创建一个对象
	String s1 = new String("abc");//在堆内存中创建两个对象（一个new 一个字符串对象）
	System.out.println(s == s1);
	
	result:
		false

字符串常量池中如果没有，就创建一个字符串对象

字符串常量池中如果有，就直接使用该对象

一般比较字符串是否相同，会使用**equals()**方法，**不能使用==，因为==会判断引用的对象是否相同**，String类中的equals复写Object中的equals，建立了String类自己的判断字符串对象是否相同的方法

源码如下：

	public boolean equals(Object anObject) {
			//首先直接判断对象是否相同，如果对象相同，说明使用的是同一个字符串常量池中的对象，那么可以直接返回true
	        if (this == anObject) {
	            return true;
	        }
			//如果传进来的参数是字符串对象的话
	        if (anObject instanceof String) {
				//将传进来的字符串对象强转成String类型
	            String anotherString = (String)anObject;
				//获取原本字符串的长度(value是存储字符串的char数组)
	            int n = value.length;
				//判断字符串长度是否相等
	            if (n == anotherString.value.length) {
					//获取两个字符串的字符数组
	                char v1[] = value;
	                char v2[] = anotherString.value;
	                int i = 0;
					//从后向前判断字符数组当中的字符是否相同
	                while (n-- != 0) {
	                    if (v1[i] != v2[i])
	                        return false;
	                    i++;
	                }
	                return true;
	            }
	        }
			//传进来的不是字符串对象
	        return false;
	    }

由上面可以看出，字符串对象是一个对象，字符串是由字符数组组成的。

### String类的常用功能

#### 获取

1.获取字符串中字符的长度

	int length();

2.根据位置获取字符

	char charAt(int index);

3.根据字符获取在字符串中的第一次出现的位置

	int indexOf(int ch)
	int indexOf(int ch, int fromIndex)//从指定位置开始查找ch第一次出现的位置
	int indexOf(String str)
	int indexOf(String str, int fromIndex)

	int lastIndexOf(int ch)//最后一次出现的位置索引
	int lastIndexOf(int ch, int fromIndex)
	int lastIndexOf(String str)
	int lastIndexOf(String str, int fromIndex)

4.截取字符串

	String substring(int beginIndex, int endIndex)//左开右闭
	String substring(int beginIndex)
	String subSequence(int beginIndex, int endIndex)//作用同上一模一样

#### 转换

1.根据传入的字符串将字符串分割成一个个的字符串数组

	String[] split(String regex, int limit)//使用正则表达式将字符串分割limit次（limit为负数的话，就尽可能多地分割）
	String[] split(String regex)

2.将字符串转变成字符数组

	char[] tocharArray()

3.将字符串变成字节数组

	byte[] getBytes()

4.将字符串中的字母转换大小写

	String toUpperCase()//大写
	String toLowerCase()//小写

5.将字符串中的内容进行替换
	
	String replace(char oldChar, char newChar)//替换全部的oldChar

6.将字符串两端的空格去除

	String trim()

7.将字符串进行连接

	String concat(String str)//将指定的字符串连接到该字符串的末尾。

#### 判断

1.判断两个字符串内容是否相同**（看上面的源码解读）**

	boolean equals(Object obj);
	
	boolean equalsIgnoreCase(String str)//忽略大小写比较

2.判断字符串中是否包含指定字符串

	boolean contains(String str)

3.是否以指定字符串开头，是否以指定字符串结尾

	boolean startsWith(String str)
	boolean endsWith(String str)

#### 比较

# 这些东西需要用的时候查api文档吧，一个个记哪能记得过来，只能靠用

#### String intern()方法

对字符串池进行操作，返回字符串对象的规范化表示形式

	String s1 = new String("abc");
	String s2 = s1.intern();
	System.out.println(s1==s2);

	result:
		false

s1是实例化的字符串对象，不是在字符串池当中的

从字符串池中找s1的字符串常量，因为没有，所以需要新建

因此结果是false


##StringBuffer（字符串缓冲区）

**线程安全**，可变的字符序列。 

字符串缓冲区就像一个String ，但可以修改。 

在任何时间点，它包含一些特定的字符序列，但可以通过某些方法调用来更改序列的长度和内容。 

字符串缓冲区可以**安全地被多个线程使用**。 这些方法在必要时进行同步，以便任何特定实例上的所有操作都按照与所涉及的各个线程所执行的方法调用顺序一致的顺序发生。 

StringBuffer的主要StringBuffer是**append**和**insert**方法，它们被重载以便接受**任何类型**的数据。 

每个都有效地将给定的数据转换为字符串，然后将该字符串的字符附加或插入到字符串缓冲区。 

append方法总是在缓冲区的末尾添加这些字符; insert方法将insert添加到指定点。 

例如，如果z是指当前内容为"start"的字符串缓冲区对象，那么方法调用z.append("le")将使字符串缓冲区包含"startle" ，而z.insert(4, "le")会将字符串缓冲区更改为包含"starlet" 。 

一般情况下，如果某人是指的一个实例StringBuffer ，则sb.append(x)具有相同的效果sb.insert(sb.length(), x) 。 

每当涉及源序列（例如从源序列追加或插入）的操作发生时，该类仅在执行操作的字符串缓冲器上进行同步，而不在源上。 

请注意，虽然StringBuffer被设计为可以安全地从多个线程并发使用，但如果构造函数或append或insert操作被传递通过线程共享的源序列，则调用代码必须确保该操作具有一致且不变的视图在操作期间的源序列。 

呼叫者通过使用不可变的源序列，或者不跨线程共享源序列，可以在呼叫期间持有锁来满足这一点。 

每个字符串缓冲区都有一个容量。 只要字符串缓冲区中包含的字符序列的长度不超过容量，就不必分配新的内部缓冲区数组。 如果内部缓冲区溢出，则会自动变大。 

除非另有说明，否则将null参数传递给null中的构造函数或方法将导致抛出NullPointerException 。 

Constructor and Description ：

	StringBuffer() 
	构造一个没有字符的字符串缓冲区，初始容量为16个字符
  
	StringBuffer(CharSequence seq) 
	构造一个包含与指定的相同字符的字符串缓冲区 CharSequence
  
	StringBuffer(int capacity) 
	构造一个没有字符的字符串缓冲区和指定的初始容量

	StringBuffer(String str) 
	构造一个初始化为指定字符串内容的字符串缓冲区

特点：

1.长度可变

2.可以存储不同类型的数据

3.最终要转变成字符串进行使用

4.可以对字符串进行修改

一般功能：

1.添加
	
	StringBuffer append()
	StringBuffer insert(int index, data)

2.删除

	StringBuffer delete(int start, int end)//左闭右开
	StringBuffer deleteCharAt(int index)

3.查找
	
	char charAt(int index)
	int indexOf(String str)
	int lastIndexOf(String str)
	int capacity()//返回当前容量

4.修改

	StringBuffer reverse()//反转

###StringBuilder

**非线程安全**

一个可变的字符序列。 

此类提供与StringBuffer的API，但**不保证同步**。 

此类设计用作简易替换为StringBuffer在正在使用由**单个线程字符串缓冲区的地方（如通常是这种情况）**。 

在可能的情况下，建议使用这个类别优先于StringBuffer ，因为它在大多数实现中将更快。 

StringBuilder的主要StringBuilder是append和insert方法，它们是重载的，以便接受任何类型的数据。 

每个都有效地将给定的数据转换为字符串，然后将该字符串的字符附加或插入字符串构建器。 

append方法始终在构建器的末尾添加这些字符; insert方法将insert添加到指定点。 

例如，如果z引用当前内容为“ start ”的字符串构建器对象，那么方法调用z.append("le")将导致字符串构建器包含“ startle ”，而z.insert(4, "le")会将字符串构建器更改为包含“ starlet ”。 

一般情况下，如果某人是指的一个实例StringBuilder ，则sb.append(x)具有相同的效果sb.insert(sb.length(), x) 。 

每个字符串构建器都有一个容量。 

只要字符串构建器中包含的字符序列的长度不超过容量，则不需要分配新的内部缓冲区。 如果内部缓冲区溢出，则会自动变大。 

StringBuilder的StringBuilder**不能安全使用多线程**。 

如果需要同步， 那么建议使用StringBuffer 。 

除非另有说明，否则将null参数传递给null中的构造函数或方法将导致抛出NullPointerException 。 


基本上和StringBuffer一样，只不过一个线程安全一个线程不安全

StringBuffer提高效率

##
# 基本数据类型对象包
##

将基本数据类型封装成对象

好处在于可以在对象中定义更多的功能方法操作该数据

常用的操作如：基本数据类型与字符串之间的转换

	基本数据类型	对应的对象
	byte		Byte
	short		Shore
	int			Integer
	long		Long
	float		Float
	double		Double
	char		Character
	boolean		Boolean

主要用于基本类型和字符串之间的转换

基本类型转字符串：
	1.基本类型 + ""
	2.用String类中的静态方法valueOf(data)
	...

字符串转基本类型
	1.使用包装类中的静态方法Xxx parseXxx(String str)
	2.如果字符串被Integer进行对象的封装，可以使用另一个非静态的方法：intValue()来将一个Integer对象转换成基本类型

只有Character没有parse方法

### 进制转换

查api文档吧

### 自动装箱拆箱

int num = 4;
num += 5;
Integer i = 4;// i = new Integer(4)
i += 6;// i先变成基本数据类型 然后在运算 最后再变成Integer
