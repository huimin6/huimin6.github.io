
SQL 语句的执行过程：

编写 SQL-->预编译-->设置参数-->执行 SQL-->封装结果

Hibernate 框架：全自动全映射框架，旨在消除 SQL，缺点就是不灵活
Mybatis 框架：是一个半自动的轻量级框架，将编写 SQL 放在配置文件里面，方便 SQL 优化

Mybatis 中的标签和配置信息


MyBatis 中的缓存

默认都是放在 Map (HashMap) 中的

1.一级缓存：sqlSession 缓存，默认开启，无法关闭

一级缓存失效的情况：

(1)sqlSession 不同

(2)sqlSession 相同，查询条件不同 (当前缓存中还没有这个数据)，即使已经有这个数据，只有查询条件不同就会导致缓存失效

(3)sqlSession 相同，查询期间执行了增删改的操作

(4)sqlSession 相同，手动清空了一级缓存

2.二级缓存：基于 namespace 的缓存，一个 namespace 对应一个二级缓存，也是默认全局开启，要使用二级缓存，必须在对应的 mapper xml 文件中添加 cache 标签

工作机制：查出的数据默认放入一级缓存，只有会话关闭，一级缓存中的内容才会保存到二级缓存中

缓存失效的情况：

(1)查出期间执行了增删改的操作

3.缓存的设置

(1)select 标签中的 useCache = "false"，关掉的是二级缓存，对一级缓存没有影响

(2)每个增删改标签中的默认的 flushCache = "true"，也就是每次增删改都会清空一级缓存，同时会清空二级缓存

(3)每个查询标签的 flushCache = "false"，每次出现不会同时清空一二级缓存

(4)cacheEnabled 属性只能控制二级缓存的开启或者关闭

(5)sqlSession.clearCache() 只是清空当前会话的一级缓存，对二级缓存没影响

(6)localCacheScope 属性用来设置缓存作用域，默认 localCacheScope = "session"，也就是当前会话有效；

如果 localCacheScope = "statement"，那么就相当于可以禁用一级缓存

4.会话的缓存查询顺序

1.新的 session 先查询二级缓存

2.二级缓存没有，查一级缓存

3.一级缓存也没有，查数据库


