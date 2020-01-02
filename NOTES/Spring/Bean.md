# Bean的创建过程

1.读取配置的 bean.xml 文件

2.根据 bean.xml 中的配置找到对应的类的配置，并实例化

3.调用实例化之后的实例

ConfigReader: 用于读取及验证配置文件

ReflectionUtil: 用于根据配置文件中的配置进行反射实例化

配置文件的读取过程

1.ResourceLoader将资源文件转换成对应的Resource文件

2.通过DocumentLoader对Resource文件进行转换，将Resource文件转换为Document文件

3.通过实现BeanDefinitionDocumentReader接口的DefaultBeanDefinitionDocumentReader类对Document进行解析，并使用BeanDefinitionParseDelegate对Element进行解析

XML配置

1.xml配置中id, name, alias的区别

https://blog.csdn.net/aitangyong/article/details/50629525
