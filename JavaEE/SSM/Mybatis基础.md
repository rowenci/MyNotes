#Mybatis
##

Mybatis使用分为四个步骤

1.编写配置文件（configFile）
	
2.编写映射文件

3.编写实际对象类

4.实现方法

###1.编写配置文件

* 1.1.文件开头的一串是关于配置文件的约束

		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE configuration
		        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
		        "http://mybatis.org/dtd/mybatis-3-config.dtd">

这些东西可以直接复制粘贴

* 1.2.在配置文件当中，配置是以标签形式来编写的，类似于android中的app配置文件

* 1.3.configuration标签

configuration标签只有一个，其中包含了所有关于Mybatis的配置，所有的配置都是在configuration标签中编写的

* 1.3.1.environment标签

environment标签可以有多个，方便在使用时进行切换，不同的environment标签用id属性来进行区别

* 1.3.1.1.transaction标签

当中的type属性有两个值：（JDBC|MANAGED），代表了不同的事务管理类型

* 1.3.1.2.dataSource标签

type属性代表不同的数据源类型

* 1.3.1.2.1.property标签

property标签是用来配置数据库连接属性的

每一个property标签配置一个数据库连接属性，分别用name进行区别，与普通JDBC相同，进行数据库连接需要注册驱动程序，数据库地址，用户名，密码。

* 1.3.2.mappers标签

mappers标签中包含了所有的注册映射文件的标签

* 1.3.2.1.mapper标签

用来注册映射文件，resource属性代表了mapper文件的类地址，路径用斜线（/）进行分割，而不是用点（.）

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

###2.编写映射文件

* 1.1.mapper文件开头是一串固定约束

		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE mapper
		        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
		        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

* 1.2.mapper标签

mapper标签中的namespace属性为命名空间，可以随意起
目的是为了区分映射文件中的方法

* 1.2.1.在mapper标签中进行增删改查操作

增删改查操作分别对应四个标签
select
insert
update
delete

这四个标签中都有属性id，用来作为对于相同操作的标签的区分

还有返回值类型属性

SQL语句就书写在这四种标签当中，用开始标签和结束标签作为SQL语句的开始和结束。

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

###3.编写用户类

用来对数据进行操作，例如用户类(User)，有属性id、username、password，在类中实现set、get、toString方法，用户类编写完成之后就可以用来对数据进行操作

###4.实现方法

要实Mybatis的使用，需要以下几个步骤

1.引入配置文件

2.创建SqLSession对象

3.进行数据操作

* 4.1.引入配置文件

要引入配置文件，首先可以将配置文件的类路径用一个String类型的resource变量给定义出来，然后用Resources.getResourceAsStream(resource)来实现一个输入流对象

* 4.2.创建SqLSession对象

要创建SqlSession对象，需要借用SqlSessionFactory对象来进行创建，顾名思义，SqlSessionFactory就是创建SqlSession对象的工厂

	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream); 

然后就可以直接获取sqlSession对象

	SqlSession sqlSession = sqlSessionFactory.openSession();

* 4.3.进行数据操作

关于数据库的操作 需要在try块中进行

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
        

上述4个为对于数据库的增删改查，代码不全

**在对数据库的操作完成之后，需要关闭sqlSession**

##

以上便是Mybatis的简单实现