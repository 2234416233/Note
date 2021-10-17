# ArrayList

### 

ArrayList是一个容量可以自动增长的动态数组，它继承了AbstractList，实现了List、RandomAccess, Cloneable, java.io.Serializable。
基本的ArrayList，长于随机访问元素，但是在List中间插入和移除元素时较慢。同时，ArrayList的操作不是线程安全的！一般在单线程中才使用ArrayList，而在多线程中一般使用Vector或者CopyOnWriteArrayList。

ArrayList遍历方式
ArrayList有三种遍历方式

##### 迭代器遍历

```java
Iterator<Integer> it = arrayList.iterator();
while(it.hasNext()){
    System.out.print(it.next() + " ");
}
```

##### 索引值遍历

```java
for(int i = 0; i < arrayList.size(); i++){
   System.out.print(arrayList.get(i) + " ");
}
```

##### for循环遍历

```java

for(Integer number : arrayList){
   System.out.print(number + " ");
}
```


需要说明的是，遍历ArrayList时，通过索引值遍历效率最高，for循环遍历次之，迭代器遍历最低。

toArray()的使用
有时候，当我们调用ArrayList中的 toArray()，可能遇到过抛出java.lang.ClassCastException异常的情况，这是由于toArray() 返回的是 Object[] 数组，将 Object[] 转换为其它类型(如，将Object[]转换为的Integer[])则会抛出java.lang.ClassCastException异常，因为Java不支持向下转型。
所以一般更常用的是使用另外一种方法进行使用：

```java
<T> T[] toArray(T[] a)
```


调用toArray(T[] a)返回T[]可通以下方式进行实现：

```java
 // toArray用法
 // 第一种方式(最常用)
 Integer[] integer = arrayList.toArray(new Integer[0]);

 // 第二种方式(容易理解)
 Integer[] integer1 = new Integer[arrayList.size()];
 arrayList.toArray(integer1);

 // 抛出异常，java不支持向下转型
 //Integer[] integer2 = new Integer[arrayList.size()];
 //integer2 = arrayList.toArray();
```

##### ArrayList用法示例

```java
/**

 * @author GongchuangSu
 * @since 2016.05.10
   */
   import java.util.ArrayList;
   import java.util.Iterator;

public class ArrayListDemo {
public static void main(String[] srgs){
     ArrayList<Integer> arrayList = new ArrayList<Integer>();

     System.out.printf("Before add:arrayList.size() = %d\n",arrayList.size());

     arrayList.add(1);
     arrayList.add(3);
     arrayList.add(5);
     arrayList.add(7);
     arrayList.add(9);
     System.out.printf("After add:arrayList.size() = %d\n",arrayList.size());

     System.out.println("Printing elements of arrayList");
     // 三种遍历方式打印元素
     // 第一种：通过迭代器遍历
     System.out.print("通过迭代器遍历:");
     Iterator<Integer> it = arrayList.iterator();
     while(it.hasNext()){
         System.out.print(it.next() + " ");
     }
     System.out.println();

     // 第二种：通过索引值遍历
     System.out.print("通过索引值遍历:");
     for(int i = 0; i < arrayList.size(); i++){
         System.out.print(arrayList.get(i) + " ");
     }
     System.out.println();

     // 第三种：for循环遍历
     System.out.print("for循环遍历:");
     for(Integer number : arrayList){
         System.out.print(number + " ");
     }

     // toArray用法
     // 第一种方式(最常用)
     Integer[] integer = arrayList.toArray(new Integer[0]);

     // 第二种方式(容易理解)
     Integer[] integer1 = new Integer[arrayList.size()];
     arrayList.toArray(integer1);

     // 抛出异常，java不支持向下转型
     //Integer[] integer2 = new Integer[arrayList.size()];
     //integer2 = arrayList.toArray();
     System.out.println();

     // 在指定位置添加元素
     arrayList.add(2,2);
     // 删除指定位置上的元素
     arrayList.remove(2);    
     // 删除指定元素
     arrayList.remove((Object)3);
     // 判断arrayList是否包含5
     System.out.println("ArrayList contains 5 is: " + arrayList.contains(5));

     // 清空ArrayList
     arrayList.clear();
     // 判断ArrayList是否为空
     System.out.println("ArrayList is empty: " + arrayList.isEmpty());
}
}
/**
Before add:arrayList.size() = 0
After add:arrayList.size() = 5
Printing elements of arrayList
通过迭代器遍历:1 3 5 7 9 
通过索引值遍历:1 3 5 7 9 
for循环遍历:1 3 5 7 9 
ArrayList contains 5 is: true
ArrayList is empty: true
*/
```


##### ArrayList源码解析

```java
package java.util;

public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 序列版本号
    private static final long serialVersionUID = 8683452581122892189L;
// 默认容量大小
private static final int DEFAULT_CAPACITY = 10;

// 空数组
private static final Object[] EMPTY_ELEMENTDATA = {};

// 用于保存ArrayList中数据的数组
private transient Object[] elementData;

// ArrayList中所包含元素的个数
private int size;

// 带初始容量参数的构造函数
public ArrayList(int initialCapacity) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    this.elementData = new Object[initialCapacity];
}

// 默认构造函数，其默认初始容量为10
public ArrayList() {
    super();
    this.elementData = EMPTY_ELEMENTDATA;
}

// 带Collection参数的构造函数
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    size = elementData.length;
    // c.toArray might (incorrectly) not return Object[] (see 6260652)
    if (elementData.getClass() != Object[].class)
        elementData = Arrays.copyOf(elementData, size, Object[].class);
}

// 将此 ArrayList 实例的容量调整为列表的当前大小(实际元素个数)
public void trimToSize() {
    modCount++;
    if (size < elementData.length) {
        elementData = Arrays.copyOf(elementData, size);
    }
}

// 如有必要，增加此 ArrayList 实例的容量，以确保它至少能够容纳最小容量参数所
// 指定的元素数
public void ensureCapacity(int minCapacity) {
    int minExpand = (elementData != EMPTY_ELEMENTDATA)
        // any size if real element table
        ? 0
        // larger than default for empty table. It's already supposed to be
        // at default size.
        : DEFAULT_CAPACITY;

    if (minCapacity > minExpand) {
        ensureExplicitCapacity(minCapacity);
    }
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}

// 返回ArrayList中的元素个数
public int size() {
    return size;
}

// 判断ArrayList是否为空
public boolean isEmpty() {
    return size == 0;
}

// 判断ArrayList是否包含Object(o)
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}

// 返回ArrayList中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1
public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}

// 返回ArrayList中最后一次出现的指定元素的索引，或如果此列表不包含索引，则返回 -1
public int lastIndexOf(Object o) {
    if (o == null) {
        for (int i = size-1; i >= 0; i--)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = size-1; i >= 0; i--)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}

// 返回此 ArrayList 实例的浅表副本
public Object clone() {
    try {
        @SuppressWarnings("unchecked")
        ArrayList<E> v = (ArrayList<E>) super.clone();
        // 将当前ArrayList的全部元素拷贝到v中
        v.elementData = Arrays.copyOf(elementData, size);
        v.modCount = 0;
        return v;
    } catch (CloneNotSupportedException e) {
        // this shouldn't happen, since we are Cloneable
        throw new InternalError();
    }
}

// 按适当顺序（从第一个到最后一个元素）返回包含此列表中所有元素的数组
public Object[] toArray() {
    return Arrays.copyOf(elementData, size);
}

// 返回ArrayList的模板数组。所谓模板数组，即可以将T设为任意的数据类型
@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}

// 位置访问操作   
@SuppressWarnings("unchecked")
E elementData(int index) {
    return (E) elementData[index];
}

// 返回ArrayList中指定位置上的元素
public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}

// 用指定的元素替代ArrayList中指定位置上的元素，并返回替代前的元素
public E set(int index, E element) {
    rangeCheck(index);

    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}

// 将指定的元素添加到ArrayList的尾部
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

// 将指定的元素插入ArrayList中的指定位置
public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}

// 移除ArrayList中指定位置上的元素，并返回该位置上的元素
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}

// 移除ArrayList中首次出现的指定元素（如果存在则移除并返回true，否则返回false）
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}

// 私有方法，用于快速移除
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}

// 移除ArrayList中的所有元素
public void clear() {
    modCount++;

    // clear to let GC do its work
    for (int i = 0; i < size; i++)
        elementData[i] = null;

    size = 0;
}

// 按照指定 collection 的迭代器所返回的元素顺序，
// 将该 collection 中的所有元素添加到ArrayList的尾部
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}

// 从指定的位置开始，将指定 collection 中的所有元素插入到ArrayList中
public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);

    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount

    int numMoved = size - index;
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew,
                         numMoved);

    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}

// 移除列表中索引在 fromIndex（包括）和 toIndex（不包括）之间的所有元素
protected void removeRange(int fromIndex, int toIndex) {
    modCount++;
    int numMoved = size - toIndex;
    System.arraycopy(elementData, toIndex, elementData, fromIndex,
                     numMoved);

    // clear to let GC do its work
    int newSize = size - (toIndex-fromIndex);
    for (int i = newSize; i < size; i++) {
        elementData[i] = null;
    }
    size = newSize;
}

// 私有方法，用于范围检测
private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

// 私有方法，用于add和addAll
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
private String outOfBoundsMsg(int index) {
    return "Index: "+index+", Size: "+size;
}

// 移除ArrayList中Collection所包含的所有元素
public boolean removeAll(Collection<?> c) {
    return batchRemove(c, false);
}

// 保留所有ArrayList和Collection共有的元素
public boolean retainAll(Collection<?> c) {
    return batchRemove(c, true);
}

private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        for (; r < size; r++)
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // Preserve behavioral compatibility with AbstractCollection,
        // even if c.contains() throws.
        if (r != size) {
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            w += size - r;
        }
        if (w != size) {
            // clear to let GC do its work
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}

// java.io.Serializable的写入函数
// 将ArrayList的“容量，所有的元素值”都写入到输出流中
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException{
    // Write out element count, and any hidden stuff
    int expectedModCount = modCount;
    s.defaultWriteObject();

    // Write out size as capacity for behavioural compatibility with clone()
    s.writeInt(size);

    // Write out all elements in the proper order.
    for (int i=0; i<size; i++) {
        s.writeObject(elementData[i]);
    }

    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}

// java.io.Serializable的读取函数：根据写入方式读出
// 先将ArrayList的“容量”读出，然后将“所有的元素值”读出
private void readObject(java.io.ObjectInputStream s)
    throws java.io.IOException, ClassNotFoundException {
    elementData = EMPTY_ELEMENTDATA;

    // Read in size, and any hidden stuff
    s.defaultReadObject();

    // Read in capacity
    s.readInt(); // ignored

    if (size > 0) {
        // be like clone(), allocate array based upon size not capacity
        ensureCapacityInternal(size);

        Object[] a = elementData;
        // Read in all elements in the proper order.
        for (int i=0; i<size; i++) {
            a[i] = s.readObject();
        }
    }
}

// 返回一个从指定位置开始遍历的ListIterator迭代器
public ListIterator<E> listIterator(int index) {
    if (index < 0 || index > size)
        throw new IndexOutOfBoundsException("Index: "+index);
    return new ListItr(index);
}

// 返回一个ListIterator迭代器
public ListIterator<E> listIterator() {
    return new ListItr(0);
}

// 返回一个Iterator迭代器
public Iterator<E> iterator() {
    return new Itr();
}

// 返回一个指定范围的子List列表
public List<E> subList(int fromIndex, int toIndex) {
    subListRangeCheck(fromIndex, toIndex, size);
    return new SubList(this, 0, fromIndex, toIndex);
}
}
```






