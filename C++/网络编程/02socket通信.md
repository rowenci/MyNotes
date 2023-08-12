# socket通信

Server：

  socket()   创建套接字

  bind()  绑定套接字的ip与端口

  listen()   设置最大监听数

  accept()   监听套接字等待连接

  read()  从套接字读取数据

  write() 向套接字写入数据

  close() 关闭套接字

Client：

  socket()   创建套接字

  connect()  连接服务器

  write() 向套接字写入数据

  read()  从套接字读取数据

  close() 关闭套接字



int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);

因为上述的网络通信函数诞生时间很早，因此地址使用的还是sockaddr类型，现在使用的时候，实际定义的时候还是使用sockaddr_in等，需要将sockaddr_in等类型强转为sockaddr类型。

在Server的网络通信过程当中，若只有一个客户端和一个服务端进行通信，那么服务端和客户端共会存在3个套接字。

1. 第一个套接字为socket函数在服务端创建出来的，用于监听连接请求。该套接字会用于bind函数。
2. 第二个套接字为服务端当中用于与客户端进行通信的套接字，这个套接字是通过connect函数返回值获得到的。
3. 第三个套接字为客户端当中用于与服务端进行通信的套接字，这个套接字是socket函数在客户端创建出来的。

## socket

```c++
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
```

创建一个套接字

domain : 协议AF_INET、AF_INET6、AF_UNIX（AF_LOCAL）

type : 选用的数据传输协议

    SOCK_STREAM     Provides sequenced, reliable, two-way, connection-based byte streams.  An out-
                           of-band data transmission mechanism may be supported.（流式）
    SOCK_DGRAM      Supports  datagrams  (connectionless,  unreliable  messages of a fixed maximum
                       length).（报文式）
    SOCK_SEQPACKET  Provides a sequenced, reliable,  two-way  connection-based  data  transmission
                       path  for datagrams of fixed maximum length; a consumer is required to read an
                       entire packet with each input system call.
    SOCK_RAW        Provides raw network protocol access.
    SOCK_RDM        Provides a reliable datagram layer that does not guarantee ordering.
    SOCK_PACKET     Obsolete and should not be used in new programs; see packet(7).

protocol : 代表协议是什么，一般为0，若STREAM则是tcp，若DGRAM则是udp

返回值 : 成功返回套接字对应的文件描述符，失败返回-1

## bind

```c++
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

sockfd：socket函数的返回值

addr：(struct sockaddr*)sockaddr_in

addrlen：sizeof(sockaddr_in)地址结构的大小

返回值：成功返回0，失败返回-1

## listen

设置与服务器同时建立连接的上限数（同时进行三次握手的客户端数量）

```c++
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int listen(int sockfd, int backlog);
```

sockfd：socket函数返回值

backlog：上限数值（最大128）

返回值：成功0 失败-1

## accept

阻塞等待客户端建立连接

```c++
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

sockfd：socket函数返回值

addr：客户端的sockaddr_in（传出参数）

返回值：成功返回一个与客户端成功连接的socket文件描述符，失败返回-1

