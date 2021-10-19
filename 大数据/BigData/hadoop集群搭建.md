#Start

Hadoop是用java编写的，所以如果要在服务器上部署hadoop，必须要在服务器上安装好java环境

在我的虚拟机中，java的环境:

vim /etc/profile

JAVA_HOME=/usr/lib/jdk/jdk1.8.0_191
JRE_HOME=${JAVA_HOME}/jre
CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
PATH=${JAVA_HOME}/bin:$PATH

source /etc/profile

##

关于hadoop的运行模式有以下三种

1.单机模式

在默认情况下，hadoop被配置成以非分布式模式运行的一个独立的java进程，这对调试非常有帮助

也就是说，这种模式下，hadoop只会在一个java进程当中运行

2.伪分布式模式

hadoop可以在单节点上以所谓的伪分布式模式运行，此时每个hadoop守护进程都作为一个独立的java进程运行

也就是说，在这种模式下，hadoop能同时在多个java进程当中运行，这种情况也就模拟了一种分布式的方式，但是这种情况下的hadoop是在单独一个节点上面运行的

3.完全分布式模式

这种情况下就是hadoop在不同的节点当中运行，也就是真正的分布式
##

###服务器初始化

1.ip地址修改

ubuntu 18以后需要按照以下步骤修改

	vim /etc/netplan/50-cloud-init.yaml
	network:
		ethernets:
		ens33:
			address: [192.168.1.11/24]
			gateway4: 192.168.1.1
			nameservers:
					addresses: [192.168.1.1,8.8.8.8]

修改完之后，netplan apply

修改主机名： /etc/hostname
修改host文件：/etc/host

2.关闭防火墙
	
	ufw disable

3.配置ssh免密登陆

vim /etc/hostname
vim /etc/hosts

>1.修改ssh配置文件

	vim /etc/ssh/sshd_config
	将
	# Authentication:
	LoginGraceTime 120
	PermitRootLogin without-password
	StrictModes yes
	改成
	PermitRootLogin yes

重启ssh服务：service ssh restart

>2.生成公钥私钥

	ssh-keygen
	直接按回车即可

	cat id_rsa.pub >> authorized_keys 
	加入授权

>3.将生成的公钥scp传送给两台虚拟机的authorized_keys授权列表文件中

	ssh-copy-id bdplatform-2
	ssh-copy-id bdplatform-3
	在其他两个服务器中一样，只是发送的地址不同

免密登陆配置完成

至此，主要的服务器初始化已经完成，可以直接使用xshell连接然后操作，可以直接安装hadoop等软件了
##

下载hadoop可以直接从官网下载，最好下载binary版本的hadoop，可以直接使用

当下载完成并解压之后，可以看见hadoop文件夹当中有以下几个目录

	bin:Hadoop最节本的管理脚本和使用脚本的目录，这些脚本是sbin目录下管理脚本的基础实现，用户可以直接使用这些脚本管理和使用hadoop
	
	etc:Hadoop配置文件所在的目录
	
	include:对外提供的编译库头文件
	
	lib:该目录包含了hadoop对外提供的编程动态库和静态库,与include中的头文件结合使用
	
	libexec:各个服务对用的shell配置文件所在的目录,可用于配置日志输出,启动参数(如JVM参数)等基本信息
	
	sbin:Hadoop管理脚本所在的目录,主要包含HDFS和YARN中各类服务的启动/关闭脚本
	
	share:Hadoop各个模块编译后的jar包所在的目录

##

###配置文件编写

1.hadoop-env.sh

export JAVA_HOME=/usr/lib/jdk/jdk1.8.0_191

2.core-site.xml

	<configuration>
        <property>
                <name>fs.default.name</name>
                <value>hdfs://192.168.209.136:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/var/hadoop</value>
        </property>
	</configuration>

**注：假如放在/tmp/hadoop当中，文件就会被定时清理，所以不能放在/tmp文件夹当中，用来保证数据不会丢失**



3.hdfs-site.xml

	<configuration>
	        <property>
	                <name>dfs.namenode.secondary.http-address</name>
	                <value>192.168.209.139:50090</value>
	        </property>
	</configuration>

4.mapred-site.xml

	<configuration>
	        <property>
	                <name>mapreduce.framework.name</name>
	                <value>yarn</value>
	        </property>
	</configuration>

5.yarn-site.xml

	<configuration>
	
	<!-- Site specific YARN configuration properties -->
	        <property>
	                <name>yarn.resourcemanager.hostname</name>
	                <value>192.168.209.136</value>
	        </property>
	        <property>
	                <name>yarn.nodemanager.aux-services</name>
	                <value>mapreduce_shuffle</value>
	        </property>
	
	</configuration>

6.slaves

	192.168.209.136
	192.168.209.139
	192.168.209.138


7.环境变量配置

	export JAVA_HOME=/usr/lib/jdk/jdk1.8.0_191
	export JRE_HOME=${JAVA_HOME}/jre    
	export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib    
	export HADOOP_HOME=/usr/lib/hadoop/hadoop-2.9.2
	export PATH=$PATH:${JAVA_HOME}/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

8.使环境变量文件生效

	source /etc/profile

##
###启动前

首次启动前需要进行格式化

格式化本质是进行文件系统的初始化操作 创建一些自己所需要的文件

格式化之后 集群启动成功 后续再也不要进行格式化

格式化的操作在hdfs集群的主角色（namenode）所在的机器上操作

	hdfs namenode -format

##
###启动方式

集群一键启动
	
	start-dfs.sh
	
	start-yarn.sh

关闭

	stop-dfs.sh
	
	stop-yarn.sh

启动成功后

	root@hadoop-1:/usr/lib/hadoop/hadoop-2.9.2/sbin# ./start-dfs.sh 
	Starting namenodes on [hadoop-1]
	hadoop-1: starting namenode, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/hadoop-root-namenode-hadoop-1.out
	hadoop-3: starting datanode, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/hadoop-root-datanode-hadoop-3.out
	hadoop-1: starting datanode, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/hadoop-root-datanode-hadoop-1.out
	hadoop-2: starting datanode, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/hadoop-root-datanode-hadoop-2.out
	Starting secondary namenodes [hadoop-2]
	hadoop-2: starting secondarynamenode, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/hadoop-root-secondarynamenode-hadoop-2.out
	root@hadoop-1:/usr/lib/hadoop/hadoop-2.9.2/sbin# ./start-yarn.sh 
	starting yarn daemons
	starting resourcemanager, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/yarn-root-resourcemanager-hadoop-1.out
	hadoop-3: starting nodemanager, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/yarn-root-nodemanager-hadoop-3.out
	hadoop-1: starting nodemanager, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/yarn-root-nodemanager-hadoop-1.out
	hadoop-2: starting nodemanager, logging to /usr/lib/hadoop/hadoop-2.9.2/logs/yarn-root-nodemanager-hadoop-2.out
	root@hadoop-1:/usr/lib/hadoop/hadoop-2.9.2/sbin# 


使用jps命令

hadoop-1中：

	37968 DataNode
	36869 ResourceManager
	38374 Jps
	37049 NodeManager
	37806 NameNode

hadoop-2中：

	35780 NodeManager
	36628 Jps
	36326 DataNode
	36487 SecondaryNameNode

hadoop-3中：

	35744 DataNode
	35409 NodeManager
	35916 Jps

**每次在hadoop-1中修改完配置文件之后，都需要做以下动作（包括格式化之后）**

	 scp -r /usr/lib/hadoop/hadoop-2.9.2/ root@hadoop-2:/usr/lib/hadoop/

将hadoop远程发送到所有的节点当中

日志文件

	starting namenode, logging to /home/rowenci/hadoop-2.9.2/logs/hadoop-root-namenode-bdplatform-1.out

##

启动之后，就可以从浏览器当中查看

namenode http://192.168.209.136:50070

resourcemanager http://192.168.209.136:8088