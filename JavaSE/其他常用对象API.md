# 其他常用对象API
##

### System类

类中的方法和属性都是静态的

System类提供的System包括标准输入，标准输出和错误输出流; 访问外部定义的属性和环境变量; 一种加载文件和库的方法; 以及用于快速复制阵列的一部分的实用方法

### Runtime类

每个Java应用程序都有一个Runtime类的Runtime ，允许应用程序与运行应用程序的环境进行接口。 当前运行时可以从getRuntime方法获得

应用程序无法创建自己的此类的实例

Runtime中没有构造方法，说明无法创建对象

又有非静态方法，说明该类提供 静态的 返回该类对象的 方法

而且只有一个，说明Runtime类使用了单例模式

### Math类

Math类包含执行基本数字运算的方法，如基本指数，对数，平方根和三角函数

### Date类

	Date date = new Date();
	SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
	String str = simpleDateFormat.format(date);
	System.out.println(str);


	str = "2017年01月01日"
	SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
	Date date = simpleDateFormat.format(str);

### Calendar类

	Calendat c = Calendar.getInstance();
	int year = c.get(Calendar.YEAR);
	int month = c.get(Calendar.MONTH);
	int day = c.get(Calendar.DAY_OF_MONTH);

### Enum类

这是所有Java语言枚举类型的公共基类

其实枚举类可以用来代替静态final常量

https://blog.csdn.net/zhoufanyang_china/article/details/86707727