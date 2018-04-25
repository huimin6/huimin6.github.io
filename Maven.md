# Maven

### Maven是基于项目对象模型(POM project object model)，可以通过一小段描述信息（配置）来管理项目的构建，报告和文档的软件项目管理工具

Maven的核心功能便是合理描述项目间的依赖关系，通俗点讲，就是通过pom.xml文件的配置获取jar包，而不需要手动去添加jar包（省事儿啊，是不是）

Maven项目的结构时固定的

Example

    ---pom.xml 核心配置文件，放在根目录下
    
    ---src
    
        ---main
        
            ---java 源码目录
            
            ---resources java配置文件目录
            
    ---test
    
        ---java
        
        ---resources
        
