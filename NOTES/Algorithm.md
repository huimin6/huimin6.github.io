## 2 sum问题

参考博客：https://www.cnblogs.com/TenosDoIt/p/3647244.html

## rand5生成rand7

5*(rand5()-1) + rand5
```
int x = ~(1<<31);
while(x < 7) {
    x = 5*(rand5()-1) + rand5;
}
return x;
```
