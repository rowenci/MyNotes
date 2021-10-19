#Spark集群搭建
##

**本笔记是建立在hadoop集群搭建成功之上**

##

###1.Spark安装

首先吸取教训，不需要三个机器上都分别安装，只用先在master节点上面安装，什么东西都配置完成之后再直接scp到slave节点上面即可

下载解压步骤省略。。。

###2.Spark配置

1./etc/profile

直接加上

	export SPARK_HOME=/usr/lib/spark/spark-2.4.2-bin-hadoop2.7
	
	export PATH=$PATH:${JAVA_HOME}/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$SPARK_HOME/bin:$SPARK_HOME/sbin

分别是sparkhome和path的环境变量

添加完成之后直接source /etc/profile

###conf文件夹当中有两个文件，分别是 spark-env.sh.template 和slaves.template

1.spark-env.sh

将spark-env.sh.template直接复制成为上述名称，然后更改

	export HADOOP_CONF_DIR=/usr/lib/hadoop/hadoop-2.9.2/etc/hadoop
	export HADOOP_HOME=/usr/lib/hadoop-2.9.2
	export SPARK_MASTER_IP=192.168.1.11
	export SPARK_DIST_CLASSPATH=$(/usr/lib/hadoop/hadoop-2.9.2/bin/hadoop classpath)
	export JAVA_HOME=/usr/lib/java/jdk1.8.0_201

直接加

2.slaves

同上，直接在文件当中先删除localhost，然后加上slave节点的ip或者hostname

如

	hadoop-2
	hadoop-3

##

文件配置完成后，用scp命令分别传输到slave节点上面

然后同样修改slave节点上面的/etc/profile环境变量 修改完后source一下即可

##

下面就是启动spark集群

启动spark集群首先需要启动hadoop集群

hadoop集群启动之后，进入spark/sbin 运行start-master.sh和start-slaves.sh

最后使用jps命令查看会发现 master节点上会多出一个master进程，slave节点上面会多出一个worker进程

##

查看spark集群状态可以在浏览器中输入master节点ip，端口是8080

至此，spark集群搭建完成

**注意，spark on yarn模式是指，在运行作业的时候是由yarn来决定的，所以需要在提交作业的时候指定yarn模式，而不是部署的时候就直接是spark on yarn模式，**

下面是提交一个计算pi的值的例子，用的是spark on yarn模式

	spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode cluster /home/rowenci/spark-2.4.2-bin-hadoop2.7/examples/jars/spark-examples*.jar

查看作业结果需要在yarn监控页面查看

http://192.168.1.11:8088/cluster

点击应用，在logs中查看stdout就是结果