<div align="center"> <img src="../pictures//maven-logo-black-on-white.png"/> </div><br>

# Maven

#### Maven是基于项目对象模型(POM project object model)，可以通过一小段描述信息（配置）来管理项目的构建，报告和文档的软件项目管理工具

Maven的核心功能便是合理描述项目间的依赖关系，通俗点讲，就是通过pom.xml文件的配置获取jar包，而不需要手动去添加jar包（省事儿啊，是不是）

Maven项目的结构时固定的，结构如下

Example

    ---pom.xml 核心配置文件，放在根目录下
    
    ---src
    
        ---main
        
            ---java 源码目录
            
            ---resources java配置文件目录
            
    ---test
    
        ---java
        
        ---resources
        

## mac电脑安装maven

参考博客：https://cloud.tencent.com/developer/article/1194577

## 依赖调解规则

如果 A -> B -> C -> X(1.0)， A -> D -> X(2.0)，A 间接的依赖 X，现在 X 中有两个版本，那么 A 最终会选择哪个版本？

* 最短路径原则：如果 A 依赖的 jar 包有两个不同的路径，那么选择路径最短的那条，也就是说上面问题的答案是，A 会选择 X(2.0)
* 第一声明优先原则：如果 A 依赖的 jar 包有两个不同的路径，但是这两条路径的长度是一样的，A -> B -> F(1.0)，A -> B -> F(2.0)，那么 A 最终会根据 F 在文件中声明的顺序，谁先声明就用谁
* 可选依赖不会传递：如果 A -> B，B -> C，B -> D，A 直接依赖于 B，B 对于 C 和 D 是可选依赖，那么 A 中不会直接引入 C 和 D。可选依赖通过 optional 配置，true 表示可选。如果 A 需要依赖 C 或者 D，就需要显示的声明对 C 或者 D 的依赖
