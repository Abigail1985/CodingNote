Calendar 类常用方法的记录：

获取时间

```java
    // 使用默认时区和语言环境获得一个日历
    Calendar cal = Calendar.getInstance();
    // 赋值时年月日时分秒常用的6个值，注意月份下标从0开始，所以取月份要+1
    System.out.println("年:" + cal.get(Calendar.YEAR));
    System.out.println("月:" + (cal.get(Calendar.MONTH) + 1));       
    System.out.println("日:" + cal.get(Calendar.DAY_OF_MONTH));
    System.out.println("时:" + cal.get(Calendar.HOUR_OF_DAY));
    System.out.println("分:" + cal.get(Calendar.MINUTE));
    System.out.println("秒:" + cal.get(Calendar.SECOND));

```

今天是 2018 年 2 月 12 日，运行结果：

```
年:2018
月:2
日:12
时:15
分:57
秒:39
```


————————————————
版权声明：本文为CSDN博主「yx0628」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yx0628/article/details/79317440