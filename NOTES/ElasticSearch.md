# ElasticSearch
1.es中使用script中需要注意上下文的引用

如果是update/insert/delete等操作, ctx._source.xxxField 来获取对应的值
如果是search操作, 可以通过doc\['xxxField'\]来获取对应的值, 但是也只能用来访问普通数据,不能访问nested对象和没有建立索引的字段, 如果要访问这些数据, 需要要通过params._source.xxxField

es中的params和doc的区别
https://blog.csdn.net/bookssea/article/details/135327968 

es官方文档中关于上下文的检索方式
https://www.elastic.co/guide/en/elasticsearch/painless/7.15/painless-ingest-processor-context.html

es中的java客户端聚合怎么用
https://www.jianshu.com/p/adc482fe1b0b
https://www.cnblogs.com/morehair/p/15824321.html

es的官方文档怎么查
https://blog.csdn.net/laoyang360/article/details/121738408

es聚合控制台操作
https://www.knowledgedict.com/tutorial/elasticsearch-aggregations.html#Sum%20Aggregation
