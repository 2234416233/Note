# 深入理解Java中的容器



1、容器的概念
在Java当中，如果有一个类专门用来存放其它类的对象，这个类就叫做容器，或者就叫做集合，集合就是将若干性质相同或相近的类对象组合在一起而形成的一个整体
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301102601329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_8,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301185638582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304112011122.png)
2、容器与数组的关系
之所以需要容器：
1、数组的长度难以扩充
2、数组中数据的类型必须相同
容器与数组的区别与联系：
1、容器不是数组，不能通过下标的方式访问容器中的元素
2、数组的所有功能通过Arraylist容器都可以实现，只是实现的方式不同
3、如果非要将容器当做一个数组来使用，通过toArray方法返回的就是一个数组
示例程序：

```
package IT;

import java.util.ArrayList;
import java.util.Iterator;

//数组的所有功能通过ArrayList容器都可以实现，只是实现的方式不同
public class App
{
     public static void main(String[] args)
     {
    	   ArrayList<Integer> arrayList = new ArrayList<Integer>();
    	   arrayList.add(12);
    	   arrayList.add(10);
    	   arrayList.add(35);
    	   arrayList.add(100);
    	   
    	   Iterator<Integer> iterator = arrayList.iterator();//获取容器的迭代器
    	   while(iterator.hasNext())
    	   {
    		   Integer value = iterator.next();//获取当前游标右边的元素，同时游标右移-->
    		   System.out.println(value);
    	   }
    	   System.out.println("通过ArrayList容器获取一个数组arr：");
    	   Object[] arr = arrayList.toArray();
    	   for(int i=0;i<arr.length;i++)
    	   {
    		   System.out.println(arr[i]);
    	   }
     }
}
123456789101112131415161718192021222324252627282930
```

输出结果：

```
12
10
35
100
通过ArrayList容器获取一个数组arr：
12
10
35
100
123456789
```

3、容器常用的几个方法

```
boolean add(Object obj):向容器中添加指定的元素
Iterator iterator（）：返回能够遍历当前集合中所有元素的迭代器
Object[] toArray():返回包含此容器中所有元素的数组。
Object get（int index）：获取下标为index的那个元素
Object remove（int index）：删除下标为index的那个元素
Object set（int index,Object element）：将下标为index的那个元素置为element
Object add（int index,Object element）：在下标为index的位置添加一个对象element
Object put(Object key,Object value):向容器中添加指定的元素
Object get（Object key）：获取关键字为key的那个对象
int size():返回容器中的元素数
12345678910
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304132425836.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228173021584.png)
实例程序：

```
package IT;

import java.util.ArrayList;

public class App
{
     public static void main(String[] args)
     {
    	   ArrayList<Integer> arrayList = new ArrayList<Integer>();
    	   arrayList.add(12);
    	   arrayList.add(10);
    	   arrayList.add(35);
    	   arrayList.add(100);
    	   System.out.println("原容器中的元素为:");
    	   System.out.println(arrayList);
    	   System.out.println("\n");
    	   
    	   /*******重置set（int index,Object element）*******/
    	   System.out.println("将下标为1位置的元素置为20,将下标为2位置的元素置为70");
           arrayList.set(1, 20);
           arrayList.set(2, 70);
           System.out.println("重置之后容器中的元素为:");
           System.out.println(arrayList);
           System.out.println("\n");
           
           /*******中间插队add（int index,Object element）*******/
           System.out.println("在下标为1的位置插入一个元素,-----插入元素：此时容器后面的元素整体向后移动");
           arrayList.add(1, 80);//在下标为1的位置插入一个元素,此时容量加1,-----位置后面的元素整体向后移动
           System.out.println("插入之后容器中的元素为:");
           System.out.println(arrayList);
           System.out.println("插入之后容器中的容量为:");
           System.out.println(arrayList.size());
           System.out.println("\n");
           
           
           /*******中间删除元素remove（int index）*******/
           System.out.println("将下标为3位置的元素70删除,-----删除元素:此时容器位置后面的元素整体向前移");
           arrayList.remove(3);
           System.out.println("删除之后容器中的元素为:");
           System.out.println(arrayList);
           System.out.println("删除之后容器中的容量为:");
           System.out.println(arrayList.size());
           
     }
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445
```

运行结果：

```
原容器中的元素为:
[12, 10, 35, 100]


将下标为1位置的元素置为20,将下标为2位置的元素置为70
重置之后容器中的元素为:
[12, 20, 70, 100]


在下标为1的位置插入一个元素,-----插入元素：此时容器后面的元素整体向后移动
插入之后容器中的元素为:
[12, 80, 20, 70, 100]
插入之后容器中的容量为:
5


将下标为3位置的元素70删除,-----删除元素:此时容器位置后面的元素整体向前移
删除之后容器中的元素为:
[12, 80, 20, 100]
删除之后容器中的容量为:
4
123456789101112131415161718192021
```

4、容器的分类
容器分为Set集、List列表、Map映射
Set集合：由于内部存储结构的特点，Set集合中不区分元素的顺序（即使存放的类实现了compareTo方法，也是没用的），不允许出现重复的元素（用户自定义的类有的时候需要实现相应方法），TreeSet容器特殊，元素放进去的时候自然而然就有顺序了，Set容器可以与数学中的集合相对应：相同的元素不会被加入。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301100913567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_12,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304111158456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
List列表：由于内部存储结构的特点，List集合中区分元素的顺序，且允许包含重复的元素。List集合中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素—有序，可以重复
Map映射：由于内部存储结构的特点，映射中不能包含重复的键值，每个键最多只能映射一个值，否则会出现覆盖的情况(后面的value值会将前面的value值覆盖掉)，Map是一种把键对象和值对象进行映射的集合，即Map容器中既要存放数据本身，也要存放关键字:相同的元素会被覆盖
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301144152195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304111230179.png)
**注意：对于Set和Map来说,元素放进去之后是没有顺序的，如果希望元素放进去之后是有顺序的，可以用treeSet和treeMap存储数据。**
实例程序：

```
       var set2 = mutable.Set.empty[Int]
       set2 += 10
       set2 ++= List(50,100,200)
       set2 += 500
       println("Set输出的结果：")
       println(set2)
       var map3 = mutable.Map.empty[String,Double]
       map3 += "Spark"->90.0
       map3 += "Hadoop"->80.0
       map3 ++= List("Scala"->100.0,"Java"->60.0)
       println("Map输出的结果：")
       println(map3)
123456789101112
```

运行结果：

```
Set输出的结果：
Set(100, 50, 500, 10, 200)
Map输出的结果：
Map(Hadoop -> 80.0, Spark -> 90.0, Scala -> 100.0, Java -> 60.0)
1234
```

实例程序：

```
       var treeSet = TreeSet(10,20,30,90,100,200,50)
       println(treeSet)
       /*键值对排序是根据key的值进行排序的,没有value的事情,让我联想到了MapReduce中排序的时候之所以根据k2
       而不是v2的值进行排序,这是因为哈希映射内部决定的，而不是MapReduce决定的
       呵呵！注意：排序区分大小写的哦！！！*/
       var treeSet2 = TreeSet[String]("Spark","Anang","Baby","Hello")
       println(treeSet2)
       var treeMap = TreeMap[String,Integer]("Java"->100,"Scala"->88,"Python"->60,"Anglebaby"->500)
       println(treeMap)
123456789
```

运行结果：

```
TreeSet(10, 20, 30, 50, 90, 100, 200)
TreeSet(Anang, Baby, Hello, Spark)
Map(Anglebaby -> 500, Java -> 100, Python -> 60, Scala -> 88)
123
```

示例程序：

```
class Student implements Comparable<Object>
{
	public int id;
	public String nameString;
	
	public Student(int id,String name)
	{
		this.id = id;
		this.nameString = name;
	}
	
	
	@Override 
	public String toString()
	{
		return this.id + " " + this.nameString;
	}
	
	
	@Override
	public int compareTo(Object obj)
	{
		Student stu = (Student)obj;
		if (this.id > stu.id)
		{
			return 1;
		}
		else if (this.id == stu.id)
		{
			return 0;
		}
		else {
			return -1;
		}
	}

}


public class Test_ArrayList_1
{
    public static void main(String[] args)
    {
           HashSet<Integer> hashSet1 = new HashSet<Integer>();
	       hashSet1.add(100);
	       hashSet1.add(200);
	       hashSet1.add(200);
	       hashSet1.add(400);
	       hashSet1.add(10);
	       hashSet1.add(20);
	       hashSet1.add(8000);
	       hashSet1.add(1000);
	       hashSet1.add(2000);
	       hashSet1.add(2000);
	       hashSet1.add(4000);
	       hashSet1.add(100);
	       hashSet1.add(200);
	       hashSet1.add(8000);
           for (Integer integer : hashSet1)
		   {
			  System.out.println(integer);   //打印出的类不含有重复的元素
		   }
           
           System.out.println("--------------------");
           
           
           HashSet<Student> hashSet2 = new HashSet<Student>();     //此时Student有一个问题,没有实现equals方法和hashCode方法.
           hashSet2.add(new Student(1000, "wtt"));
           hashSet2.add(new Student(2000, "wtt"));
           hashSet2.add(new Student(3000, "wtt"));
           hashSet2.add(new Student(3000, "wtt"));
           hashSet2.add(new Student(100, "wtt"));
           hashSet2.add(new Student(50, "wtt"));
           hashSet2.add(new Student(10, "wtt"));
           
           for (Student student : hashSet2)
		   {
			  System.out.println(student);          //尽管Student类实现了compareTo方法,也是没用的,每次打印出元素的顺序是不一样的.
		   }
           
           System.out.println("--------------------");
           
    }
}

/*
 *  1000
	2000
	100
	4000
	20
	200
	10
	400
	8000
	--------------------
	3000 wtt
	1000 wtt
	100 wtt
	10 wtt
	3000 wtt
	2000 wtt
	50 wtt
	--------------------
 * */

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106
```

![这里写图片描述](https://img-blog.csdn.net/20160727144133391)

5、toString()方法的使用：凡是把类对象放到容器中，相应的类都应该实现Object类中的toString()方法；凡是Java中自带的数据类型，都已经重写完了toString()方法
实例1：(未重写toString()方法之前)

```
package IT;


public class App
{
     public static void main(String[] args)
     {
           //Java中自带的类
    	  System.out.println("-----凡是Java中自带的数据类型都已经重写完了toString()方法！---");
    	  System.out.println(new Integer(2).toString());
    	  System.out.println(new String("zhang").toString());
    	  //用户自定义的类Student
    	  System.out.println(new Student("zhangsan",99.8).toString());
    	  
     }
}
class Student
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
}
1234567891011121314151617181920212223242526
```

输出结果：

```
-----凡是Java中自带的数据类型都已经重写完了toString()方法！---
2
zhang
IT.Student@1af2f973
1234
```

实例2：(重写完toString()方法之后)

```
package IT;

import java.util.ArrayList;

public class App
{
     public static void main(String[] args)
     {
         ArrayList<Student> arr = new ArrayList<Student>();
         arr.add(new Student("zhangsan",89.8));
         arr.add(new Student("lisi",90));
         arr.add(new Student("wangwu",60.6));
         
         System.out.println(arr);
     }
}
class Student
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
}
123456789101112131415161718192021222324252627282930
```

输出结果：

```
[zhangsan	89.8, lisi	90.0, wangwu	60.6]
1
```

6、Comparable接口中的compareTo()方法:凡是需要进行比较排序的类都应该实现Comparable接口中的compareTo()方法；凡是把类对象放到以树为内部结构的容器中都应该实现Comparable接口中的compareTo()方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228210731157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_12,color_FFFFFF,t_70)
实例1：

```
package IT;

import java.util.ArrayList;
import java.util.Collections;

public class App
{
     public static void main(String[] args)
     {
         ArrayList<Student> arr = new ArrayList<Student>();
         arr.add(new Student("zhangsan",89.8));
         arr.add(new Student("lisi",90));
         arr.add(new Student("wangwu",60.6));
         arr.add(new Student("wangting",85.6));
         
         Collections.sort(arr);
         
         for (Student student : arr)
		{
			System.out.println(student);
		}
         
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      return (int) (this.score - obj.score);//比较的标准为score进行升序
	}
}
123456789101112131415161718192021222324252627282930313233343536373839404142
```

输出结果：

```
wangwu	60.6
wangting	85.6
zhangsan	89.8
lisi	90.0
1234
```

实例2：

```
package IT;


import java.util.TreeSet;

public class App
{
     public static void main(String[] args)
     {
            TreeSet<Student> treeSet = new TreeSet<Student>();
            treeSet.add(new Student("wangwu",60.6));
            treeSet.add(new Student("lisi",90.0));
            treeSet.add(new Student("wangting",85.6));
            treeSet.add(new Student("zhangsan",60.6));
            
            for (Student student : treeSet)
			{
				System.out.println(student);
			}
         
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      if(this.score > obj.score)
	    	  return 1;
	      else
	    	  return -1;
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

输出结果：

```
zhangsan	60.6
wangwu	60.6
wangting	85.6
lisi	90.0
1234
```

7、凡是把类对象放到以哈希表为内部存储结构的容器中，相应的类必须要实现equals方法和hashCode方法，这样才符合哈希表真实的逻辑功能.（对于咱们自己定义的类，如果你没有重写hashcode方法，我们可以通过hashcode方法获取该对象的内存地址）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301180934855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
**简而言之：哈希表先根据它的hashcode方法提供的哈希码找到存储的位置，在从位置所关联的链表里面寻找是否有相同的对象，如果有相同的对象，则不存放，如果没有，则存放进去。** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301184852790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301193242564.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
如果你还不懂，看我这篇文章吧：https://wenku.baidu.com/view/a1f1c88ce518964bcf847cd0
实例程序1：(为重写之前)

```
package IT;


import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

public class App
{
     public static void main(String[] args)
     {
           //Java中自带的数据类型
    	  System.out.println("先测试Java中自带的数据类型:");
    	  HashMap<String, Double> hashMap1 = new HashMap<String,Double>();
    	  hashMap1.put("zhangsan", 96.0);
    	  hashMap1.put("lisi", 88.6);
    	  hashMap1.put("wangwu", 98.6);
    	  hashMap1.put("wangting",  87.5);
    	  hashMap1.put("zhangsan", 96.0);
    	  hashMap1.put("lisi", 88.6);
    	  hashMap1.put("wangwu", 98.6);
    	  hashMap1.put("wangting",  87.5);
    	  
    	  Set<String> keySet = hashMap1.keySet();
    	  Iterator<String> iterator = keySet.iterator();
    	  while(iterator.hasNext())
    	  {
    		  String key = iterator.next();
    		  System.out.println(key+"\t"+hashMap1.get(key));
    	  }
    	  System.out.println("Java中自带的数据类型:相同的对象会覆盖！");
    	  System.out.println("\n");
    	  //用户自定义的数据类型：为重写之前
    	  System.out.println("测试用户自定义的数据类型--未重写两个方法之前:");
    	  HashMap<Student, String> hashMap2 = new HashMap<Student,String>();
    	  hashMap2.put(new Student("zhangsan",88.8), "beijing");
    	  hashMap2.put(new Student("lisi",88.8), "beijing");
    	  hashMap2.put(new Student("wangwu",66.9), "beijing");
    	  hashMap2.put(new Student("zhangsan",88.8), "beijing");
    	  hashMap2.put(new Student("lisi",88.8), "beijing");
    	  hashMap2.put(new Student("wangwu",66.9), "beijing");
    	  Set<Student> keySet2 = hashMap2.keySet();
    	  Iterator<Student> iterator2 = keySet2.iterator();
    	  while(iterator2.hasNext())
    	  {
    		  Student key = iterator2.next();
    		  System.out.println(key+"\t"+hashMap2.get(key));
    	  }
    	  System.out.println("如果没有重写:导致相同的对象不会被覆盖!");
    	  
    	 
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      if(this.score > obj.score)
	    	  return 1;
	      else
	    	  return -1;
	}
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374
```

输出结果:

```
先测试Java中自带的数据类型:
wangting	87.5
wangwu	98.6
lisi	88.6
zhangsan	96.0
Java中自带的数据类型:相同的对象会覆盖！


测试用户自定义的数据类型--为重写两个方法之前:
zhangsan	88.8	beijing
wangwu	66.9	beijing
lisi	88.8	beijing
wangwu	66.9	beijing
zhangsan	88.8	beijing
lisi	88.8	beijing
如果没有重写:导致相同的对象不会被覆盖!
12345678910111213141516
```

实例程序2:重写之后

```
package IT;


import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

public class App
{
     public static void main(String[] args)
     {
    	  //用户自定义的数据类型：为重写之后
    	  System.out.println("测试用户自定义的数据类型--重写两个方法之后:");
    	  HashMap<Student, String> hashMap2 = new HashMap<Student,String>();
    	  hashMap2.put(new Student("zhangsan",88.8), "beijing");
    	  hashMap2.put(new Student("lisi",88.8), "beijing");
    	  hashMap2.put(new Student("wangwu",66.9), "beijing");
    	  hashMap2.put(new Student("zhangsan",88.8), "beijing");
    	  hashMap2.put(new Student("lisi",88.8), "beijing");
    	  hashMap2.put(new Student("wangwu",66.9), "beijing");
    	  Set<Student> keySet2 = hashMap2.keySet();
    	  Iterator<Student> iterator2 = keySet2.iterator();
    	  while(iterator2.hasNext())
    	  {
    		  Student key = iterator2.next();
    		  System.out.println(key+"\t"+hashMap2.get(key));
    	  }
    	  System.out.println("重写过后:相同的对象会被覆盖!");
    	  
    	 
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      if(this.score > obj.score)
	    	  return 1;
	      else
	    	  return -1;
	}
	@Override
	public int hashCode()
	{
		return (int) (this.name.hashCode()*score);//保证相同对象映射到同一个索引位置
	}
	@Override
	public boolean equals(Object obj)
	{
		Student cc = (Student)obj;
		return this.name==cc.name&&this.score==cc.score;
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364
```

输出结果:

```
测试用户自定义的数据类型--重写两个方法之后:
wangwu	66.9	beijing
zhangsan	88.8	beijing
lisi	88.8	beijing
重写过后:相同的对象会被覆盖!
12345
```

8、重要的一个逻辑:逻辑上来讲，只要两个对象的内容相同，其地址(hashCode()返回值)以及这两个对象就应该相同(equals())，
实例程序(为重写之前):

```
package IT;

public class App
{
     public static void main(String[] args)
     {
    	 //Java中自带的数据类型
    	 System.out.println(new Integer(1).equals(new Integer(1)));
    	 System.out.println(new Integer(1).hashCode()==new Integer(1).hashCode());
    	 System.out.println(new String("zhang").equals(new String("zhang")));
    	 System.out.println(new String("zhang").hashCode()==new String("zhang").hashCode());
    	 
         System.out.println("\n");
         
         //用户自定义的数据类型
         
    	 System.out.println(new Student("zhangsan",98.8).equals(new Student("zhangsan",98.8)));
    	 System.out.println(new Student("zhangsan",98.8).hashCode());
    	 System.out.println(new Student("zhangsan",98.8).hashCode());
    	 
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      if(this.score > obj.score)
	    	  return 1;
	      else
	    	  return -1;
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

输出结果:

```
true
true
true
true


false
488676694
1211729930
123456789
```

重写之后：

```
package IT;

public class App
{
     public static void main(String[] args)
     {
    	 System.out.println(new Student("zhangsan",98.8).equals(new Student("zhangsan",98.8)));
    	 System.out.println(new Student("zhangsan",98.8).hashCode());
    	 System.out.println(new Student("zhangsan",98.8).hashCode());
    	  
    	 
     }
}
class Student implements  Comparable<Student>
{
     public String name;
     public double score;
     public Student(String name,double score)
     {
    	 this.name = name;
    	 this.score = score;
     }
     public String toString()
     {
    	 return this.name+"\t"+this.score;
     }
	public int compareTo(Student obj)
	{
	      if(this.score > obj.score)
	    	  return 1;
	      else
	    	  return -1;
	}
	@Override
	public int hashCode()
	{
		return (int) (this.name.hashCode()*score);
	}
	@Override
	public boolean equals(Object obj)
	{
		Student cc = (Student)obj;
		return this.name==cc.name&&this.score==cc.score;
	}
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445
```

输出结果:

```
true
-2147483648
-2147483648
123
```

上面的5、6、7、8可以归结为4个"凡是"，1个“逻辑”:
1、凡是把类对象放到容器中，相应的类都应该实现Object类中的toString()方法；
2、凡是需要进行比较排序的类都应该实现Comparable接口中的compareTo()方法；凡是把类对象放到以树为内部结构的容器中都应该实现Comparable接口中的compareTo()方法
3、凡是把类对象放到以哈希表为内部存储结构的容器中，相应的类必须要实现equals方法和hashCode方法，这样才符合哈希表真实的逻辑功能.
4、逻辑上来讲，只要两个对象的内容相同，其地址(hashCode()返回值)以及这两个对象就应该相同(equals())。
9、哈希冲突的相关概念
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190301142009623.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
本质上讲就是:hash(对象1.hashCode())=hash2(对象2.hashCode())，即第一个对象的hashCode()方法返回的哈希码值带入到哈希函数后得到的索引位置与第二个对象的hashCode()方法返回的哈希码值带入到哈希函数后得到的索引位置相同，这就是哈希冲突。
最常见的哈希算法是取模法。
下面简单讲讲取模法的计算过程。
比如：数组的长度是5。这时有一个数据是6。那么如何把这个6存放到长度只有5的数组中呢。按照取模法，计算6％5，结果是1，那么就把6放到数组下标是1的位置。那么，7
就应该放到2这个位置。到此位置，哈斯冲突还没有出现。这时，有个数据是11，按照取模法，11％5＝1，也等于1。那么原来数组下标是1的地方已经有数了，是6。这时又计算出1这个位置，那么数组1这个位置，就必须储存两个数了。这时，就叫哈希冲突。冲突之后就要按照顺序来存放了。
如果数据的分布比较广泛，而且储存数据的数组长度比较大。
那么哈希冲突就比较少。否则冲突是很高的。
10、iterator接口的作用
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304104505807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304104747885.png)
重要方法:

```
boolean hasNext():是用来判断当前游标(迭代器)的后面是否存在元素，如果存在返回真，否则返回假
Object next()：先返回当前游标右边的元素，然后游标后移一个位置
void remove()：不推荐使用iterator的remove()方法，而是推荐使用容器自带的remove方法。
123
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304103327282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EyMDExNDgwMTY5,size_6,color_FFFFFF,t_70)
实例程序:

```
package IT;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

public class App
{
     public static void main(String[] args)
     {
    	  HashMap<String, Double> hashMap = new HashMap<String,Double>();
    	  hashMap.put("zhangsan", 88.6);
    	  hashMap.put("lisi", 69.0);
    	  hashMap.put("wanqwu", 100.0);
    	  hashMap.put("lisi", 69.0);
    	  
    	  Set<String> keySet = hashMap.keySet();
    	  Iterator<String> iterator = keySet.iterator();
    	  while(iterator.hasNext())
    	  {
    		  String key = iterator.next();//获取迭代器右边的元素，同时右移
    		  System.out.println(key+hashMap.get(key));
    	  }
    	  
    	 
     }
}
123456789101112131415161718192021222324252627
```

思考题:

```
package IT;


import java.util.TreeSet;

public class App
{
     public static void main(String[] args)
     {
             TreeSet<Student> treeSet = new TreeSet<Student>();
           
         
             treeSet.add(new Student("zhangsan",98));
             treeSet.add(new Student("zhangsan",98));
             System.out.println(treeSet.size());
             System.out.println(treeSet);
    	 
             //本程序中并没有重写equals方法，但是treeSet将识别出两个new Student("zhangsan",98)为相同的，因为内部数据结构吗？
             System.out.println(new Student("zhangsan",98).equals(new Student("zhangsan",98)));
 
     }
}
class Student implements Comparable<Object>
{
	  public String name;
	  public double score;
	  public Student(String name,double score)
	  {
		  this.name = name;
		  this.score = score;
	  }
	  public String toString()
	  {
		  return name + "\t" + score;
	  }
	@Override
	public int compareTo(Object obj)
	{
		Student cc = (Student)obj;
		return (int) (this.score - cc.score);
	}

}
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

程序3：

```
public class Test_iterator
{
    public static void main(String[] args)
	{
		ArrayList<Integer> arrayList = new ArrayList<Integer>();
		arrayList.add(10);
		arrayList.add(20);
		arrayList.add(30);
		
		Iterator<Integer> iterator = arrayList.iterator();
		
		
		while (iterator.hasNext())
		{
			Integer aaInteger = iterator.next();
			System.out.println(aaInteger);
			
		}
	}
}


/**
 *  10
	20
	30
 */
123456789101112131415161718192021222324252627
```

上面的讲解如有问题，欢迎留言指正！