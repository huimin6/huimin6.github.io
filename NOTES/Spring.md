<!-- MarkdownTOC -->

- [Spring](#spring)
    + [IoC](#ioc)
    + [Spring中IOC的实现原理](#spring中ioc的实现原理)
    + [bean的生命周期](#bean的生命周期)
    + [Spring中用到的设计模式](#spring中用到的设计模式)
- [SpringMVC](#springmvc)

<!-- /MarkdownTOC -->

# Spring
## IoC
1.IoC 是什么
　　Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在 Java 开发中，IoC 意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好Ioc呢？理解好 IoC 的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

　　> 谁控制谁，控制什么：传统 Java SE 程序设计，我们直接在对象内部通过 new 进行创建对象，是程序主动去创建依赖对象；而 IoC 是有专门一个容器来创建这些对象，即由 IoC 容器来控制对 象的创建；谁控制谁？当然是 IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取(不只是对象包括比如文件等)。

　　> 为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。

　　用图例说明一下，传统程序设计如图2-1，都是主动去创建相关对象然后再组合起来：
## Spring中IOC的实现原理

参考博客：https://blog.csdn.net/shenghuaday/article/details/51399433

## bean的生命周期

1.调用者通过getBean()请求某一个Bean，如果容器注册了org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessor接口，那么在实例化Bean之前，将调用接口的postProcessBeforeInstantiation()方法<br>
2.根据配置情况调用Bean构造函数或工厂方法实例化Bean<br>
3.如果Bean配置了属性信息，那么在实例化后将调用Bean的属性设置方法设置属性值<br>
4.通过后处理器BeanPostPorcessor对Bean进行加工(AOP、动态代理)<br>
5.执行属性中定义的(如果定义过这个方法)init-method方法<br>
6.容器关闭，触发对Bean后续生命周期的管理，如果Bean实现了DisposableBean接口，就调用destory()方法，释放资源，销毁Bean


## Spring中用到的设计模式

(1)工厂模式：Spring中bean的创建
# SpringMVC

1.在web.xml中配置Servlet
```
<!-- springmvc的前端控制器 -->
    <servlet>
    <!--配置一个叫做taotao-portal的Servlet，自动启动，然后mapping到所有的请求
        <servlet-name>taotao-portal</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- contextConfigLocation用来指定SpringMVC配置文件的位置， 如果不配置contextConfigLocation， springmvc的配置文件默认是：WEB-INF/servlet名字+"-servlet.xml" -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>taotao-portal</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

2.创建SpringMVC的xml配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc 
        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
    <!-- 配置包扫描器 -->    
    <context:component-scan base-package="com.taotao.portal.controller"></context:component-scan>
    <!-- 配置注解驱动 -->
    <!-- 这是SpringMVC提供的一键式的配置方法，配置此标签后SpringMVC会帮我们自动做一些注册组件之类的事情 -->
    <mvc:annotation-driven/>
    <!-- 视图解析器 -->
    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
```

3.创建controller和view

在com.taotao.portal.controller包下面创建带有Controller注解的类，在    WEN-INF包下面创建对于的jsp页面作为视图