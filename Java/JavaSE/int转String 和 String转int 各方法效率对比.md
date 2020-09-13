# Java中int转String 和 String转int 各方法效率对比

![img](https://csdnimg.cn/release/phoenix/template/new_img/original.png)

[行者小朱](https://me.csdn.net/u012050154) 2016-05-05 10:45:44 ![img](https://csdnimg.cn/release/phoenix/template/new_img/articleReadEyes.png) 80552 ![img](https://csdnimg.cn/release/phoenix/template/new_img/tobarCollectionActive.png) 已收藏 32

分类专栏： [Java](https://blog.csdn.net/u012050154/category_6116524.html)

版权

# **一、int转String有三种方式**

(1)num + ""

(2)String.valueOf(num)

(3)Integer.toString(num)



```java
//int => String



int num = 123456789;



 



//(1)num + ""



long start = System.currentTimeMillis();//得到开始运行时系统时间		



for(int i=0; i<100000; i++){



	String str = num + "";



}	



long end = System.currentTimeMillis();//得到结束运行时系统时间



System.out.println("num + \"\" : " + (end - start));



 



//(2)String.valueOf(num)



start = System.currentTimeMillis();



for(int i=0; i<100000; i++){



	String str = String.valueOf(num);



}



end = System.currentTimeMillis();



System.out.println("String.valueOf(num) : " + (end - start));



 



//(3)Integer.toString(num)



start = System.currentTimeMillis();



for(int i=0; i<100000; i++){



	String str = Integer.toString(num);



}



end = System.currentTimeMillis();



System.out.println("Integer.toString(num) : " + (end - start));
```

运行结果：



```apache
num + "" : 45
String.valueOf(num) : 8
Integer.toString(num) : 9
```



可以发现，第一种方式与后面两种方式运行的时间相差比较大。关于这一点的解释，**可以参考[博客](http://m.blog.csdn.net/article/details?id=50880110)写的比较详细**，这里只说一下区别：

**String.valueOf():**采用String.valueOf(object)的基础是Object#toString()，但这里不用担心object是否为null这一问题，JDK中String#valueOf(object)源码：

```java
public static String valueOf(Object obj){return (obj==null)?"null":obj.toString();}
```

所以使用该方法不必担心object为null的情况，但同时注意当object为null时该方法返回"null"，而非null!!!



**Integer.toString():**采用Integer.toString()的基础仍是Object#toString()，因为java.lang.Object类中已有public方法toString()，所以对任何严格意义上的Java对象都可以调用此方法，但使用时需要注意，必须保证object不是null值，否则将会抛出NullPointerException异常！！！



# **二、String转int有两种方式**

(1)Integer.parseInt(str)

(2)Integer.valueOf(str).intValue()



```java
//String => int



String s = "123456789";



 



//(1)Integer.parseInt(str)		



start = System.currentTimeMillis();



for(int i=0; i<100000; i++){



	int n = Integer.parseInt(s);



}



end = System.currentTimeMillis();



System.out.println("Integer.parseInt(str) : " + (end - start));



 



//(2)Integer.valueOf(str).intValue()



start = System.currentTimeMillis();



for(int i=0; i<100000; i++){



	int n = Integer.valueOf(s).intValue();



}



end = System.currentTimeMillis();



System.out.println("Integer.valueOf(str).intValue() : " + (end - start));
```

运行结果：



```less
Integer.parseInt(str) : 13
Integer.valueOf(str).intValue() : 10
```

可以发现，两者运行时间基本差不多。但是第二种方法，先是将String => Integer => int，即将String包装类型转化为Integer包装类型，再将包装类型转化为基本类型。

【包装类型转化为基本类型，调用包装类对象的intValue()、shortValue()、doubleValue()等方法】