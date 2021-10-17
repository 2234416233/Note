# 【J2SE】容器类——List、Set和Map



一、容器

   java容器主要分为三种类型：set（集）、List（列表）、Map（映射）。Java  API所提供的一系列的实例，用于在程序中存放对象。

①J2SDK所提供的容器API位于java.util包内。

②容器API的类图结构如下所示：

​    ![img](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161217161905977)



二、Collection

  Collection接口—定义了存取一组对象的方法，其子接口Set和List分别定义了存储方式。



  容器类对象在调用remove、contains等方法时需要比较对象是否相等，这会涉及到对象类型的equals方法和hashCode方法；对于自定义的类型，需要重写equals  和 hashCode方法以实现自定义的对象相等规则。（注意：相等的对象应该具有相等的hash  codes。）

  增强Name类的equals和hashCode方法如下：

```java
public boolean equals(Object){
	if(obj instanceof Name){
	   Name name = (Name) obj;
           return (firstName.equals(name.firstName))
               && (lastName.equals(name.lastName));
      }
      return super.equals(obj);
}
public int hashCode(){
      return firstName.hashCode();
}
```



1、Set是最简单的一种集合，集合中的对象不按特定的方式排序，并且没有重复对象。Set接口主要实现了三个类：

①HashSet：HashSet类按照哈希算法来存取集合中的对象，存取速度比较快。为快速查找设计Set。存入HashSet的对象必须定义hashCode（）。

②TreeSet：TreeSet类实现了SortedSet接口，能够对集合中的对象进行排序。

③LinkedHashSet：具有HashSet的查询速度，且内部使用链表维护元素的顺序（插入的次序）。因此在使用迭代器遍历Set时，结果会按元素插入的次序显示。



2、List的特征是其元素以线性方式存储，集合中可以存放重复对象。

①ArrayList：代表长度可以改变的数组，可以对元素进行随机的访问，向ArrayList中插入与删除元素的速度慢。

②LinkedList：在实现中采用**链表**数据结构。插入和删除速度快，访问速度慢。



三、Map接口：

  Map是一种把键对象和值对象映射的集合，它的每一个元素都包含一堆键对象和值对象。Map接口并没有继承Collection接口。从Map集合中检索元素时，只要给出键对象，就会返回对应的值对象。Map接口的实现类有HashMap、TreeMap以及LinkedHashMap等Map类中存储的键值对通过键来标识，所以键值不能重复。

①HashMap：给予哈希表的Map接口的实现。

②LinkedHashMap：此类实现了一个哈希表，该哈希表将键映射到相应的值。

③TreeMap：基于红黑树的NavigableMap实现。



四、Iterator和增强for循环

  1、Iterator：所有实现了Collection接口的容器类都有一个iterator方法，用以返回一个实现了iterator接口的对象。Iterator对象称作迭代器，用以方便的实现对容器内元素的遍历操作。

   Iterator接口定义了如下方法：

   ![img](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161217170218449)



   2、增强for循环主要用来取集合数组的数据。只能用在数组实现iterable接口的集合类上。

```java
package cn.itcast.generic;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Set;
import org.junit.Test;


public class Demo2 {
	@Test
	public void test1(){
		List<String> list = new ArrayList<String>();
		list.add("aaa");
		list.add("bbb");
		list.add("ccc");
		//传统
		Iterator<String> it = list.iterator(); //得到了集合迭代器
		while(it.hasNext()){
			String value = it.next();
			System.out.println(value);			
		}		
		//增强for循环
		for(String s:list){
			System.out.println(s);
		}		
	}
	@Test
	public void test2(){
		Map<Integer,String> map = new LinkedHashMap<Integer,String>();
		map.put(1, "aaa");
		map.put(2, "bbb");
		map.put(3, "ccc");
		//传统 keyset entryset
		Set<Map.Entry<Integer,String>> set = map.entrySet();
		Iterator<Map.Entry<Integer,String>> it = set.iterator();
		while(it.hasNext()){
			Map.Entry<Integer, String> entry = it.next();		
			int key=entry.getKey();
			String value = entry.getValue();
			System.out.println(key + "=" + value);			
		}
		//增强for循环，增强for循环不能直接对map进行迭代
		for(Map.Entry<Integer, String> entry:map.entrySet()){
			int key = entry.getKey();
			String value=entry.getValue();
			System.out.println(key + "=" + value);
		}
	}
}
```


输出结果：



![img](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161217170732394)  ![img](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20161217170747285)