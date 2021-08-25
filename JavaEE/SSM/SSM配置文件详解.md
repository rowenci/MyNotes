#SSM框架配置文件详解
##

在SSM项目当中，所需要的配置文件总共有以下几个

>1.web.xml

>2.applicationContext.xml

>3.springmvc.xml

下面将会依次详细介绍三个配置文件的配置方法以及配置的含义

###1.web.xml

web.xml是ssm项目当中最重要的一个配置文件，当服务启动时会首先加载web.xml这个文件，里面包括了对前端控制器、乱码等的配置

下面贴上代码，代码当中有详细的注释解释每个配置的意思

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	         version="4.0">
	
	    <!--全局范围内参数初始化-->
	    <context-param>
	        <param-name>contextConfigLocation</param-name>              <!--参数名称-->
	        <param-value>classpath:applicationContext.xml</param-value> <!--参数取值-->
	    </context-param>
	    <context-param>
	        <param-name>AREACODE</param-name>
	        <param-value>410800000000</param-value>
	    </context-param>
	
	    <!--spring的监听器-->
	    <listener>
	        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	    </listener>
	
	    <!--springMVC的配置-->
	    <!--前端控制器-->
	    <servlet>
	        <servlet-name>springmvc</servlet-name>
	        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	        <init-param>
	            <param-name>contextConfigLocation</param-name>
	            <!-- 此处不配置 默认找 /WEB-INF/[servlet-name]-servlet.xml -->
	            <param-value>classpath:springmvc.xml</param-value>
	        </init-param>
	        <load-on-startup>1</load-on-startup>
	    </servlet>
	    <servlet-mapping>
	        <servlet-name>springmvc</servlet-name>
	        <!--
	        1:*.do *.action 拦截以.do结尾的请求 (不拦截 jsp png jpg .js .css)
			2:/ 拦截所有请求 (不拦截.jsp) 建议使用此种 方式 （拦截 .js.css .png) (放行静态资源)
			3:/* 拦截所有请求（包括.jsp) 此种方式 不建议使用
			-->
	        <url-pattern>*.do</url-pattern>
	    </servlet-mapping>
	
	    <!--<servlet>
	        <servlet-name>imageServlet</servlet-name>
	        <servlet-class>com.utils.PageAutocode</servlet-class>
	    </servlet>
	    <servlet-mapping>
	        <servlet-name>imageServlet</servlet-name>
	        <url-pattern>/servlet/imageServlet</url-pattern>
	    </servlet-mapping>-->
	
	
	    <!--如果某个会话在一定时间内未被访问，则服务器可以扔掉来节约内存  单位为分钟-->
	    <session-config>
	        <session-timeout>60</session-timeout>
	    </session-config>
	
	    <servlet>
	        <servlet-name>services</servlet-name>
	        <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
	    </servlet>
	    <servlet-mapping>
	        <servlet-name>services</servlet-name>
	        <url-pattern>/services/*</url-pattern>
	    </servlet-mapping>
	
	    <!-- 静态资源文件的引入 -->
	    <servlet-mapping>
	        <servlet-name>default</servlet-name>
	        <url-pattern>*.js</url-pattern>
	    </servlet-mapping>
	    <servlet-mapping>
	        <servlet-name>default</servlet-name>
	        <url-pattern>*.css</url-pattern>
	    </servlet-mapping>
	    <servlet-mapping>
	        <servlet-name>default</servlet-name>
	        <url-pattern>*.png</url-pattern>
	    </servlet-mapping>
	
	    <!--过滤器-->
	    <filter>
	        <description>字符集过滤器</description>
	        <filter-name>encodingFilter</filter-name>
	        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	        <init-param>
	            <description>字符集编码</description>
	            <param-name>encoding</param-name>
	            <param-value>UTF-8</param-value>
	        </init-param>
	    </filter>
	    <filter-mapping>
	        <filter-name>encodingFilter</filter-name>
	        <url-pattern>/*</url-pattern>
	    </filter-mapping>
	
	    <!--直接输入项目名进入时返回的页面-->
	    <welcome-file-list>
	        <welcome-file>index.jsp</welcome-file>
	    </welcome-file-list>
	</web-app>

###2.applicationContext.xml

applicationContext.xml的作用是为了给spring进行配置

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
							http://www.springframework.org/schema/beans/spring-beans.xsd
							http://www.springframework.org/schema/context
							http://www.springframework.org/schema/context/spring-context-4.0.xsd">
	
	    <context:annotation-config />
	    <!--自动扫描，将标注spring注解的类自动转化bean-->
	    <context:component-scan base-package="com.service" />
	
	
	    <!--配置数据库-->
	    <bean id="dataSource"
	          class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	        <property name="driverClassName">
	            <value></value>
	        </property>
	        <property name="url">
	            <value>
	            </value>
	
	        </property>
	        <property name="username">
	            <value></value>
	        </property>
	        <property name="password">
	            <value></value>
	        </property>
	    </bean>
	
	    <!--配置sessionfactory-->
	    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
	        <property name="typeAliasesPackage" value="com.bean" />
	        <!--绑定数据源-->
	        <property name="dataSource" ref="dataSource" />
	        <!--自动扫描dao.xml文件-->
	        <property name="mapperLocations" value="classpath:com/mapper/*.xml" />
	    </bean>
	    <!--装配dao接口-->
	    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	        <property name="basePackage" value="com.mapper" /> <!-- DAO接口所在包名，Spring会自动查找其下的类dao.class -->
	    </bean>
	
	    <!-- spring扫描service层@Service Controller交给springmvc管理 -->
	    <context:component-scan base-package="com">
	        <context:exclude-filter type="annotation"
	                                expression="org.springframework.stereotype.Controller" />
	    </context:component-scan>
	
	</beans>

###springmvc.xml

springmvc.xml是为了配置springMVC

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:mvc="http://www.springframework.org/schema/mvc"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
			http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
	
	
	    <context:annotation-config />
	
	    <!-- springmvc扫包 @Controller, @Service ... -->
	    <!-- use-default-filters="false" 加上这个为了让springmvc扫完controller层就停止扫描 -->
	    <context:component-scan base-package="com.controller"
	                            use-default-filters="false">
	        <context:include-filter type="annotation"
	                                expression="org.springframework.stereotype.Controller" />
	    </context:component-scan>
	
	
	    <!-- 注册转化器 -->
	    <mvc:annotation-driven />
	    <!-- 处理静态资源的 -->
	    <mvc:default-servlet-handler />
	
	    <!-- 视图解析器 -->
	    <bean id="jspViewResolver"
	          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <!-- 前缀和后缀 -->
	        <property name="prefix" value="/WEB-INF/jsp/" />
	        <property name="suffix" value=".jsp" />
	    </bean>
	
	    <!--<mvc:interceptors>
	        &lt;!&ndash; 登录权限拦截器 &ndash;&gt;
	        <mvc:interceptor>
	            <mvc:mapping path="/**" />
	            <mvc:exclude-mapping path="/welcomeController/*"/>
	            <mvc:exclude-mapping path="/loginController/login.do"/>
	            <mvc:exclude-mapping path="/loginController/check.do"/>
	            <mvc:exclude-mapping path="/loginController/exit.do"/>
	            <mvc:exclude-mapping path="/registController/*"/>
	            <bean class="com.interceptor.LoginInterceptor">
	            </bean>
	        </mvc:interceptor>
	    </mvc:interceptors>-->
	
	</beans>

据本人亲自整合，在jar包导入完全的时候，这些配置文件可直接使用（数据库连接参数被删除）