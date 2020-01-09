1.数据表中的数据字段varChar类型比较, 不能按照正常的数据比较逻辑处理, 因为 "99.5" > "521.5" == True

2.正则表达式的设计, 要考虑超长序列匹配和 Java 中正则匹配的"回溯"问题, 可能会导致匹配的过程中次数太多, 导致CPU负载达到100%

3.数据表结构的变更, 比如添加列、添加索引, 拆分成单个SQL去执行多个任务, 否则可能会引发一些意料之外的问题

4.HashMap 的遍历要通过 entrySet 去遍历, 效率会比直接通过 ketSet 去循环获取 value 高

5.时间类型的操作要通过 Calendar 和 DateTime 去执行, 可以避免 Date(Long date) 这种转换导致数据类型溢出的问题

6.对于 String 类型的判空, 要通过Apache commonsLang3中的StringUtils.isBlank()或者StringUtils.isEmpty(), 
  isBlank()会对空白字符进行判断, 比isEmpty()更准确
  
7.使用CollectionUtils.isEmpty对集合的判空  

8.如何避免在业务代码中插入太多的if else 逻辑:https://softwareengineering.stackexchange.com/questions/343534/what-is-the-better-way-to-escape-from-too-many-if-else-if-from-the-following-cod
