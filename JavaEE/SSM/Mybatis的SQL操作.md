#SQL操作（XML文件编写）
##

MyBatis的强大之处就在于他的映射语句，而关键之处就是XML文件的编写

	#{id}和${id}的区别
	在后台向数据库传送SQL语句的时候，参数的值尽量是不要直接放在SQL语句当中的
	如果直接只用${id}作为取参数的话，参数值将会直接添加在SQL语句当中并传送到数据库
	而如果使用#{id}来取参数，参数的值将不会直接显示在SQL语句当中，可以防止SQL注入

###select

在顶级元素select当中，有很多属性是允许你进行配置

>id    在命名空间中唯一的标识符，可以用来引用这条语句，在SSM框架当中需要与Dao接口当中的方法名相同才可以进行映射

>parameterType    会传入这条语句的参数类的完全限定名或别名，这个属性是可选的，Mybatis会自动推断出具体传入语句的参数

>resultType    从这条语句中返回期望类型的完全限定名或别名

>resultMap    外部resultMap的命名引用，与resultType选其一使用

>flushCache    将其设置为true，只要语句被调用，都会导致本地缓存和二级缓存被清空

>useCache    将其设置为true，将会导致本条语句的结果被二级缓存，对select元素的默认值为true

>timeout    在抛出异常之前，驱动程序等待数据库返回请求结果的秒数

>...

###insert,update,delete

与select相差不多

###sql

这个元素可以用来定义可以重用的SQL代码段，可以包含在其他语句当中，它可以被静态地（在加载参数）参数化。

	<sql id="userColumns">#{user}.id,#{user}.username,#{user}.password</sql>

这个SQL代码段可以被包含在其他语句当中

	<select id="selectUsers" resultType="map">
		select
			<include refid="userColumns"><property name="alias" value="t1"/></include>
			<include refid="userColumns"><property name="alias" value="t2"/></include>
		from user_account t1
		cross join user_account t2
	</select>

属性值也可以被用在include元素的refid属性里

	<include refid="#{include_target}"/>

或include内部语句中
	
	#{perfix}Table

###参数（parameters）

##resultMap

###类型别名

对一个类进行类型别名设置以后就不用输入类的完全限定名称了

	<typeAlias type="com.bean.User" alias="User"/>
	<select id="selectUsers" resultType="User">
		select id,username,password
		from user_account
		where id = #{id};
	</select>

##
###动态SQL

在xml文件当中，可以使用条件语句来进行SQL语句的拼接

###if

	<select id="selectUserByid" resultType="User">
		select * from user_account where id = #{id}
		<if test="age != null">
			and age = #{age}
		</if>
	</select>

这段查询语句的意思是：根据id查询出用户的信息，如果年龄不为空的话，那么判断条件再加上一个年龄

###choose,when,otherwise

有时候不想应用到所有的条件语句，而只想从中选择出一项。针对这种情况，可以使用choose元素

还是上面的例子，但是这次变为提供了age就按age查找，提供了性别，就按性别查找，如果两者都没有，就返回所有符合条件的用户信息

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

###trim,where,set

如果上面这个例子中，where的条件都不符合，otherwise也不符合，那么查询语句就会出错，这个时候，我们可以通过自定义trim元素来定制where元素的功能。比如，和where元素等价的自定义trim元素为：

	<trim prefix="WHERE" prefixOverrides="AND |OR">
		...
	</trim>

prefixOverrides属性会忽略通过管道分割的文本序列（这个例子当中的空格也是必要的），它的作用是一出所有指定在prefixOverrides属性中的内容，并且插入prefix属性中指定的内容

类似的用于动态更新语句的解决方案叫做set，set元素可以用于动态包含需要更新的列，而舍去其它的

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

在这里，set元素会动态前置set关键字，同时也会删掉无关的逗号，

###foreach

在对集合进行遍历的时候，就可以使用foreach

	<select>
		select * from user_account
		where id in
		<foreach item="item" index="index" collection="list" open="(" separator="," close=")">
			#{item}
		</foreach>
	</select>

>item表示集合中每一个元素进行迭代时的别名，

>index指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选。

>open表示该语句以什么开始，

>separator表示在每次进行迭代之间以什么符号作为分隔 符，

>close表示以什么结束。

###多数据库支持

###CDATA

CDATA 指的是不应由 XML 解析器进行解析的文本数据

在编写sql语句的时候，可以使用CDATA来使解析器忽略这段语句

CDATA 部分由 "<![CDATA[" 开始，由 "]]>" 结束

在这段代码当中的sql语句，其中的<,&会被忽略，防止解析器把它们当做别的意思

"<" 会产生错误，因为解析器会把该字符解释为新元素的开始。

"&" 也会产生错误，因为解析器会把该字符解释为字符实体的开始。

###提示

假如在select标签当中需要查询多个数据并用list来接收的时候，resultType中的属性类型是list中元素的类型，而不是list