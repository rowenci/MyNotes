# Spring

## 反射

将类的各个组成部分封装成为其他对象



获取class对象的方式：

1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象
2. 类名.class：通过类名的属性class获取
3. 对象.getClass()：getClass()方法在Object类中定义着



```java
public static void main(String[] args) throws Exception {

        //多用于配置文件，将类名定义在配置文件当中，读取文件来加载类
        Class<?> clazz1 = Class.forName("com.company.Person");
        System.out.println(clazz1);

        //多用于参数的传递
        Class<Person> clazz2 = Person.class;
        System.out.println(clazz2);

        //多用于对象的获取字节码的方式
        Person p = new Person();
        Class<? extends Person> clazz3 = p.getClass();
        System.out.println(clazz3);

        System.out.println(clazz1 == clazz2);
        System.out.println(clazz1 == clazz3);
        System.out.println(clazz2 == clazz3);
        // all true

        /**
         * 同一个字节码文件（*.class）在一次程序运行过程当中，只会被加载一次，不论通过哪一种方式获取的class对象都是同一个对象
         */

    }
```



Class对象功能：

1. 获取成员变量
   1. Field[] getFields()：获取所有public修饰的成员变量
   2. Field getField(String name)
   3. Field[] getDeclearedFields()
   4. Field getDeclearedField(String name)
2. 获取构造方法
   1. Constructor<?>[] getConstructors() 
   2. Constructor<T> getConstructor(类<?>... parameterTypes) 
   3. Constructor<?>[] getDeclaredConstructors()  
   4. Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)  
3. 获取成员方法
   1. Method[] getMethods()  
   2. Method getMethod(String name, 类<?>... parameterTypes)  
   3. Method[] getDeclaredMethods()  
   4. Method getDeclaredMethod(String name, 类<?>... parameterTypes)  
4. 获取类名
   1. String getName()  



使用反射：

1. 使用成员变量

   1. 设置值：void set(Object obj, Object value)
   2. 获取值：get(Object obj)
   3. 忽略访问权限修饰符的安全检查：setAccessible(true)//暴力反射

2. 创建对象

   T newInstance(Object... initargs)

   如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法

   构造器方法当中也需要使用setAccessible方法

3. 使用方法

   1. 执行方法：Object invoke(Object obj, Object... args)
   2. 获取方法名称：String getName()：获取方法名
   3. 忽略访问权限修饰符的安全检查：setAccessible(true)

## IOC

IOC（Inverse of control）：控制反转

意思就是将创建对象的功能，转交给Spring来完成。



### 创建阶段

创建一个对象类，并封装对象的属性，对外开放get和set方法。

在使用Spring创建这个类之前，需要在Spring的配置文件当中，对这个类进行注入。

有两种注入方法：



1. Set注入

   Spring在底层就会通过调用对象属性对应的set方法，完成成员变量的赋值。

   当然，根据不同类型的成员变量，在property标签当中，还需要嵌套其他标签，例如：list, set, map, props

   还有一种的用户自定义的类型，比如自己定义的一个类，当做对象的成员变量进行注入

   ```xml
   1.
   <bean id="person" class="com.rowenci.Person">
   	<property name="id">
   		<value>10</value>
   	</property>
   	<property name="name">	
   		<value>rowenci</value>
   	</property>
   </bean>
   
   
   
   2.
   JDK类型注⼊ 
   <property name="name">
       <value>rowenci</value> 
   </property>
   
   <property name="name" value="rowenci"/> 
   注意：value属性    只能简化 8种基本类型 +String 注⼊标签 ⽤户⾃定义类型 
   <property name="userDAO">
   	<ref bean="userDAO"/>
   </property>
   <property name="userDAO" ref="userDAO"/>
   
   
   
   3.基于p命名空间简化
   JDK类型注⼊ 
   <bean id="person" class="xxxx.Person"> 
       <property name="name">
           <value>rowenci</value> 
       </property> 
   </bean>
   <bean id="person" class="xxx.Person" p:name="rowenci"/>
   注意：value属性    只能简化 8种基本类型 +String 注⼊标签 ⽤户⾃定义类型 
   <bean id="userService" class="xx.UserServiceImpl"> 
       <property name="userDAO"> 
       	<ref bean="userDAO"/> 
       </property> 
   </bean> 
   <bean id="userService" class="xxx.UserServiceImpl" p:userDAO-ref="userDAO"/>
   ```



这个时候就有一个问题，就是Spring是如何知道被写在配置文件当中的字符串，究竟是什么类型的呢？

这是因为，Spring通过类型转换器把配置⽂件中字符串类型的数据，转换成了对象中成员变量对应 类型的数据，进⽽完成了注⼊

当Spring内部没有提供特定类型转换器时，⽽程序员在应⽤的过程中还需要使⽤，那么就需要程序员⾃⼰定义类型转换器

首先，创建一个类，并且实现Converter接口

```java
public class MyDateConverter implements Converter<String, Date> { 
    /*
    convert⽅法作⽤：String -- ->  Date 
    SimpleDateFormat sdf = new SimpleDateFormat(); 
    sdf.parset(String) ---> Date param:source 代表的是配置⽂件中⽇期字符串
    <value>2020-10-11</value>
    return : 当把转换好的Date作为convert⽅法的返回值后,Spring⾃动为birthday属性进⾏注⼊（赋值） 
    */ 
    @Override 
    public Date convert(String source) { 
        Date date = null; 
        try { 
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd"); 
        date = sdf.parse(source);
        } catch (ParseException e) {
            e.printStackTrace(); 
        } 
        return date;
    }
}
```

然后，在配置文件当中将该自定义类型转换器的对象给创建出来，并且进行注册

```xml
<bean id="myDateConverter" class="xxxx.MyDateConverter"/>
⽬的：告知Spring框架，我们所创建的 MyDateConverter是⼀个类型转换器 <!--⽤于注册类型转换器--> 
<bean id="conversionService"class="org.springframework.context.support.ConversionServiceFa ctoryBean"> 
    <property name="converters">
        <set> 
            <ref bean="myDateConverter"/> 
        </set> 
    </property> 
</bean>
```

Spring里面内置的日期类型的转换器，是用 / 来分割的，而不是 - 

以上，就是set注入的一些问题



2. 构造注入

   Spring通过调用对象的构造函数，通过配置文件，为成员变量进行赋值

```java
public class Customer implements Serializable { 
	private String name;
    private int age; 
	public Customer(String name, int age) { 
		this.name = name; 
		this.age = age; 
    } 
}
```



```xml
<bean id="customer" class="com.rowenci.Customer"> 
    <constructor-arg> 
    	<value>rowenci</value> 
    </constructor-arg> 
    <constructor-arg> 
    	<value>102</value> 
    </constructor-arg> 
</bean>
```

参数个数不同时

通过控制<constructor-arg>标签的数量进行区分

构造参数个数相同时

通过在标签引⼊type属性进行类型的区分 <constructor-arg type="">





以上是简单对象的创建过程，什么是简单对象呢？就是通过new关键字就能直接实例化的一个对象

那么还有一种复杂对象，就是指无法直接通过new构造方法创建的对象，如：

1. Connection
2. SqlSessionFactory



对于这种复杂对象，就需要使用特定的方法来对对象进行创建，分别有三种方法：

1. FactoryBean接口

创建复杂对象的工厂类实现FactoryBean接口，并进行对象创建代码的编写，在编写完成之后，在Spring配置文件当中对该工厂对象进行注入



FactoryBean中有三个方法

1. getObject()：用于书写创建复杂对象的代码，并把复杂对象作为方法的返回值进行返回
2. getObjectType()：返回创建复杂对象的Class对象
3. isSingleton()：这个对象是否是单例



```xml
# 如果Class中指定的类型是FactoryBean接⼝的实现类，那么通过 id值获得的是这个类所创建的复杂对象Connection 
<bean id="conn" class="com.rowenci.factorybean.ConnectionFactoryBean"/>

细节 
如果就想获得FactoryBean类型的对象 
ctx.getBean("&conn") 获得就是 ConnectionFactoryBean对象 
isSingleton⽅法返回 true 那么只会创建⼀个复杂对象
返回 false 每⼀次都会创建新的对象
根据这个对象的特点 ，决定是返回true (SqlSessionFactory) 还是 false (Connection)

依赖注⼊的体会(DI)
把ConnectionFactoryBean中依赖的4个字符串信息，通过set方法进⾏配置⽂件的注⼊ 好处：解耦合
<bean id="conn" class="com.baizhiedu.factorybea n.ConnectionFactoryBean"> 
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3 306/suns?useSSL=false"/> 
    <property name="username" value="root"/> 
    <property name="password" value="123456"/> 
</bean>
```

FactoryBean的实现原理：

```
接⼝回调 
1. 为什么Spring规定FactoryBean接⼝实现并且 getObject()? 
2. ctx.getBean("conn") 获得是复杂对象Connection⽽没有获得ConnectionFactoryBean(&)

Spring内部运⾏流程
1. 通过conn获得ConnectionFactoryBean类的对象，进⽽通过instanceof判断出是FactoryBean接⼝的实现类 
2. Spring按照规定 getObject() ---> Connection 
3. 返回Connection
```



2. 实例工厂

首先获取ConnFactory，然后再通过factory-bean以及factory-method来获取conn对象

这样的方式，能够避免Spring框架的侵入（也就是对象实现FactoryBean接口）

还能够整合遗留系统

```xml
<bean id="connFactory" class="com.baizhiedu.factorybean.ConnectionFactory"/>
<bean id="conn"  factory-bean="connFactory" factory-method="getConnection"/>
```

connFactory是自己写的工厂

conn是告诉spring工厂类是哪个，获取对象的工厂方法是哪个



3. 静态工厂

```xml
<bean id="conn" class="com.baizhiedu.factorybean.StaticConnectionFactory" factory-method="getConnection"/>
```

创建工厂的方法是静态的，与实例工厂的区别就是，不需要对工厂类进行注入，可以直接使用静态工厂的方法来获取对象



下面还有一个问题，就是关于对象是否单例的问题

对于简单对象，可以直接在bean标签中添加一个scope属性即可

```xml
<bean id="account" scope="singleton|prototype" class="xxxx.Account"/>
sigleton:只会创建⼀次简单对象
默认值 prototype:每⼀次都会创建新的对象
```

对于复杂对象，在FactoryBean实现的isSingleton方法当中：

1. 返回true：单例
2. 返回false：非单例



当然，Spring还能够配置何时来对对象进行创建

1. scope="singleton"：Spring工厂创建的同时，对象也进行创建

   当然，如果需要懒汉式的话，可以在bean标签当中加一个lazy-init属性，并设置为true，即可

2. scope="prototype"：Spring工厂在获取对象时，创建对象





### 初始化阶段

在对象创建完成，以及配置文件编写完成之后，就能够在main方法当中对对象进行创建以及使用

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("/applicationContext.xml");
Person p = (Person)ctx.getBean("person");
System.out.println(p.toString);
```



Spring⼯⼚在创建完对象后，调⽤对象的初始化⽅法，完成对应的初始化操作

这个初始化操作可以由程序员来进行编写，并让Spring工厂进行调用

有两种方法：

1. 实现**InitializingBean**接⼝

   ```java
   //程序员根据需求，实现的⽅法，完成初 始化操作 
   public void afterProperitesSet(){ 
   }
   ```

   

2. 对象提供一个方法，并在配置文件中配置

   ```xml
   public void myInit(){ 
   } 
   <bean id="product" class="xxx.Product" init- method="myInit"/>
   ```

   如果⼀个对象即实现InitializingBean 同时⼜提供的普通的初始化⽅法，那么首先Spring会执行InitializingBean，然后才执行普通的方法

注意，注入操作，是发生在初始化操作之前的，那么这个初始化操作，就可以用来进行资源的初始化，比如：数据库连接，IO，网络等



在创建对象完成之后，可以通过实现BeanPostProcessor接口，来对Spring工厂创建的对象进行再加工



```
程序员实现BeanPostProcessor规定接⼝中的⽅法：

Object postProcessBeforeInitiallization(Object bean String beanName) 
作⽤：Spring创建完对象，并进⾏注⼊后，可以运⾏Before⽅法进⾏加⼯
获得Spring创建好的对象：通过⽅法的参数最终通过返回值交给Spring框架

Object postProcessAfterInitiallization(Ob ject bean String beanName) 
作⽤：Spring执⾏完对象的初始化操作后，可以运⾏After⽅法进⾏加⼯
获得Spring创建好的对象：通过⽅法的参数 最终通过返回值交给Spring框架 

实战中：
很少处理Spring的初始化操作：没有必要区分Before After。只需要实现其中的⼀个After⽅法即可 

注意： 
postProcessBeforeInitiallization return bean对象，因为即使不用它，Spring依然会执行，那么就依然需要你返回当前的对象
```



```java
public class MyBeanPostProcessor implements BeanPostProcessor { 
    @Override
    public Object postProcessBeforeInitializat ion(Object bean, String beanName) throws BeansException { 
        return bean;
    }
    @Override 
    public Object postProcessAfterInitializati on(Object bean, String beanName) throws BeansException { 
        Categroy categroy = (Categroy) bean; 
        categroy.setName("xiaowb");
        return categroy;
    }
}
```

```xml
<bean id="myBeanPostProcessor" class="xxx.MyBeanPostProcessor"/>
```

**注：BeanPostProcessor会对Spring⼯⼚中所有创建的对象进⾏加⼯。**



**InitializingBean 和 BeanPostProcessor区别**

1. BeanPostProcess先于InitializingBean执行
2. InitializingBean是在对象已经实例化之后，执行接口的afterPropertiesSet()方法；
   BeanPostProcess是在bean构建的过程中，postProcessBeforeInitialization，postProcessAfterInitialization
   注意：每个bean在构建的过程中，Spring都会遍历所有的BeanPostProcessor的实现类，调用实现类中的方法，入参为构建好的bean
3. 自己实现BeanPostProcess接口时，如果想生效，还需将实现类构建成SpringBean
   即需要用@Component注解等方法

**执行顺序**

构造方法-->BeanPostProcessor-->InitializingBean-->bean中的初始化方法



### 销毁阶段

Spring销毁对象前，会调⽤对象的销毁⽅法，完成销毁操作 
1. Spring什么时候销毁所创建的对象？ ctx.close(); 
2. 销毁⽅法：程序员根据⾃⼰的需求，定义销毁⽅法，完成销毁操作 
调⽤：Spring⼯⼚完成调⽤

销毁方法：

1. DisposableBean

```java
public void destroy()throws Exception{ 
}
```

2. 定义⼀个普通的销毁⽅法

```java
public void myDestroy()throws Exception{ 
} 
```

```xml
<bean id="" class="" init-method="" destroy-method="myDestroy"/>
```

注意：

1.     销毁⽅法的操作只适⽤于 scope="singleton"
2.     什么叫做销毁操作 主要指的就是资源的释放操作 io.close()  connection.close();



下面还有一点补充，就是关于各种参数在Spring配置文件当中，不利于维护的解决方案

把Spring配置文件中需要经常修改的字符串信息，转移到⼀个更小的配置⽂件中 
1. Spring的配置文件中存在需要经常修改的字符串？ 
存在以数据库连接相关的参数代表
2. 经常变化字符串，在Spring的配置文件中，直接修改不利于项目维护(修改) 
3. 转移到⼀个小的配置⽂件(.properties)，利于维护(修改) 
    配置⽂件参数化：利于Spring配置⽂件的维护(修改)

提供⼀个小的配置⽂件(.properities)

名字：随便 
放置位置：随便 

```properties
jdbc.driverClassName = com.mysql.jdbc.Driver 
jdbc.url = jdbc:mysql://localhost:3306/suns? useSSL=false 
jdbc.username = root 
jdbc.password = 123456
```

applicationContext.xml

```xml
<context:property-placeholder location="classpath:/db.properties"/>
```

在Spring配置⽂件中通过${key}获取⼩配置⽂件中 对应的值





以上就是Spring的Bean各个生命周期的一些简单知识。通过与IOC结合，来进行了一个简单介绍



## AOP

aspect oriented programing



### 静态代理模式

通过代理类，为原始类（⽬标）增加额外的功能 好处：利于原始类(⽬标)的维护

1. ⽬标类    原始类 
指的是    业务类 (核⼼功能 --> 业务运算 DAO调⽤) 
2. ⽬标⽅法，原始⽅法 
⽬标类(原始类)中的⽅法    就是⽬标⽅法 (原始⽅法) 
3. 额外功能 (附加功能) 
⽇志，事务，性能



代理类 = ⽬标类(原始类) + 额外功能 + 原始类(⽬标类)实现相同的接⼝ 
房东 ---> public interface UserService{ 
	m1
	m2 
} 
UserServiceImpl implements UserService{ 
	m1 ---> 业务运算 DAO 
	调⽤ 
	m2 
} 
UserServiceProxy implements UserService 
m1 
m2

![image-20200616171955130](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616171955130.png)

这个时候，就能够通过userserviceproxy来作为userservice的实现，然后额外功能就能添加到对应的方法当中了

然而静态代理存在一些问题：

1. 静态类⽂件数量过多，不利于项⽬管理
   
   UserServiceImpl UserServiceProxy 
   OrderServiceImpl OrderServiceProxy 
   
2. 额外功能维护性差 
    代理类中    额外功能修改复杂(麻烦)



### Spring动态代理

概念：通过代理类为原始类(⽬标类)增加额外 功能 
好处：利于原始类(⽬标类)的维护



1. 创建原始对象(⽬标对象)

   ```java
   public class UserServiceImpl implements UserService { 
       @Override 
       public void register(User user) { 
           System.out.println("UserServi ceImpl.register 业务运算 + DAO "); 
       } 
       @Override 
       public boolean login(String name, String password) { 
           System.out.println("UserServi ceImpl.login"); 
           return true;
       } 
   }
   ```

   ```xml
   <bean id="userService" class="com.baizhiedu.proxy.User ServiceImpl"/>
   ```

2. 额外功能 MethodBeforeAdvice接⼝

   额外的功能书写在接⼝的实现中，运⾏在 原始⽅法执⾏之前运⾏额外功能。

   ```java
   public class Before implements MethodBeforeAdvice { 
   /* 
   作⽤：需要把运⾏在原始⽅法执 ⾏之前运⾏的额外功能，书写在before⽅ 法中 
   */ 
       @Override 
       public void before(Method method, Object[] args, Object target) throws Throwable { 
   		System.out.println("-- ---method before advice log---- --"); 
       } 
   }
   ```

   ```xml
   <bean id="before" class="com.baizhiedu.dynamic.Before"/>
   ```

   3. 定义切⼊点

      切⼊点：额外功能加⼊的位置 
      ⽬的：由程序员根据⾃⼰的需要，决定额 外功能加⼊给那个原始⽅法 register login 
      简单的测试：所有⽅法都做为切⼊点，都 加⼊额外的功能。

   ```xml
   <aop:config> 
   <aop:pointcut id="pc" expression="execution(* * (..))"/> 
   </aop:config>
   ```

   4. 组装 (2 3整合)

   ```xml
   表达的含义：所有的⽅法都加⼊ before的额外功能 
   <aop:advisor advice- ref="before" pointcut- ref="pc"/>
   ```

   5. 调⽤

   ```
   ⽬的：获得Spring⼯⼚创建的动态代理对象，并进⾏调⽤ ApplicationContext ctx = new ClassPathXmlApplicationContext ("/applicationContext.xml"); 注意： 
   1. Spring的⼯⼚通过原始对象的 id值获得的是代理对象 
   2. 获得代理对象后，可以通过声明 接⼝类型，进⾏对象的存储 
   UserService userService= (UserService)ctx.getBean("user Service"); 
   userService.login("")
   userService.register() 
   ```

   

Spring创建的动态代理类在哪⾥？

这个动态代理类就是整合了业务代码和额外功能的一个类

Spring框架在运⾏时，通过动态字节码 技术，在JVM创建的，运⾏在JVM内部，等 程序结束后，会和JVM⼀起消失 
什么叫动态字节码技术:通过第三个动态 字节码框架，在JVM中创建对应类的字节 码，进⽽创建对象，当虚拟机结束，动态 字节码跟着消失。
结论：动态代理不需要定义类⽂件，都是 JVM运⾏过程中动态创建的，所以不会造成静态代理，类⽂件数量过多，影响项⽬管理的问题。

![image-20200616172502790](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616172502790.png)

动态代理编程简化代理的开发

在额外功能不改变的前提下，创建其他⽬标类（原始类）的代理对象时，只需要指定原始(⽬标)对象即可。

动态代理额外功能的维护性⼤⼤增强



MethodBeforeAdvice分析

1. MethodBeforeAdvice接⼝作⽤：额 外功能运⾏在原始⽅法执⾏之前，进⾏额外 功能操作。

   ```java
   public class Before1 implements MethodBeforeAdvice { 
   /* 
   作⽤：需要把运⾏在原始⽅法执⾏ 之前运⾏的额外功能，书写在before⽅法 中 
   Method: 额外功能所增加给的那 个原始⽅法
   login⽅法 
   register⽅法 
   showOrder⽅法 
   Object[]: 额外功能所增加给的 那个原始⽅法的参数。String name,String password 
   User 
   Object: 额外功能所增加给的那 个原始对象  UserServiceImpl 
   OrderServiceImpl 
   */ 
   	@Override 
   	public void before(Method method, Object[] args, Object target) throws Throwable { 
   		System.out.println("---- -new method before advice log------"); 
       } 
   }
   ```
   
2. before⽅法的3个参数在实战中，该如 何使⽤。 
   before⽅法的参数，在实战中，会根 据需要进⾏使⽤，不⼀定都会⽤到，也有可 能都不⽤。

   ```java
   Servlet{ 
   	service(HttpRequest request,HttpResponse response){ 
   		request.getParameter("name") -- > 
   		response.getWriter() 
   		---> 
       } 
   }
   ```

MethodInterceptor(⽅法拦截器) 

methodinterceptor接⼝：额外功能可以 根据需要运⾏在原始⽅法执⾏ 前、后、前 后。

```java
public class Arround implements MethodInterceptor { 
/* 
invoke⽅法的作⽤:额外功能 书写在invoke 
额外功能 
原始⽅法之前 
原始⽅法之后 
原始⽅法执⾏之前    之后 
确定：原始⽅法怎么运⾏ 
参数：MethodInvocation （Method):额外功能所增加给的那个原始 
⽅法 
login register 
invocation.proceed() ---> login 运⾏ 
register运⾏ 
返回值：Object: 原始⽅法 的返回值 
Date convert(String name) 
*/
    @Override 
    public Object invoke(MethodInvocation invocation) throws Throwable { 
        System.out.println("-----额外功能 log----"); 
        Object ret = invocation.proceed(); 
        return ret; 
    } 
} 
```

额外功能运⾏在原始⽅法执⾏之后 

```java
@Override 
public Object invoke(MethodInvocation invocation) throws Throwable { 
    Object ret = invocation.proceed(); 
    System.out.println("-----额外功 能运⾏在原始⽅法执⾏之后----"); 
    return ret; 
} 
```

额外功能运⾏在原始⽅法执⾏之前，之后 

什么样的额外功能    运⾏在原始⽅法执⾏之前，之后都要添加？ 
事务

```java
@Override 
public Object invoke(MethodInvocation invocation) throws Throwable { 
    System.out.println("-----额外功 能运⾏在原始⽅法执⾏之前----"); 
    Object ret = invocation.proceed(); 
    System.out.println("-----额外功 能运⾏在原始⽅法执⾏之后----"); 
    return ret; 
}
```

额外功能运⾏在原始⽅法抛出异常的时候 

```java
@Override 
public Object invoke(MethodInvocation invocation) throws Throwable { 
    Object ret = null; 
    try { 
        ret = invocation.proceed();
    } catch (Throwable throwable) { 
        System.out.println("-----原 始⽅法抛出异常    执⾏的额外功能 ---- "); 
        throwable.printStackTrace();  
    } 
    return ret; 
}
```

MethodInterceptor影响原始⽅法的返回值 

原始⽅法的返回值，直接作为invoke⽅法 的返回值返回，MethodInterceptor不会 影响原始⽅法的返回值 
MethodInterceptor影响原始⽅法的返回 值 
Invoke⽅法的返回值，不要直接返回原始 ⽅法的运⾏结果即可。

```java
@Override 
public Object invoke(MethodInvocation invocation) throws Throwable {   					    System.out.println("------ log-----"); 
   Object ret = invocation.proceed(); 
   return false; 
}
```



AfterReturningAdvice

运行在方法执行完成之后

```java
public class test implements AfterReturningAdvice {
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        
    }
}
```





### AOP编程

AOP (Aspect Oriented Programing)  ⾯向切⾯编程 = Spring动态代理开发 以切⾯为基本单位的程序开发，通过切⾯间的彼 此协同，相互调⽤，完成程序的构建 
切⾯ = 切⼊点 + 额外功能 
OOP (Object Oritened Programing)  ⾯向对象编程 Java 
以对象为基本单位的程序开发，通过对象间的彼 此协同，相互调⽤，完成程序的构建 
POP (Producer Oriented Programing) ⾯向过程(⽅法、函数)编程 C 
以过程为基本单位的程序开发，通过过程间的彼 此协同，相互调⽤，完成程序的构建

AOP的概念： 
本质就是Spring得动态代理开发，通过 代理类为原始类增加额外功能。 
好处：利于原始类的维护 
注意：AOP编程不可能取代OOP，OOP编程有意 补充。 



AOP编程的开发步骤 

1. 原始对象 
2. 额外功能 (MethodInterceptor) 3. 切⼊点 
4. 组装切⾯ (额外功能+切⼊点) 



AOP的底层实现原理

1. AOP如何创建动态代理类(动态字节码技术) 
2. Spring⼯⼚如何加⼯创建代理对象 
    通过原始对象的id值，获得的是代理对象 

JDK的动态代理

Proxy.newProxyInstance⽅法参数详解

![image-20200616173036586](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616173036586.png)

![image-20200616173051232](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616173051232.png)

编码 

```java
public class TestJDKProxy { 
    /* 
    1. 借⽤类加载器 
TestJDKProxy 
UserServiceImpl 
2. JDK8.x前 
final UserService 
userService = new 
UserServiceImpl(); 
*/ 
public static void main(String[] args) { 
//1 创建原始对象 
UserService userService = new UserServiceImpl(); 
//2 JDK创建动态代理 
/* 
*/ 
InvocationHandler handler = new InvocationHandler(){ 
    @Override 
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable { 
    System.out.println("------proxy log --------"); 
    //原始⽅法运⾏ Object ret = 
    method.invoke(userService, args); 
    return ret; 
    } 
}
    UserService userServiceProxy = (UserService)Proxy.newProxyInstance(UserServiceImpl.class.getClassLoader(),userService.getClass( ).getInterfaces(),handler); 
    userServiceProxy.login("suns", "123456"); 
    userServiceProxy.register(new User()); 
    } 
} 
```





CGlib的动态代理

CGlib创建动态代理的原理：⽗⼦继承关系创建 代理对象，原始类作为⽗类，代理类作为⼦类， 这样既可以保证2者⽅法⼀致，同时在代理类中 提供新的实现(额外功能+原始⽅法) 

![image-20200616173151407](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616173151407.png)

CGlib编码 

```java
package com.baizhiedu.cglib; 
import com.baizhiedu.proxy.User; import org.springframework.cglib.proxy. Enhancer; 
import org.springframework.cglib.proxy. MethodInterceptor; 
import org.springframework.cglib.proxy. MethodProxy; 
import java.lang.reflect.Method; public class TestCglib { 
public static void main(String[] args) { 
    //1 创建原始对象 
    UserService userService = new UserService(); 
    /* 
    2 通过cglib⽅式创建动态代 理对象 
    Proxy.newProxyInstance(classloa der,interface,invocationhandler) 
    Enhancer.setClassLoader() 
    Enhancer.setSuperClass() 
    Enhancer.setCallback();  ---> MethodInterceptor(cglib) 
    Enhancer.create() -- 
    -> 代理 
    */ 
    Enhancer enhancer = new Enhancer(); 
    enhancer.setClassLoader(TestCgl ib.class.getClassLoader()); 
    enhancer.setSuperclass(userServ ice.getClass()); 
    MethodInterceptor interceptor = new 
    MethodInterceptor() { 
    //等同于 
    InvocationHandler --- invoke 
    @Override 
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable { 
        System.out.println("---cglib log----"); 
        Object ret = method.invoke(userService, args); 
        return ret; 
    } 
    }; 
    enhancer.setCallback(intercepto r); 
    UserService userServiceProxy = (UserService) enhancer.create(); 
    userServiceProxy.login("suns", "123345"); 
	userServiceProxy.register(new User()); 
    } 
} 
```



总结 

1. JDK动态代理 
Proxy.newProxyInstance()  通过接⼝ 创建代理的实现类 
2. Cglib动态代理 Enhancer        通过继承⽗类创建的代理类 



Spring⼯⼚如何加⼯原始对象

![image-20200616173249847](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616173249847.png)

```java
public class ProxyBeanPostProcessor implements BeanPostProcessor { 
    @Override 
    public Object postProcessBeforeInitialization( Object bean, String beanName) throws BeansException { 
        return bean; 
    } 
@Override 
/* 
Proxy.newProxyInstance(); 
*/ 
    public Object postProcessAfterInitialization(O bject bean, String beanName) throws BeansException { 
InvocationHandler handler = new InvocationHandler() { 
    @Override 
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable { 
    System.out.println("----- new Log-----"); 
    Object ret = method.invoke(bean, args); 
    return ret; 
            } 
        }; 
return Proxy.newProxyInstance(ProxyBean PostProcessor.class.getClassLoader(),bean.getClass().getInterfac es(),handler); 
    } 
} 
```

```xml
<bean id="userService" class="com.baizhiedu.factory.User ServiceImpl"/> 


<!--1. 实现BeanPostProcessor 进⾏加⼯ 
2. 配置⽂件中对 
BeanPostProcessor进⾏配置 
--> 
<bean id="proxyBeanPostProcessor" class="com.baizhiedu.factory.ProxyBeanPostProcessor"/> 
```

基于注解的AOP编程的开发步骤

1.     原始对象 
2.     额外功能 
3.     切⼊点

4. 组装切⾯

```java
# 通过切⾯类定义了额外功能 
@Around 
定义了切⼊点 
@Around("execution(* login(..))") 
@Aspect 切⾯类 
package com.baizhiedu.aspect; import org.aspectj.lang.ProceedingJoi nPoint; 
import org.aspectj.lang.annotation.Ar ound; 
import org.aspectj.lang.annotation.As pect; 
/* 
1. 额外功能
public class 
MyArround implements MethodInterceptor{ 
    public Object invoke(MethodInvocation invocation){ 
        Object ret = invocation.proceed(); 
        return ret; 
    }
}
2. 切⼊点 
<aop:config 
<aop:pointcut id="" expression="execution(* login(..))"/> 
*/ 
@Aspect 
public class MyAspect { 
    @Around("execution(* login(..))") 
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable { 
        System.out.println("----aspect log------"); 
        Object ret = joinPoint.proceed(); 
        return ret; 
    }
}
```

```xml
<bean id="userService" class="com.baizhiedu.aspect.Us erServiceImpl"/> 
<!-- 
切⾯ 
1. 额外功能 
2. 切⼊点 
3. 组装切⾯ 
--> 
<bean id="arround" class="com.baizhiedu.aspect.My Aspect"/> 
<!--告知Spring基于注解进⾏AOP编 程--> 
<aop:aspectj-autoproxy /> 
```

切⼊点复⽤

切⼊点复⽤：在切⾯类中定义⼀个函数 上⾯@Pointcut注解 通过这种⽅式，定 义切⼊点表达式，后续更加有利于切⼊点 复⽤。 

```java
@Aspect 
public class MyAspect { 
    @Pointcut("execution(* login(..))") 
    public void myPointcut() {} 
    @Around(value="myPointcut()")
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable { 
        System.out.println("----aspect log ------"); 
        Object ret = joinPoint.proceed(); 
        return ret; 
    }
    @Around(value="myPointcut()") 
    public Object arround1(ProceedingJoinPoint joinPoint) throws Throwable { 
        System.out.println("- ---aspect tx ------"); 
        Object ret = joinPoint.proceed(); 
        return ret; 
    }
}
```

动态代理的创建⽅式

AOP底层实现  2种代理创建⽅式 
1.  JDK  通过实现接⼝    做新的实现 类⽅式    创建代理对象 
2.  Cglib通过继承⽗类    做新的⼦类 创建代理对象 
默认情况 AOP编程    底层应⽤JDK动态 代理创建⽅式 
如果切换Cglib

1. 基于注解AOP开发 
<aop:aspectj- 
autoproxy proxy-target- class="true" /> 
2. 传统的AOP开发 
<aop:config proxy- 
target-class="true"> 
</aop>

AOP开发中的⼀个坑

坑：在同⼀个业务类中，进⾏业务⽅法间的相 互调⽤，只有最外层的⽅法,才是加⼊了额外 功能(内部的⽅法，通过普通的⽅式调⽤，都 调⽤的是原始⽅法)。如果想让内层的⽅法也 调⽤代理对象的⽅法，就要 AppicationContextAware获得⼯⼚，进⽽ 获得代理对象。

```java
public class UserServiceImpl implements UserService, ApplicationContextAware { 
	private ApplicationContext ctx;
    @Override 
	public void setApplicationContext(ApplicationC ontext applicationContext) throws BeansException { 
		this.ctx = applicationContext; 
    } 
    @Log 
    @Override 
    public void register(User user) { 
    	System.out.println("UserServiceIm pl.register 业务运算 + DAO "); 
        //throw new 
        RuntimeException("测试异常"); 
        //调⽤的是原始对象的login⽅法 
        ---> 核⼼功能 
        /* 
        设计⽬的：代理对象的login ⽅法 --->  额外功能+核⼼功能 
        ApplicationContext ctx 
        = new ClassPathXmlApplicationContext("/a pplicationContext2.xml"); 
        UserService 
        userService = (UserService) 
        ctx.getBean("userService"); 
        userService.login(); 
        Spring⼯⼚重量级资源    ⼀个 
        应⽤中    应该只创建⼀个⼯⼚ 
        */ 
        UserService userService = (UserService) 
        ctx.getBean("userService"); 
        userService.login("suns", "123456"); 
    } 
    @Override 
    public boolean login(String name, String password) { 
        System.out.println("UserServiceImpl.login"); 
        return true; 
    } 
} 
```





AOP阶段知识总结

![image-20200616172935544](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616172935544.png)

# Mybatis

Mybatis使用分为四个步骤

1. 编写配置文件（configFile）

2. 编写映射文件

3. 编写实际对象类

4. 实现方法



## 1.编写配置文件

文件开头的一串是关于配置文件的约束

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
		PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
		"http://mybatis.org/dtd/mybatis-3-config.dtd">
```

这些东西可以直接复制粘贴

在配置文件当中，配置是以标签形式来编写的，类似于android中的app配置文件

configuration标签：configuration标签只有一个，其中包含了所有关于Mybatis的配置，所有的配置都是在configuration标签中编写的

environment标签：environment标签可以有多个，方便在使用时进行切换，不同的environment标签用id属性来进行区别

transaction标签：当中的type属性有两个值：（JDBC|MANAGED），代表了不同的事务管理类型

dataSource标签：type属性代表不同的数据源类型

property标签：property标签是用来配置数据库连接属性的，每一个property标签配置一个数据库连接属性，分别用name进行区别，与普通JDBC相同，进行数据库连接需要注册驱动程序，数据库地址，用户名，密码。

mappers标签：mappers标签中包含了所有的注册映射文件的标签

mapper标签：用来注册映射文件，resource属性代表了mapper文件的类地址，路径用斜线（/）进行分割，而不是用点（.）

```xml
<configuration> 
    <!-- 配置开发环境，可以配置多个，在具体用时再做切换 -->
    <environments default="demo">
        <environment id="demo">
            <transactionManager type="JDBC" />    <!-- 事务管理类型：JDBC、MANAGED -->
            <dataSource type="POOLED">    <!-- 数据源类型：POOLED、UNPOOLED、JNDI -->
                <property name="driver" value="org.postgresql.Driver" />
                <property name="url" value="jdbc:postgresql://localhost:5432/postgres" />
                <property name="username" value="postgres" />
                <property name="password" value="rowenci" />
            </dataSource>
        </environment>
    </environments>

    <!-- 加载映射文件 mapper -->
    <mappers>
        <!-- 路径用 斜线（/） 分割，而不是用 点(.) -->
        <mapper resource="com/mapper/UserMapper.xml"></mapper>
    </mappers>
</configuration>
```

## 2.编写映射文件

mapper文件开头是一串固定约束

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```



mapper标签：mapper标签中的namespace属性为命名空间，可以随意起，目的是为了区分映射文件中的方法

在mapper标签中进行增删改查操作

增删改查操作分别对应四个标签
select
insert
update
delete

这四个标签中都有属性id，用来作为对于相同操作的标签的区分

还有返回值类型属性

SQL语句就书写在这四种标签当中，用开始标签和结束标签作为SQL语句的开始和结束。



```xml
<mapper namespace="Mapper.UserMapper">   <!-- 命名空间，名字可以随意起，只要不冲突即可 -->
<!-- id是标签的唯一标识，用来区分不同的查询标签 -->
<!-- 查询功能，resultType 设置返回值类型  就是返回出来的对象-->
<select id="findAll" resultType="com.User">  <!-- 书写 SQL 语句 -->
    SELECT * FROM user_account where id = #{id}
</select>
<insert id="insert" parameterType="com.User">
    INSERT INTO user_account VALUES(#{id},#{username},#{password})
</insert>
<update id="update" parameterType="com.User">
    UPDATE user_account
    SET username = #{username},password = #{password}
    WHERE id = #{id}
</update>
<delete id="delete" parameterType="com.User">
    DELETE FROM user_account
    WHERE id = #{id}
</delete>
</mapper>
```



## 3.编写用户类

用来对数据进行操作，例如用户类(User)，有属性id、username、password，在类中实现set、get、toString方法，用户类编写完成之后就可以用来对数据进行操作



## 4.实现方法

要实Mybatis的使用，需要以下几个步骤

1. 引入配置文件

   要引入配置文件，首先可以将配置文件的类路径用一个String类型的resource变量给定义出来，然后用Resources.getResourceAsStream(resource)来实现一个输入流对象

2. 创建SqLSession对象

   要创建SqlSession对象，需要借用SqlSessionFactory对象来进行创建，顾名思义，SqlSessionFactory就是创建SqlSession对象的工厂

      SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream); 

    然后就可以直接获取sqlSession对象

      SqlSession sqlSession = sqlSessionFactory.openSession();

3. 进行数据操作

   关于数据库的操作 需要在try块中进行

   ```java
       //查询
       /*try{
           // 调用 mapper 中的方法：命名空间 + id
           User user = sqlSession.selectOne("Mapper.UserMapper.findAll",1);
           System.out.println(user);
       }finally{
           sqlSession.close();
       }*/
   
   
   
       //插入
       /*User user = new User();
       user.setId(2);
       user.setUsername("asd");
       user.setPassword("asd");
   
       sqlSession.insert("Mapper.UserMapper.insert",user);
       sqlSession.commit();
       sqlSession.close();*/
   
   
       //修改
       /*User user = sqlSession.selectOne("Mapper.UserMapper.findAll",2);
       user.setUsername("asd");
       user.setPassword("asd");
       sqlSession.update("Mapper.UserMapper.update",user);
       sqlSession.commit();
       sqlSession.close();*/
   
   
       //删除
       /*sqlSession.delete("Mapper.UserMapper.delete",2);
       sqlSession.commit();
       sqlSession.close();*/
   ```

   上述4个为对于数据库的增删改查，代码不全

    **在对数据库的操作完成之后，需要关闭sqlSession**

    以上便是Mybatis的简单实现



## SQL操作（XML文件编写）

MyBatis的强大之处就在于他的映射语句，而关键之处就是XML文件的编写

\#{id}和${id}的区别
在后台向数据库传送SQL语句的时候，参数的值尽量是不要直接放在SQL语句当中的
如果直接只用${id}作为取参数的话，参数值将会直接添加在SQL语句当中并传送到数据库
而如果使用#{id}来取参数，参数的值将不会直接显示在SQL语句当中，可以防止SQL注入

### select

在顶级元素select当中，有很多属性是允许你进行配置

1. id  在命名空间中唯一的标识符，可以用来引用这条语句，在SSM框架当中需要与Dao接口当中的方法名相同才可以进行映射

2. parameterType  会传入这条语句的参数类的完全限定名或别名，这个属性是可选的，Mybatis会自动推断出具体传入语句的参数

3. resultType  从这条语句中返回期望类型的完全限定名或别名

4. resultMap  外部resultMap的命名引用，与resultType选其一使用
5. flushCache  将其设置为true，只要语句被调用，都会导致本地缓存和二级缓存被清空
6. useCache  将其设置为true，将会导致本条语句的结果被二级缓存，对select元素的默认值为true
7. timeout  在抛出异常之前，驱动程序等待数据库返回请求结果的秒数
8. ...

### insert,update,delete

与select相差不多

### sql

这个元素可以用来定义可以重用的SQL代码段，可以包含在其他语句当中，它可以被静态地（在加载参数）参数化。

```xml
<sql id="userColumns">#{user}.id,#{user}.username,#{user}.password</sql>
```

这个SQL代码段可以被包含在其他语句当中

```xml
<select id="selectUsers" resultType="map">
    select
        <include refid="userColumns"><property name="alias" value="t1"/></include>
        <include refid="userColumns"><property name="alias" value="t2"/></include>
    from user_account t1
    cross join user_account t2
</select>
```

属性值也可以被用在include元素的refid属性里

```xml
<include refid="#{include_target}"/>
```

或include内部语句中

```xml
#{perfix}Table
```

### 参数（parameters）

#### resultMap

类型别名

对一个类进行类型别名设置以后就不用输入类的完全限定名称了

```xml
<typeAlias type="com.bean.User" alias="User"/>
<select id="selectUsers" resultType="User">
    select id,username,password
    from user_account
    where id = #{id};
</select>
```

动态SQL

在xml文件当中，可以使用条件语句来进行SQL语句的拼接

if

```xml
<select id="selectUserByid" resultType="User">
    select * from user_account where id = #{id}
    <if test="age != null">
        and age = #{age}
    </if>
</select>
```

这段查询语句的意思是：根据id查询出用户的信息，如果年龄不为空的话，那么判断条件再加上一个年龄

choose,when,otherwise

有时候不想应用到所有的条件语句，而只想从中选择出一项。针对这种情况，可以使用choose元素

 还是上面的例子，但是这次变为提供了age就按age查找，提供了性别，就按性别查找，如果两者都没有，就返回所有符合条件的用户信息

```xml
<select id="selectUserByid" resultType="User">
    select * from user_account where id = #{id}
    <choose>
        <when test="age != null">
            and age = #{age}
        </when>
        <when test="sex != null">
            and sex = #{sex}
        </when>
        <otherwise>
            and home = #{home}
        </otherwise>
    </choose>
</select>
```

trim,where,set

如果上面这个例子中，where的条件都不符合，otherwise也不符合，那么查询语句就会出错，这个时候，我们可以通过自定义trim元素来定制where元素的功能。比如，和where元素等价的自定义trim元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR">
    ...
</trim>
```

prefixOverrides属性会忽略通过管道分割的文本序列（这个例子当中的空格也是必要的），它的作用是一出所有指定在prefixOverrides属性中的内容，并且插入prefix属性中指定的内容

类似的用于动态更新语句的解决方案叫做set，set元素可以用于动态包含需要更新的列，而舍去其它的

```xml
<update>
    update user_account
        <set>
            <if test="username != null">username = #{username},</if>
            <if test="password != null">password = #{password},</if>
            <if test="age != null">age = #{age},</if>
            <if test="sex != null">sex = #{sex},</if>
            <if test="home != null">home = #{home}</if>     
        </set>
</update>
```

在这里，set元素会动态前置set关键字，同时也会删掉无关的逗号，

foreach

在对集合进行遍历的时候，就可以使用foreach

```xml
<select>
    select * from user_account
    where id in
    <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```

> item表示集合中每一个元素进行迭代时的别名，
>
> index指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选。
>
> open表示该语句以什么开始，
>
> separator表示在每次进行迭代之间以什么符号作为分隔 符，
>
> close表示以什么结束。

多数据库支持

CDATA

CDATA 指的是不应由 XML 解析器进行解析的文本数据

在编写sql语句的时候，可以使用CDATA来使解析器忽略这段语句

CDATA 部分由 

"<![CDATA["

开始，由

"]]>"

结束

在这段代码当中的sql语句，其中的<,&会被忽略，防止解析器把它们当做别的意思

"<" 会产生错误，因为解析器会把该字符解释为新元素的开始。

"&" 也会产生错误，因为解析器会把该字符解释为字符实体的开始。

提示

假如在select标签当中需要查询多个数据并用list来接收的时候，resultType中的属性类型是list中元素的类型，而不是list



## 缓存

一共有两级缓存

默认情况下，只有一级缓存开启（SqlSession级别的缓存，也成为本地缓存）

二级缓存需要手动开启和配置，它是基于namespace级别的缓存

为了提高扩展性，Mybatis提供了缓存接口Cache，可以通过Cache接口自定义二级缓存

### 一级缓存

一级缓存也叫本地缓存

与数据库同一次会话期间查询到的数据会放在本地缓存当中

如果需要获取相同的数据，直接从缓存当中拿，不需要再查询数据库

增删改操作会刷新缓存

只在一个sqlSession当中才有效

### 二级缓存

二级缓存也叫全局缓存，一级缓存的作用域太低了，所以诞生了二级缓存

基于namespace级别的缓存，一个名称空间，对应一个二级缓存

只要开启了二级缓存，在同一个mapper下面就有效

所有的数据都会先放在以及缓存当中，只有当会话提交，或者关闭之后，才会交到二级缓存当中



工作机制：

一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存当中

如果当前会话关闭了，这个会话对应的一级缓存就没了，但是我们想要的是，会话关闭之后，一级缓存的数据被保存到二级缓存当中

新的会话查询信息，就可以从二级缓存当中获取内容

不同的mapper查出的数据会放在自己对应的缓存（map）当中



第一次查询，先看二级缓存当中有没有，再看一级缓存中有没有，最后查数据库



### 关于Mybatis缓存的思考

我觉得，一级缓存在某些情况下是很有效的，但是二级缓存我觉得可以使用Redis来进行代替

原因如下：

1. 任何修改数据库的操作，都会导致二级缓存失效
2. 根据二级缓存的原理，它是从一级缓存当中得到的，而一级缓存是查询数据库得到的，那么相比较之下，与其使用二级缓存这种不稳定（指的是缓存失效的场景）的缓存，不如直接在数据库前加上一层redis缓存，然后搭配mybatis进行使用，如果redis当中没有数据，那么再往数据库当中查，最后将查到的结果返回到redis当中，并且增删改不会直接失效整个缓存。这样的话，就可以保证缓存的稳定性。



# SpringMVC

## servelet

![image-20200616152553580](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200616152553580.png)

## SpringMVC

### SpringMVC运行流程

 在SpringMVC的运行机制当中，通过以下几个组件来进行工作

1. DispatcherServlet：前端控制器，是整个流程控制的中心，控制其他组件处理用户的请求，统一调度，降低组件之间的耦合性，提高每个组件的扩展性。是一个总指挥。
2. HandlerMapping：处理器映射器，根据用户发来请求的url找到Handler，有多种映射方式，如：配置文件方式，注解方式等。
3. Handler：处理器，也可叫做Controller(控制器)，他是一个后端控制器，在前端控制器的控制下，对具体的用户请求进行处理，因为涉及到具体的用户业务请求，所以需要由工程师根据业务需求进行开发。
4. ViewResolver：视图解析器，进行试图解析，首先根据逻辑视图名解析成物理视图名(即具体的页面地址)，再生成View视图对象，最后对View进行渲染，将处理结果通过页面展示给用户。



**SpringMVC的具体流程步骤**

1. 用户发送请求到DispatcherServlet前端控制器
2. 前端控制器收到请求之后，调用HandlerMapping处理器映射器来寻找Handler处理器
3. 处理器映射器找到处理器之后，返回一个执行链给前端控制器
4. 前端控制器向HandlerAdapter处理器适配器发送请求调用处理器
5. 处理器适配器调用处理器进行业务的处理
6. 处理器执行完业务处理之后，返回一个ModelAndView给处理器适配器
7. 处理器适配器再将ModelAndView返回给前端控制器
8. 前端控制器请求ViewResolver视图解析器对ModelAndView进行试图解析
9. 视图解析器解析完视图之后生成一个View并将其返还给前端控制器
10. 前端控制器对视图进行渲染，然后向用户返回一个响应的结果



### 参数绑定

 从客户端请求key/value数据，经过参数绑定，将key/value数据绑定到controller方法的形参上

 springmvc中，接受页面提交的数据是通过方法形参来接受，而不是在controller类定义成员变量接收



 springmvc中提供了许多的converter(转换器)来进行任意类型的转换

 在特殊情况下（例如日期数据的绑定，需要定义数据格式）需要自定义converter



 参数绑定默认支持的类型可以直接在controller方法的形参上定义这些类型的对象，就可以直接使用这些对象

1. HttpServletRequest-----------通过request对象获取请求信息

2. HttpServletResponse----------通过response处理响应信息

3. HttpSession------------------通过session对象得到session中存放的对象

4. Model/ModelMap---------model是一个接口，modelMap是一个接口实现 定义两者都行，model定义后实现的是modelMap，用来将model数据填充到request域

5. 简单类型：通过@RequestParam对简单类型的参数进行绑定，如果不使用该注解，要求request传入的参数名称和controller方法的形参名一致才可以绑定成功

   > 如果使用注解，不用进行限制
   > 该注解可以这样使用 @RequestParam(value="" required="")
   > value表示绑定的参数名称
   > 通过required属性指定参数是否必须要传入，如果设置为true，没有传入参数那么就会报错

 **简单来说就是客户端是什么样子的，才能绑定到形参上面**



### pojo绑定

页面中input的name和controller的pojo形参中的属性名称一致，将页面中数据绑定到pojo

自定义参数绑定实现日期类型绑定

对于controller形参中pojo对象，如果属性中有日期类型，需要自定义参数绑定
将请求日期数据串传成日期类型，要转换的日期类型和pojo中日期属性的类型保持一致

需要进行配置文件配置

数组绑定

将页面选择(多选)的商品id，传到controller方法的形参，方法形参使用数组接收页面请求的多个商品id 

**总之，要传什么，就在jsp中的name属性中填写清楚**



### 数据回显

提交后，如果出现错误，将刚才提交的数据回显到刚才的提交页面

pojo数据回显方法

springmvc默认对pojo数据进行回显

pojo数据传入controller方法后，springmvc自动将pojo数据放到request域，key等于pojo类型（首字母小写）

用@ModelAttribute指定pojo回显到页面在request中的key

最简单的可以用model，直接.addAttribute可以进行回显，此方法可以用于简单类型数据回显



### 拦截器(Interceptor)

拦截器是基于AOP，用于对处理器进行预处理和后处理

首先，拦截器类Interceptor需要实现(implements)HandlerInterceptor接口

接口中提供3个方法

1. preHandle
   进入Handler方法之前执行
   可用于身份认证、身份授权(登录认证、权限校验)
   比如身份认证，如果认证不通过，表明用户没有登录，需要此方法进行拦截，不再向下执行
   该方法的返回值 false(表示拦截，不向下执行)
   true(表示放行，继续向下执行)

2. postHandle
   进入Handler方法之后，返回modelAndView之前执行
   应用场景从modelAndView出发：将公用的模型数据(如菜单导航)在这里传到视图，也可以在这里统一制定视图

3. afterCompletion
   执行Handler方法之后执行
   可用于统一异常处理，统一日志处理

拦截器配置


当有两个拦截器且都放行的时候

preHandle按顺序执行
postHandle和afterCompletion按逆序执行

拦截器1放行，拦截器2不放行的时候

拦截器1放行，拦截器2的preHandle方法才执行
拦截器2的preHandle方法不放行，拦截器2的postHandle和afterCompletion不执行

只要有一个拦截器不放行，postHandle方法就不会执行

两个拦截器都不放行的时候

拦截器1preHandle不放行，postHandle和afterHandle都不执行
拦截器1preHandle不放行，拦截器2不执行

> 比如：统一日志处理拦截器，需要该拦截器preHandle一定要放行，且将它放在拦截器链接中的第一个位置
>
> 比如：登陆认证拦截器，放在拦截器链接中第一个位置；权限校验拦截器放在登陆认证拦截器之后(因为登陆通过后进行权限校验)



# SpringBoot

自动装配原理

https://blog.csdn.net/Dongguabai/article/details/80865599

启动过程

https://www.jianshu.com/p/603d125f21b3