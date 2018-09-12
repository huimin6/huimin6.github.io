面试问题

1.能不能自己写一个和 java.lang.String 相同的 String 类？

不能自己写以 "java." 开头的类，其要么不能加载进内存，要么即使你用自定义的类加载器去强行加载，也会收到一个 SecurityException。
自己创建一个 java.lang 的 package 然后新建一个 String 类，是可以编译的，但是不能运行，会抛出异常：java.lang.SecurityException: Prohibited package name: java.lang

参考博客：https://blog.csdn.net/tang9140/article/details/42738433

2.创建一个类放入 Java 集合中

重写 equals 必须重写 hashCode() 方法

假设创建一个 Student 类，那么必须重写 equals() 和 hashCode() 方法，保证
equals 返回 true 时，hashCode 也相同，hashCode 不同时，equals 也返回 false

Integer 类中的 equals()
```
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}
```

Integer 类中的 hashCode()方法
```
public int hashCode() {
    return Integer.hashCode(value);
}
```

3.finally 和 return

下面程序的代码输出结果是 2 不是 3
```
class Test {
    public int incr() {
        int x = 1;

        try {
            return ++x;
        } catch (Exception e) {

        } finally {
            ++x;
        }
        return x;
    }

    public static void main(String[] args) {
        Test t = new Test();
        int y = t.incr();
        System.out.println(y);
    }
}
```

finally 块中的代码一定会被执行，即使 try 块中有 return, break, continue 这些关键字，但是在执行 finally 之前，return 中的 x 值会被保存到一个局部变量中，最后执行完 finally 后会继续执行之前的 return，所以最后返回的结果是 2，不是 3

下面代码的返回值就是 3 不是 2
```
class Test {
    public int incr() {
        int x = 1;

        try {
            return ++x;
        } catch (Exception e) {

        } finally {
            return ++x;
        }
    }

    public static void main(String[] args) {
        Test t = new Test();
        int y = t.incr();
        System.out.println(y);
    }
}
```
如果 try 和 finally 中都有 return，会忽略 try 中的 return，执行 finally 中的 return

## Spring 中两个 bean 需要按顺序加载，那么怎么做？

使用 depends-on 属性

<bean id="sysinit" class="SystemInit">  

<bean id="manager" class="CacheManager"  depends-on="sysinit"/>  

CacheManager 对象的创建，依赖于 SystemInit 对象，所以 SystemInit 会先加载

depends-on 适用于表面上看起来两个 bean 之间没有使用属性之类的强连接的 bean，但是两个 bean 又确实存在前后依赖关系的情况，使用了 depends-on 的时候，
CacheManager 会先于 SystemInit 销毁