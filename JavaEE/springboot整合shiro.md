#springboot整合shiro
##

###shiro的核心api

subject : 用户主题(把操作交给SecurityManager)

SecurityManager : 安全管理器(关联realm)

Realm : shiro连接数据的桥梁

###spring整合shiro

1.修改pom.xml(引入shiro的maven配置)

2.自定义Realm类

>1.extends AuthorizingRealm

>2.编写doGetAuthorizationInfo(授权)

>3.编写doGetAuthenticationInfo(认证)

3.编写shiro的配置类

>需要加上@Configuration注解

>1.创建ShiroFilterFactoryBean(@Bean)

>2.创建DefaultWebSecurityManager(@Bean)

>3.创建Realm(加上注解@Bean 让方法返回的对象放回spring的环境当中,用来进行依赖注入)

springboot整合shiro,只需要以上步骤即可整合成功**注:此时的shiro还需要进一步配置才能实现响应的功能**

###shiro功能配置

见springbootsm项目

###springboot+mybatis+shiro

首先整合过程直接见springbootsm项目

需要注意的一点，mapper和service各种类名之前的注解都需要加上，否则，在项目启动的时候，springboot会进行报错，显示在shiroconfig当中的一些方法会注入错误。

如：

UserMapper当中的 @Repository和@Mapper

UserServiceImpl当中的 @Service

ShiroConfig当中的 @Configuration 以及方法上面的 @Bean