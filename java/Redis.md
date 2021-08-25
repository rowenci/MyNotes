# Redis



## ACID

A：Atomicity（原子性）

C：Consistency（一致性）

I：Isolation（独立性）

D：Durability（持久性）



## CAP

C：Consistency（强一致性）

A：Availability（可用性）

p：Partition tolerance（分区容错性）

一个分布式系统，最多满足两种

CA：单点集群，满足一致性、可用性的系统，通常在可扩展性上不太强大

​		如：传统数据库

CP：满足一致性、分区容忍性的系统，通常性能不是特别高

​		大多数网站架构的选择

AP：满足可用性、分区容忍性的系统，通常对一致性要求低一些

​		redis、mongoDB



## BASE

BA：Basically Available（基本可用）

S：Soft state（软状态）

E：Eventually consistent（最终一致性）

通过让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上的改观

大型系统由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，必须采用另外一种方式来完成，那就是BASE



## 常用命令

切换数据库：select + 数据库index（默认使用0号库）

查看当前数据库的key的数量：dbsize

查看所有key：keys *

清除当前库所有key：flushdb

清除所有库的所有key：flushall



常见数据类型操作命令：http://redisdoc.com/



## 数据类型



### key

查询所有key：keys *

判断某个key是否存在：exists key

移除：move key db

为指定key设置过期时间：expire key + seconds

查看key还有多久过期：ttl key

查看key是什么类型：type key



### String（字符串）

二进制安全的数据类型，可以包含任何数据，比如jpg图片和序列化的对象

什么是二进制安全？就是存进去是什么二进制那么出来就是什么二进制，redis不会对redis进行编解码，编解码是程序员的事情

一个字符串最多可以使512M



set/get/del/append/strlen

incr（加）/decr（减）/incrby（加n个）/decrby（减n个），一定要是数字才能进行加减

getrange（获取指定索引内的值如0-3）/setrange（设置指定索引内的值如setrange key 0 xxx）

setex(set with expire) 键秒值 到期之后直接清除 setex k4 10 v4 / setnx(set if not exist)

mset/mget/msetnx（同时进行多个赋值 mset k1 v1 k2 v2 k3 v3		mget  k1 k2 k3	-	v1 v2 v3）

getset(先get再set)



### Hash（哈希，类似java里的map）

是一个键值对集合，是String类型的field和value的映射表，hash特别适合用于存储对象

类似Java里面的Map<String, Object>

同样是KV，但是V也是一个键值对



应用场景：

存对象

商品详情页







hset/hget/hmset/hmget/hgetall/hdel

hlen

hexists key 在key里面的某个值的key

hkeys/hvals

hincrby/hincrbyfloat

hsetnx



### List（列表）

双向链表

key指向头和尾指针

简单的字符串列表，按照插入顺序排序

可以添加一个元素到列表的头部或者尾部

底层是一个链表而不是数组

单值多value

如果键不存在，创建新的链表

如果键已经存在，新增内容

如果值完全移除，对应的键消失





应用场景：

可以当栈和队列还有数组（lindex）

可以将程序中的数据迁移到redis当中

这就变成了无状态服务





lpush/rpush/lrange

lpush list01 1 2 3 4 5            ->5 4 3 2 1先进后出

lrange list01 0 -1

rpush list02 1 2 3 4 5           ->1 2 3 4 5先进先出

lrange list02 0 -1



lpop/rpop

lpop list01	->	5

rpop list02	->	1



lindex按照索引下标获取元素（从上到下）

lindex list01 2	->	2

lindex list02 2	->	4



llen查看元素个数

lrem key1 key2 ... 删指定value

ltrim 开始index 结束index，截取指定范围的值后再复制给key

![image-20200612190639515](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200612190639515.png)

rpoplpush 源列表 目标列表

![image-20200612190808256](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200612190808256.png)

lset key index value将值赋值给指定下标的元素

linsert key before/after 元素 值（在元素前/后插入元素）





### Set（集合）

String类型的无序集合，底层是HashTable

元素不重复

单值多value



推荐系统：

共同好友 交集

推荐好友 差集

可能认识 并集



sadd/smembers/sismember

scard获取集合里面的元素个数

srem key value删除集合中某个元素

srandmember key 某个整数 （随机出几个数）抽奖

spop key随机出

smove key1 key2 在key1里某个值	将key1里的某个值赋值给key2

sdiff key1 key2	差集

sinter key1 key2	并集

sunion key1 key2	交集



### Zset（sorted set 有序集合）

Zset和Set一样是String类型元素的集合。

不同的是每一个元素都会关联一个double类型的分数

redis是通过分数来为集合中的成员进行从小到大的排序

Zset的元素是唯一的，但分数（score）却可以重复

之前set是k1 v1 v2 v3

现在zset是k1 score1 v1 score2 v3



场景：

排行榜

有序事件

评论+动态分页



底层在元素少或者元素小的情况下，是个ziplist

当元素数达到某个值，或者元素大了的情况下，就变成了skiplist

![image-20200614141154477](C:\Users\45928\AppData\Roaming\Typora\typora-user-images\image-20200614141154477.png)





zadd/zrange

zrangebyscore key 开始score 结束score（（不包含，limit作用是返回限制，limit 开始下标步 多少步）

zrem key 某score下对应的value值（删除元素）

zcard/zcount key score区间/zrank key values值（获得下标值/zscore key对应值，获取）

zrevrank key values值（逆序获得下标值）

zrevrange

zrevrangebyscore key 结束score 开始score



### Bitmap

这是一个二进制数据

应用场景：统计一个人在365天里面哪几天登陆了

365位，0没登陆1登陆了

setbit rowenci 2 1

setbit rowenci 364 1

bitcount rowenci 0 -1   ----- >  2

serlen rowenci 46也就是才消耗46个字节



统计哪些人登陆了

首先将用户id和自然数做一个映射

setbit 20200101 2 1

setbit 20200101 8 1

setbit 20200102 8 1

bitop or res 20200101 20200102

bitcount res 0 -1

这个就是用或运算来判断两天当中有哪些用户登陆了





12306

购票，每个人买的站不同

将每个站作为一个key，座位号作为二进制位

用二进制或运算可以判断这个区间有没有票了

只要有1，就不能买，没0可以买





权限判断

000 010 111



## RDB

Redis DataBase

https://www.cnblogs.com/ysocean/p/9114268.html

既可以手动执行，也可以根据服务器配置定期执行，该功能可以将某个时间点上的数据库状态保存在一个RDB文件当中

生成的RDB文件是一个压缩的二进制文件，通过该文件可以还原生成RDB文件时的数据库状态



两个命令可以生成RDB文件

SAVE：阻塞redis服务器进程，直到RDB文件创建完成，服务器进程阻塞期间，服务器不能处理任何命令请求。

BGSAVE：fork一个子进程来创建RDB文件，主进程继续处理命令请求（fork过程也会产生阻塞）

RDB文件的载入工作是在服务启动时自动执行的，所以redis没有专门用于载入RDB文件的命令，只要Redis服务器在启动的时候检测到有RDB文件存在，那么就会自动载入



在载入RDB文件时，数据库处于阻塞状态



①、优势

1.RDB是一个非常紧凑(compact)的文件，它保存了redis 在某个时间点上的数据集。这种文件非常适合用于进行备份和灾难恢复。

2.生成RDB文件的时候，redis主进程会fork()一个子进程来处理所有保存工作，主进程不需要进行任何磁盘IO操作。

3.RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

②、劣势

1、RDB方式数据没办法做到实时持久化/秒级持久化。因为bgsave每次运行都要执行fork操作创建子进程，属于重量级操作，如果不采用压缩算法(内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑)，频繁执行成本过高(影响性能)

2、RDB文件使用特定二进制格式保存，Redis版本演进过程中有多个格式的RDB版本，存在老版本Redis服务无法兼容新版RDB格式的问题(版本不兼容)

3、在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改(数据有丢失)



**注**：因为AOF文件的更新频率比RDB文件更高，所以如果服务器开启了AOF持久化功能，那么服务器会优先使用AOF文件来还原数据库状态，只有AOF持久化功能处于关闭状态时，服务器才会使用RDB文件来还原数据库状态

## AOF

append only file

https://www.jianshu.com/p/1e34fdc51e3b

https://www.jianshu.com/p/c55570df108b

## 事务

https://www.cnblogs.com/DeepInThought/p/10720132.html

## 消息队列

https://www.cnblogs.com/qlqwjy/p/9763754.html

## 主从复制

https://www.cnblogs.com/daofaziran/p/10978628.html

主：写

从：读



主：热

从：备

## 集群

哨兵sentinel模式

https://www.jianshu.com/p/06ab9daf921d

## 缓存穿透、击穿、雪崩

https://www.cnblogs.com/xichji/p/11286443.html

https://www.bilibili.com/video/BV1n5411s7Wb

https://www.jianshu.com/p/2104d11ee0a2

https://cloud.tencent.com/developer/article/1136056

https://segmentfault.com/a/1190000015482091

布隆过滤器一定能判断数据不存在

但是它不能判断数据一定存在

哈希一致性算法

哈希环，利于扩容，只需要迁移两个数据库当中的数据

但是容易发生数据倾斜

发现热点数据：https://www.cnblogs.com/hongdada/p/10406902.html

LFU

https://www.cnblogs.com/linxiyue/p/10955533.html