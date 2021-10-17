# Java中对于+和append拼接字符串效率的误解

### 引言

　　对于初学Java的人来说，在学习String的时候，肯定有无数个人和我们讲过，”尽量不要使用+拼接字符串，效率不好，应该使用append，你自己循环拼接个十万次自己瞧瞧就知道了“，然后像下面那样给我们演示了一下，用+和用StringBuilder的append拼接个十万次，输出一下各自消耗的时间，差距非常大，让我们立刻深信不疑，+拼接就是个垃圾，除了平时方便测试代码，否则都不要去用了，并且当有初学者使用+拼接时，也会毫不犹豫地去高谈阔论地教育一番。

### 分析

```java
public class Test {
    public static void main(String[] args){
        String str = "";
        long start = System.currentTimeMillis();
        for(int i=0; i<100000; i++)
            str += "a";
        long end = System.currentTimeMillis();
        System.out.println(end - start);

        StringBuilder sb = new StringBuilder();
        start = System.currentTimeMillis();
        for(int i=0; i<100000; i++)
            sb.append("a");
        str = sb.toString();
        end = System.currentTimeMillis();   
        System.out.println(end - start);
    }
}
/*
console:
D:\>javac Test.java

D:\>java Test
4655
0
*/
```

可以看到确实是差距巨大，并且我们也看过不少下面反编译的字节码指令：

![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20171122165753319)

上面的方框是+循环拼接，下面的方框是append循环拼接。
可以看到循环中+拼接会创建一个StringBuilder，除此之外和直接使用StringBuilder没有两样，但问题是每次循环都会new一个StringBuilder，也就是说效率就差在这里了。
（在刚学Java7的时候，我从别人那里得到一个很搞笑的解释，”+拼接底层实际上是使用了StringBuilder，所以效率很低，因此我们应该在任何时候都使用StringBuffer来拼接字符串“，噗，真不知道这种混乱的逻辑是怎么产生的，StringBuffer是线程安全的怎么可能比StringBuilder效率高，233333）

### 规则

对于此处的str = str + “a”，编译器会处理为new StringBuilder().append(str).append(“a”)，不管一次性+几个字符串，只要+拼接全部在一条语句中，就只会new一次，循环中+拼接被断成了十万条语句，那自然就会new十万次，证据如下：

```java
public class Test {
    public static void main(String[] args){
        String a = "a";
        String b = "a";
        String c = "a";
        String d = "a";
        String e = a + b + c;
        e += d;
    }
}
```

反编译后：
![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20171122195304877)
可以看到，对于有String类型变量参与的情况（String变量+”字面量” 或者 String变量+String变量），只要+拼接没有断，那就会一直调用append，一旦另起一条语句，马上就会new一个StringBuilder.
所以最开始那样拼接十万次的测试根本没有意义，根本不公平，真要测试也应该像下面这样：

```java
public class Test {
    public static void main(String[] args){
        String str = "";
        long start = System.currentTimeMillis();
        for(int i=0; i<100000; i++)
            str += "a";
        long end = System.currentTimeMillis();
        System.out.println(end - start);

        StringBuilder sb = new StringBuilder();
        start = System.currentTimeMillis();
        for(int i=0; i<100000; i++)
            sb = new StringBuilder().append(sb).append("a");//为了保证公平，这里也要new
        str = sb.toString();
        end = System.currentTimeMillis();   
        System.out.println(end - start);
    }
}
/*
D:\>javac Test.java

D:\>java Test
4656
3116
*/
```

这样看来，差距也没什么，实际上如果数量再大些，+效率比直接StringBuilder可能更好。

或者这样测试（此时已经不是String类型变量而是字面量了）：

```java
public class Test {
    public static void main(String[] args){
        long start = System.currentTimeMillis();
        String str = "a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a"+"a";
        long end = System.currentTimeMillis();  
        System.out.println(end - start);

        StringBuilder sb = new StringBuilder();
        start = System.currentTimeMillis();
        //append是可以用循环的，这里故意这么搞     

        sb.append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a").append("a");
        str = sb.toString();
        end = System.currentTimeMillis();   
        System.out.println(end - start);
    }
}
/*
D:\>javac Test.java

D:\>java Test
0
0
*/
```

数量太少，不够用。
![这里写图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20171122204153118)
（把StringBuilder的测试给省了，太长了，截不下）
如果查看字节码指令的话，会发现这200个”a”直接就被合成了一个完整的字符串（jdk1.8下的结果，我不知道jdk1.7是不是这样），没有使用StringBuilder，这可能和字符串的常量池有关，但是从结果来看，字符串字面量的拼接是不会使用new的。

公司的项目不知出于什么原因不让用ORM框架，非要手动拼接SQL语句，对于是否使用+拼接大量SQL还和同事争论了一番，对于那些静态固定的SQL，用+来换行增强可读性完全没有问题；但是如果要动态改变SQL条件的话，还是应该使用StringBuilder的append，因为动态SQL肯定会根据不同的条件拼接不同的String变量，那么SQL语句中途肯定会断开，用+的话每断开一次都会new一次。

```java
String sql1 = "SELECT COL1, COL2, COL3, COL4 "
         + "FROM TABLE1 LEFT JOIN TABLE2 "
         + "ON TABLE1.ID = TABLE2.ID WHERE "
         + "COL5 = :col5 AND COL6 = :col6";

StringBuilder sb = new StringBuilder();
sb.append("SELECT COL1, COL2, COL3, COL4 FROM TABLE1 ")
  .append("WHERE 1");
if(var1 != null)
    sb.append(" AND COL5 = ").append(var1);
if(var2 != null)
    sb.append(" AND COL6 = ").append(var2);
String sql2 = sb.toString();
```

其实编译器还可能对+进行一些优化，比如`String a = b + c + d` 可能被处理成`a = new StringBuilder(b).append(c).append(d)`

## 总结

　　Java中+拼接字符串并不是人们口中所说的那样臭名昭著，人们口中所说的效率低下指的是在循环中的+拼接，只是不知道从哪天开始完全变了味。如果你能保证在一条语句中把字符串或者字符串变量全部拼接完而不断开，那+拼接根本没有任何缺点，编译器还会有一些优化，如果拼接的全是字符串字面量，那效果更好，它们直接就变成了一个完整的字符串。并且+书写简洁方便，可读性强。直接用StringBuilder的append也没有问题，只是不要append()和+混合使用。