# 伪分布式部署minio

有了docker-compose之后，部署这类应用非常非常简单

docker-compose up 搞定

一般最新的docker-compose.yaml文件在minio的github仓库里面

找/doc/orchestration/docker-compose/

docker-compose.yaml文件内容如下：

```docker-compose.yaml
version: '3.7'

# starts 4 docker containers running minio server instances. Each
# minio server's web interface will be accessible on the host at port
# 9001 through 9004.
services:
  minio1:
    image: minio/minio:RELEASE.2020-01-03T19-12-21Z
    volumes:
      - /media/data1:/data:z
    ports:
      - "9001:9000"
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...4}/data

  minio2:
    image: minio/minio:RELEASE.2020-01-03T19-12-21Z
    volumes:
      - /media/data2:/data:z
    ports:
      - "9002:9000"
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...4}/data

  minio3:
    image: minio/minio:RELEASE.2020-01-03T19-12-21Z
    volumes:
      - /media/data3:/data:z
    ports:
      - "9003:9000"
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...4}/data

  minio4:
    image: minio/minio:RELEASE.2020-01-03T19-12-21Z
    volumes:
      - /media/data4:/data:z
    ports:
      - "9004:9000"
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...4}/data
```




当集群启动之后，看一下控制台的输出，找一下ip地址然后放到浏览器里面访问，我也不知道为什么这个ip这么奇怪，一个个试吧。

SDK直接看https://docs.min.io/cn/java-client-quickstart-guide.html里面的

**这个minio的服务端不用管，它其实是一个用命令行来控制minio集群的东西，和浏览器访问是一样的。**