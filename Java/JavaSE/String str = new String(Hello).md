# Java里String str = new String("Hello");会创建几个对象?



谈到这个问题首先得知道String的两种赋值方式：

  第一种是通过“字面量”赋值。 例如String str="hello";

  第二种是通过new关键字创建新对象。例如String str=new String("hello");

这两种方式到底有什么不同？程序执行的时候内存到底有几个实例？实例存在哪？字面量又存在了哪里？变量又存在哪？概念很容易搞混。下面我们一个个的讲。

虚拟机内存主要分为三块:

1. 堆：存放对象实例和数组。
2. 栈：存放基本类型，以及对象的引用。
3. 方法区：“类”被加载后的信息，常量、静态变量存放在这儿。

下面开始说String，比如下面这个main方法里声明一个字符串str

```java
package com.song.test;

public class StringTest {
	public static void main(String[] args) {
		String str = new String("hello");
	}
}
```

我们可以把

```java
String str = new String("hello");
```

这行代码分成String str、=、"hello"和new String（）四部分来看。String str只是名为str的String类型的变量，因此并没有创建对象；=是对变量str进行初始化，将某个对象的引用赋值给它，显然也没有创建对象；那么，new String("hello")为什么又能被看成"hello"和new String()呢？



我们来看一下被我们调用了的String的构造器： 

public String(String original) { //other code ... }  大家都知道，我们常用的创建一个类的实例（对象）的方法有以下两种:

一、使用new创建对象。 

二、调用Class类的newInstance方法，利用反射机制创建对象。

我们正是使用new调用了String类的上面那个构造器方法创建了一个对象，并将它的引用赋值给了str变量。同时我们注意到，被调用的构造器方法接受的参数也是一个String对象，这个对象正是"hello"。由此我们又要引入另外一种创建String对象的方式的讨论——引号内包含文本。

这种方式是String特有的，并且它与new的方式存在很大区别。 

String str="hello"; 

毫无疑问，这行代码创建了一个String对象“hello”。



String a="hello"; String b="hello"; 那这里呢？

答案还是一个。

String a="he"+"llo";  再看看这里呢？

答案是三个“he”、“llo”、“hello”



在JAVA虚拟机（JVM）中存在着一个字符串池，其中保存着很多String对象，并且可以被共享使用，因此它提高了效率。由于String类是final的，它的值一经创建就不可改变，因此我们不用担心String对象共享而带来程序的混乱。字符串池由String类维护，我们可以调用intern()方法来访问字符串池。 

我们再回头看看String a="hello";，这行代码被执行的时候，JAVA虚拟机首先在字符串池中查找是否已经存在了值为"hello"的这么一个对象，它的判断依据是String类equals(Object obj)方法的返回值。如果有，则不再创建新的对象，直接返回已存在对象的引用；如果没有，则先创建这个对象，然后把它加入到字符串池中，再将它的引用返回。因此，我们不难理解前面三个例子中头两个例子为什么是这个答案了。

只有使用引号包含文本的方式创建的String对象之间使用“+”连接产生的新对象才会被加入字符串池中。对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中，对此我们不再赘述。因此我们提倡大家用引号包含文本的方式来创建String对象以提高效率，实际上这也是我们在编程中常采用的。

因为new关键字会在堆申请一块新内存来创建对象。虽然字面还是“hello”，但是完全是不同的对象，有不同的内存地址。

字符串常量池在内存中什么位置呢？

```java
public class Test {  
    public static void main(String[] args) {  
        String str = "abc";  
        char[] array = {'a', 'b', 'c'};  
        String str2 = new String(array);  
        //使用intern()将str2字符串内容放入常量池  
        str2 = str2.intern();  
        //这个比较用来说明字符串字面常量和我们使用intern处理后的字符串是在同一个地方  
        System.out.println(str == str2);  

        //那好，下面我们就拼命的intern吧  
        ArrayList<String> list = new ArrayList<String>();  
        for (int i = 0; i < 10000000; i++) {  
            String temp = String.valueOf(i).intern();  
            list.add(temp);  
        }  
    }  
}
```

执行一下，会怎么样？

```
true 
Exception in thread "main" java.lang.OutOfMemoryError: PermGen space 
        at java.lang.String.intern(Native Method) 
        at Test.main(Test.java:16) 
Java Result: 1
```

 异常信息告诉我们PermGen 满了。奥，我知道字符串常量池在哪了。PermGen就是jvm规范中所谓的方法区。

   这里偷懒了一下，只是指定了很大的数10000000让PermGen 溢出，不过时间可能长点。勤快的人还是自己指定java运行的内存比较好，稍小点就能验证。



# 底层实现

```
String str = "hello";
String str2 = new String("hello");
12
```

上面两中创建 string对象的方式底层实现的不同之处：
（1）首先，先确定String常量池的定义：
常量池(constant pool)指的是在编译期被确定，并被保存在已编译的.class文件中的一些数据。它包括了关于类、方法、接口等中的常量，也包括字符串常量。
Java为了提高性能，静态字符串（字面量/常量/常量连接的结果）在常量池中创建，并尽量使用同一个对象，重用静态字符串。
对于重复出现的字符串字面量，JVM会首先在常量池中查找，如果常量池中存在即返回该对象。
（2）str指向在常量池中的字符串"hello"，在编译期就已经确定；str2指向堆上的对象，是在运行期创建，而堆中实际存放的字符串还是常量池中"hello"，即value属性还是指向常量池中的字符串；

由下面的运行图即可看出，str和str2中value属性的实际地址是相同的，即最终指向的字符串的位置是相同的。
![在这里插入图片描述](https://gcore.jsdelivr.net/gh/2234416233/myImage/img/20190318155015924.png)
内存模型图入下图所示：
![在这里插入图片描述](https://gcore.jsdelivr.net/gh/2234416233/myImage/img/20190318160908185.png)
结论：String str2 = new String(“hello”);首先会在堆内存中申请一块内存存储对象内容,同时还会检查字符串常量池中是否含有"hello"字符串,若没有则添加"hello"到字符串常量池中，同时把常量池中的地址放到堆内存中；若有，则直接去常量池中的字符串内存地址即可；然后在栈中创建一个引用str2指向其堆内存块对象。（此过程中可能会创建两个对象，也可能就一个）

**关于String对象的一些常见问题**
**1. 下面这段代码的输出结果是什么？**

String a = “hello2”; 　　String b = “hello” + 2; 　　System.out.println((a == b));

输出结果为：true。原因很简单，“hello”+2在编译期间就已经被优化成"hello2"，因此在运行期间，变量a和变量b指向的是同一个对象。

**2.下面这段代码的输出结果是什么？**

String a = “hello2”; 　 String b = “hello”; String c = b + 2; System.out.println((a == c));

输出结果为:false。由于有符号引用的存在，所以 String c = b + 2;不会在编译期间被优化，不会把b+2当做字面常量来处理的，因此这种方式生成的对象事实上是保存在堆上的。因此a和c指向的并不是同一个对象。
　　
**3.下面这段代码的输出结果是什么？**

String a = “hello2”; 　 final String b = “hello”; String c = b + 2; System.out.println((a == c));

输出结果为：true。对于被final修饰的变量，会在class文件常量池中保存一个副本，也就是说不会通过连接而进行访问，对final变量的访问在编译期间都会直接被替代为真实的值。那么String c = b + 2;在编译期间就会被优化成：String c = “hello” + 2;