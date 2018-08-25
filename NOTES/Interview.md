面试问题
1.能不能自己写一个和 java.lang.String 相同的 String 类？

不能自己写以"java."开头的类，其要么不能加载进内存，要么即使你用自定义的类加载器去强行加载，也会收到一个SecurityException。
自己创建一个 java.lang 的 package 然后新建一个 String 类，是可以编译的，但是不能运行，会抛出异常：java.lang.SecurityException: Prohibited package name: java.lang

参考博客：https://blog.csdn.net/tang9140/article/details/42738433
