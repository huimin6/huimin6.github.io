<!-- MarkdownTOC -->

- [JavaWeb](#javaweb)
    + [get和post的区别](#get和post的区别)
    + [cookie和session的区别](#cookie和session的区别)
    + [http请求和响应](#http请求和响应)
    + [http和https](#http和https)

<!-- /MarkdownTOC -->

# JavaWeb
## get和post的区别
(1)get一般用来查询和获取服务端的资源，post用来修改服务端的资源

(2)get用来获取资源，是安全的和幂等的（幂等的意思就是多个URL请求返回的结果是一样的）

(3)get请求在url中传递的参数是有长度限制的，而post没有

(4)使用GET方法时，查询字符串（键值对）被附加在URL地址后面一起发送到服务器
(/test/demo_form.jsp?name1=value1&name2=value2)，GET请求能够被缓存，会保存在浏览器的浏览记录中，post请求不会被缓存

## cookie和session的区别
Session是在服务端保存的一种数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；

Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

## http请求和响应

参考博客：https://blog.csdn.net/xun527/article/details/78387345

## http和https

参考博客：https://www.cnblogs.com/wqhwe/p/5407468.html
