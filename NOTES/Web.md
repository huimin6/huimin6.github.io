<!-- MarkdownTOC -->

- [JavaWeb](#javaweb)
    + [get和post的区别](#get和post的区别)
    + [cookie和session的区别](#cookie和session的区别)
    + [http请求和响应](#http请求和响应)
    + [http 和 https](#http-和-https)

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

Session 是在服务端保存的一种数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；

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

https 的连接过程：

https 首先与安全套接层建立连接，服务端接收到客户端的连接请求之后，将 SSL 证书发给客户端

客户端校验这个证书的信息，并从中读取公钥，并用公钥加密对称秘钥，发送个服务端

服务端接收到之后，用自己的私钥去解密，获得对称秘钥，以后所有的会话都用对称秘钥加密

细节的讲解：https://baijiahao.baidu.com/s?id=1570143475599137&wfr=spider&for=pc