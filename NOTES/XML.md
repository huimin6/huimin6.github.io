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

目标xml文件

```
<?xml version="1.0" ?> 
<Cancelacion xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             RfcEmisor="VSI850514HX4" 
             Fecha="2011-11-23T17:25:06" 
             xmlns="http://cancelacfd.sat.gob.mx">
    <Folios>
        <UUID>BD6CA3B1-E565-4985-88A9-694A6DD48448</UUID> 
    </Folios>
</Cancelacion>
```

```
//Crear un document XML vacío
DocumentBuilderFactory dbfac = DocumentBuilderFactory.newInstance();
dbfac.setNamespaceAware(true);
DocumentBuilder docBuilder;
docBuilder = dbfac.newDocumentBuilder();
DOMImplementation domImpl = docBuilder.getDOMImplementation();
Document doc = domImpl.createDocument("http://cancelacfd.sat.gob.mx", "Cancelacion", null);
doc.setXmlVersion("1.0");
doc.setXmlStandalone(true);

Element cancelacion = doc.getDocumentElement();
cancelacion.setAttributeNS("http://www.w3.org/2000/xmlns/","xmlns:xsd","http://www.w3.org/2001/XMLSchema");
cancelacion.setAttributeNS("http://www.w3.org/2000/xmlns/","xmlns:xsi","http://www.w3.org/2001/XMLSchema-instance");
cancelacion.setAttribute("RfcEmisor", rfc);
cancelacion.setAttribute("Fecha", fecha);

Element folios = doc.createElement("Folios");
cancelacion.appendChild(folios);
for (int i=0; i<uuid.length; i++) {
    Element u = doc.createElement("UUID");
    u.setTextContent(uuid[i]);
    folios.appendChild(u);
}
```


注意：不要通过类似 element.addNamspace（"",url） 这样的方式或类似下面的写法
