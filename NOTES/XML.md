## dom4j: 生成的XML文件根节点 xmlns="" 的问题

使用dom4j写入XML文件时，写入完毕后发现root element中没有 xmlns，也即是没有命名空间。

正确的写法如下：

```
Document document = DocumentHelper.createDocument();
Element kmlElement = document.addElement(KML_ELEMENT_KML, "http://www.opengis.net/kml/2.2");
```

一定要在添加根节点时，即为其指定命名空间。其中， KML_ELEMENT_KML 是root element的名称。

注意：不要通过类似 element.addNamspace（"",url） 这样的方式或类似下面的写法
```
Element kmlElement = DocumentHelper.createElement(KML_ELEMENT_KML);
kmlElement.addAttribute(KML_ATTRIBUTE_XMLNS, "http://www.opengis.net/kml/2.2");
Document document = DocumentHelper.createDocument(kmlElement);
```
来为根element指定缺省的命名空间，这样的做法就会出现生成的xml 根节点命名空间是空的问题。

不过下面的写法仍然是对的(貌似只有xmlns需要注意这一点)：

```
Element root = document.addElement("beans","http://www.springframework.org/schema/beans");
root.addAttribute("xmlns:xsi", "http://www.w3.org/2001/XMLSchema-instance");
root.addAttribute("xmlns:p", "http://www.springframework.org/schema/p");
root.addAttribute("xsi:schemaLocation", "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd");
```
生成xml文件（发现xmlns可以出现了）
```
<beans
xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:p="http://www.springframework.org/schema/p"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
```
转载：https://www.cnblogs.com/yongdaimi/p/10318526.html
