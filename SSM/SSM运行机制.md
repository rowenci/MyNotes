#SSM运行机制
##

SSM框架：Spring + SpringMVC + Mybatis

由SSM将其搭建而成的系统划分为表现层，Controller层，Service层，Dao层。

Spring：作为整个框架中最重要的部分，Spring将SpringMVC和Mybatis粘合在一起，使两者能相互合作进行工作

SpringMVC：在项目中拦截用户请求，对用户的请求进行处理。

Mybatis：对jdbc的封装，是数据持久化的工具。

运行机制参考SpringMVC的运行机制，主要增加的工作就是通过Mybatis与数据库进行交互

编写步骤：

DataBase ----> Bean ----> Dao.java ----> Dao.xml ----> Service.java ----> ServiceImpl.java ----> Controller.java ---->jsp