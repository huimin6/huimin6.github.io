<!-- MarkdownTOC -->

- [JavaWeb](#javaweb)
    + [get和post的区别](#get和post的区别)
    + [cookie和session的区别](#cookie和session的区别)
    + [http请求和响应](#http请求和响应)
    + [http 和 https](#http-和-https)
    + [HTTP 的长连接和短连接](#http-的长连接和短连接)
    + [RPC 框架](#rpc-框架)

<!-- /MarkdownTOC -->

# JavaWeb

## get和post的区别

(1)get一般用来查询和获取服务端的资源，post用来修改服务端的资源

(2)get用来获取资源，是安全的和幂等的（幂等的意思就是多个URL请求返回的结果是一样的）

(3)get请求在url中传递的参数是有长度限制的，而post没有

(4)使用GET方法时，查询字符串（键值对）被附加在URL地址后面一起发送到服务器
(/test/demo_form.jsp?name1=value1&name2=value2)，GET请求能够被缓存，会保存在浏览器的浏览记录中，post请求不会被缓存

(5)post请求是将请求内容放入 http 包体中

(6)可以用 get 去实现 post 请求，post 必须到 FORM 表单，但是 get 不需要，所以就有人为了省事儿

实例：

GET与POST方法实例：

get
```
GET /books/?sex=man&name=Professional HTTP/1.1
Host: www.wrox.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
Gecko/20050225 Firefox/1.0.1
Connection: Keep-Alive
```

post
```
POST / HTTP/1.1
Host: www.wrox.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
Gecko/20050225 Firefox/1.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 40
Connection: Keep-Alive
(----此处空一行----)
name=Professional%20Ajax&publisher=Wiley
```

## cookie和session的区别

Session 是在服务端保存的一种数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中，默认保存 30 分钟

Cookie 是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现 Session 的一种方式。cookie 中保存的是 sessionId。

1.禁用 cookie 后 session 的实现方式

(1)URL重写，通过在发送的请求 URL 后面添加 sessionId 信息，发送到服务端

http://...../xxx;jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764 

(2)表单隐藏字段，就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把 sessionId 传递回服务器

2.session 的创建和销毁

在程序中第一次调用 request.getSession() 方法时就会创建一个新的 Session

session 对象默认 30 分钟没有使用，则服务器会自动销毁 session，在 web.xml 文件中可以手工配置 session 的失效时间

参考的博客：http://justsee.iteye.com/blog/1570652

参考的博客：https://www.cnblogs.com/xdp-gacl/p/3855702.html

参考博客：https://blog.csdn.net/qq_33936481/article/details/51164081

## http请求和响应

参考博客：https://blog.csdn.net/xun527/article/details/78387345

## http 和 https

参考博客：https://www.cnblogs.com/wqhwe/p/5407468.html

http 特点：

* 无连接：每次连接只处理一次请求，不是不建立连接，还是需要建立 TCP 连接的

* 无状态：对处理过的事务没有记忆能力 

* 媒体独立：可以传输任何类型的数据，前提是客户端和服务端知道如何处理数据

https 的连接过程：

https 首先与安全套接层建立连接，服务端接收到客户端的连接请求之后，将 SSL 证书发给客户端

客户端校验这个证书的信息，并从中读取公钥，并用公钥加密对称秘钥，发送个服务端

服务端接收到之后，用自己的私钥去解密，获得对称秘钥，以后所有的会话都用对称秘钥加密

细节的讲解：https://baijiahao.baidu.com/s?id=1570143475599137&wfr=spider&for=pc

## HTTP 的长连接和短连接

实际是 TCP 的长连接和短连接

HTTP 1.0 默认短连接，就是一次读写完毕直接释放连接

HTTP 1.1 默认长连接，就是保持客户端和服务端的连接一段时间，一般是 2 个小时，由服务端提供 TCP 的保活功能。当客户端再次访问服务端时，直接使用当前这条连接，不需要重新建立连接

1.长短连接适用场景

长连接多用于操作频繁，点对点的通讯，WEB 网站的 HTTP 服务一般都用短链接，便于管理

2.长短连接的优缺点

长连接：省去了 TCP 建立和释放连接的操作，减少浪费，节约时间，但是如果连接太多，服务端可能会压力太大

短连接：对于服务端来说，管理比较简单，存在的连接都是有用的连接，但是如果客户端请求频繁，会在 TCP 连接的建立和释放操作上浪费时间和带宽


## RPC 框架

RPC，Remote Procedure Call Protocol 远程过程调用协议

RPC 的应用场景

如果应用部署在两台不同的机器 A 和 B 上，如果 A 想调用 B 上的某一个方法，比如 Employee getEmployeeByName(String name){}，由于不太同一个内存空间，所以没法直接调用，需要通过网络来表达调用和传送数据。

在这个过程中需要解决一下问题：

1.通讯问题，客户端和服务端之间通过 TCP 建立连接，所有数据都通过这个连接传送。

2.寻址问题，就是 A 机器上的应用怎么告诉底层的 RPC 框架，如何连接到 B 服务器以及特定的端口。解决方案是基于 Web 服务协议栈的 RPC，就要提供一个 endpoint URI，或者是从 UDDI 服务上查找。

3.当 A 服务器上的应用发起远程过程调用时，方法的参数需要通过底层的网络协议如 TCP 传递到 B 服务器，由于网络协议是基于二进制的，内存中的参数的值要序列化成二进制的形式，也就是序列化 (Serialize) 或编组 (marshal)，通过寻址和传输将序列化的二进制发送给 B 服务器。

4.B 服务器收到请求后，需要对参数进行反序列化 (序列化的逆操作)，恢复为内存中的表达方式，然后找到对应的方法 (寻址的一部分)进行本地调用，然后得到返回值。

5.返回值还要发送回服务器 A 上的应用，也要经过序列化的方式发送，服务器 A 接到后，再反序列化，恢复为内存中的表达方式，交给 A 服务器上的应用。

## 分布式、高并发、多线程

分布式

分布式更多是一个概念，**是为了解决单个物理服务器容量和性能瓶颈问题而采用的优化手段**。该领域需要解决的问题较多，在不同的技术层面上，包括分布式缓存、分布式数据库、分布式文件系统 (FastDFS)。常见的这些名词如Hadoop、zookeeper、MQ等都和分布式有关。

分布式的实现有两种形式：

水平扩展：当一台机器的性能不能满足当前的流量的时候，就添加新的机器，将流量平分到所有服务器上，所有机器都可以提供相同的服务

垂直拆分：前端有多种查询需求时，一台机器不能满足需求，可以将不同的查询需求分发到不同的机器上，比如 A 机器可以负责查询余票的请求，B 机器则负责处理支付的请求

高并发

高并发主要是处理瞬时流量激增的情况
