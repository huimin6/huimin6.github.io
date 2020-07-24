# 领域驱动设计

关于领域建模的介绍：http://insights.thoughtworks.cn/ddd-domain-model/

## 分层架构

领域驱动设计通过分层的方式组织系统结构, 分为四层:
1.用户界面层

2.应用层(Service)

3.领域层(业务模型和业务规则)

4.基础设施层(数据库相关的操作)

分层的好处是便于设计出高内聚的程序, 层与层之间通过接口进行交互

# 基础元素

在领域驱动设计中通过以下内容来表示模型: Service, Entity, ViewObject
