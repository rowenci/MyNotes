#AOP（面向切面编程）
##

1.代理设计模式（Proxy）
	
通过代理类，为原始类增加额外功能

在代理类中，将原始对象引用进来就可以调用原始类中的方法，即保留原始类的功能

2.Spring框架的动态代理

代理类 = 原始类 + 额外功能

	1.原始类

	在applicationContext.xml配置文件当中首先在bean中将原始类给实现出来

	2.额外功能
	
	代理类创建完之后需要implements MehtodBeforeAdvice

	该额外功能在原始功能之前执行

	然后在applicationContext.xml配置文件当中在bean中将代理类给实现出来

	3.切入点（额外功能加入的位置）

	在配置文件中使用<aop:config></aop:config>标签

	<aop:config>
		<aop-pointcut id="" expression="execution(* *(..))"/>
	</aop:config>

	使用execution()中的语法来表示切入点在哪个方法

	4.组装代理类（切入点和额外功能）

	<aop:advisor pointcunt-ref="切入点的id" advice-ref="额外功能的bean的id">

测试类中

	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("配置文件的路径名，用/分割");
	原始类 yuanshilei = (原始类)applicationContext.getBean("原始类的bean的id");

要获得代理类，只需要通过原始类的id值，就可以获得