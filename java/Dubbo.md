# Dubbo

Dubbo是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和Spring框架无缝集成。
Dubbo是一款高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

## RPC

什么是RPC？

Remote Procedure Call：远程过程调用。

允许一台计算机程序远程调用另外一台计算机的子程序，不用关心底层网络通信。

![img](https://upload-images.jianshu.io/upload_images/9613667-28776e12a4275c92.png?imageMogr2/auto-orient/strip|imageView2/2/w/500/format/webp)

如果一个服务需要调用另一个服务，而这两个服务不在同一个服务器环境下，那么就可以使用RPC来进行服务的远程调用。

两个服务首先会建立一个TCP连接，如果需要进行服务调用，那么client就会发出请求，将调用转化为信息，一般会通过一个服务注册与发现中心，找到服务提供者的地址，并将调用信息发送过去，服务提供者就会将信息解析为本地方法调用，然后开始调用本地方法，最后将方法调用的结果转化为信息发送到服务消费者，那么服务消费者就能得到远程方法的结果了。

## Dubbo简介

关于Dubbo的一些简介，可以查看实习时的Dubbo文档

