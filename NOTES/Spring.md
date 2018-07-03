# Spring

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