# java-----instanceof与getClass的区别

![img](https://csdnimg.cn/release/phoenix/template/new_img/original.png)

[她说巷尾的樱花开了](https://me.csdn.net/hzw19920329) 2016-04-08 12:11:41 ![img](https://csdnimg.cn/release/phoenix/template/new_img/articleReadEyes.png) 30951 ![img](https://csdnimg.cn/release/phoenix/template/new_img/tobarCollectionActive.png) 已收藏 3

分类专栏： [java基础](https://blog.csdn.net/hzw19920329/category_6165934.html)

版权

在比较一个类是否和另一个类属于同一个类实例的时候，我们通常可以采用instanceof和getClass两种方法通过两者是否相等来判断，但是两者在判断上面是有差别的，下面从代码中看看区别：



```java
public class Test



{



	public static void testInstanceof(Object x)



	{



		System.out.println("x instanceof Parent:  "+(x instanceof Parent));



		System.out.println("x instanceof Child:  "+(x instanceof Child));



		System.out.println("x getClass Parent:  "+(x.getClass() == Parent.class));



		System.out.println("x getClass Child:  "+(x.getClass() == Child.class));



	}



	public static void main(String[] args) {



		testInstanceof(new Parent());



		System.out.println("---------------------------");



		testInstanceof(new Child());



	}



}



class Parent {



 



}



class Child extends Parent {



 



}



/*



输出:



x instanceof Parent:  true



x instanceof Child:  false



x getClass Parent:  true



x getClass Child:  false



---------------------------



x instanceof Parent:  true



x instanceof Child:  true



x getClass Parent:  false



x getClass Child:  true



*/
```

从程序输出可以看出,instanceof进行类型检查规则是:你属于该类吗？或者你属于该类的派生类吗？而通过getClass获得类型信息采用==来进行检查是否相等的操作是严格的判断。不会存在继承方面的考虑；