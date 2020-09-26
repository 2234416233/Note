# Python 链表

​    数据结构是计算机科学必须掌握的一门学问，很多的教材都是用C语言实现链表，因为C有指针，可以很方便的控制内存，很方便就实现链表，其他的语言，则没那么方便，有很多都是用模拟链表.

   **因为python是动态语言，可以直接把对象赋值给新的变量。**在C/C++中，通常采用“指针+结构体”来实现链表；**而在Python中，则可以采用“引用+类”来实现链表**

**。**

链表的定义：是一组数据项的集合，其中每个数据项都是一个节点的一部分，每个节点还包含指向下一个节点的链接

链表的结构：data为自定义的数据，next为下一个节点的地址。

​                   ![img](https://img-blog.csdn.net/20180721153428887?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzcyODAzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

​          ![img](https://img-blog.csdnimg.cn/20190603130743945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwODE1MjM3,size_16,color_FFFFFF,t_70)

**基本元素**：

1. 节点：每个节点有两个部分，左边称为值域，存放用户数据；右边部分称为指针域，用来存放指向下一个元素的指针。
2. head:head节点永远指向第一个节点
3. tail: tail永远指向最后一个节点
4. None:链表中最后一个节点的指针域为None值

**链表种类：**单向链表、单向循环链表、双向链表、双向循环链表

​                  ![img](https://img-blog.csdn.net/20180721154905692?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzcyODAzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  1、节点类定义如下，我们将节点类定义成Node，该类在初始化实例对象时，定义了两个实例变量，其中data用来存储节点的值，next用来存储下一个节点的索引：

```python
class Node:
    def __init__(self,data = None, next = None):
        self.data = data
        self.next = next
```

2、定义一个节点，如下创建了三个独立的节点：

```
node1 = Node(1)
node2 = Node(2)
node3 = Node(3)
```

然后再把每个节点的关系表示出来，就OK了。

```
node1.next = node2
node2.next = node3
```

### 操作链表的原理知识

### 1.遍历链表

链表的基本操作：遍历next节点 

- 在列表中查找一个元素 
- 在列表中插入一个元素
- 从列表中删除一列元素

  **不可以用head来遍历列表，否则会丢失列表的一些节点 ，可以使用和head相同类型的临时的指针变量**，这个变量先初始化为链表结构的head指针，然后控制一个循环。

```python
probe = head
while probe != None:
    probe = probe.next
```

### 插入

![img](https://img-blog.csdnimg.cn/20190819213746607.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwODE1MjM3,size_16,color_FFFFFF,t_70)

### 3.删除

![img](https://img-blog.csdnimg.cn/20190819213757897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwODE1MjM3,size_16,color_FFFFFF,t_70)

```python
#先定一个node的类


class Node():                  #value + next

    def __init__ (self, value = None, next = None):
        self._value = value
        self._next = next
    def getValue(self):
        return self._value
    def getNext(self):
        return self._next
    def setValue(self,new_value):
        self._value = new_value
    def setNext(self,new_next):
        self._next = new_next

#实现Linked List及其各类操作方法
class LinkedList():

    def __init__(self):      #初始化链表为空表
        self._head = Node() 
        self._tail = None
        self._length = 0

    #检测是否为空

    def isEmpty(self):
        return self._head == None  
    #add在链表前端添加元素:O(1)
    def add(self,value):
        newnode = Node(value,None)    #create一个node（为了插进一个链表）
        newnode.setNext(self._head)   
        self._head = newnode

    #append在链表尾部添加元素:O(n)


    def append(self,value):
        newnode = Node(value)
        if self.isEmpty():
            self._head = newnode   #若为空表，将添加的元素设为第一个元素
        else:
            current = self._head
            while current.getNext() != None:
                current = current.getNext()   #遍历链表
            current.setNext(newnode)   #此时current为链表最后的元素


    #search检索元素是否在链表中    
    def search(self,value):
        current=self._head
        foundvalue = False
        while current != None and not foundvalue:
            if current.getValue() == value:
                foundvalue = True
            else:
                current=current.getNext()
        return foundvalue

    #index索引元素在链表中的位置

    def index(self,value):
        current = self._head
        count = 0
        found = None
        while current != None and not found:
            count += 1
            if current.getValue()==value:
                found = True
            else:
                current=current.getNext(
        if found:
            return count
        else:
            raise ValueError ('%s is not in linkedlist'%value)
    #remove删除链表中的某项元素

    def remove(self,value):
        current = self._head
        pre = None
        while current!=None:
            if current.getValue() == value:
                if not pre:
                    self._head = current.getNext()
                else:
                    pre.setNext(current.getNext())
                break
            else:
                pre = current
                current = current.getNext()
    #insert链表中插入元素
    def insert(self,pos,value):
        if pos <= 1:
            self.add(value)
        elif pos > self.size():
            self.append(value)
        else:
            temp = Node(value)
            count = 1
            pre = None
            current = self._head
            while count < pos:
                count += 1
                pre = current
                current = current.getNext()
            pre.setNext(temp)
            temp.setNext(current)
```

------

leetcode:

   给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例：**

```html
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
# Definition for singly-linked list.

# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None


class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        n=l1.val+l2.val
        l3=ListNode(n % 10)
        l3.next=ListNode(n//10)
        p1=l1.next
        p2=l2.next
        p3=l3
        while True:
            if p1 and p2:
                sum=p1.val+p2.val+p3.next.val
                p3.next.val=sum%10
                p3.next.next=ListNode(sum//10)
                p1=p1.next
                p2=p2.next
                p3=p3.next
            elif p1 and not p2:
                sum=p1.val+p3.next.val
                p3.next.val=sum%10
                p3.next.next=ListNode(sum//10)
                p1=p1.next
                p3=p3.next
            elif not p1 and p2:
                sum=p2.val+p3.next.val
                p3.next.val=sum%10
                p3.next.next=ListNode(sum//10)
                p2=p2.next
                p3=p3.next
            else:
                if p3.next.val==0:
                    p3.next=None
                break
        return l3
```

from :https://www.cnblogs.com/kumata/p/9147077.html

单链表翻转：`开始以单链表的第二个元素为循环变量，用2个变量循环向后操作,并设置1个辅助变量tmp,保存数据;时间消耗O(n),空间消耗O(1)`

```python
def reverse_linkedlist3(head):
    if head == None or head.next == None: #边界条件
        return head
    p1 = head #循环变量1
    p2 = head.next #循环变量2
    tmp = None #保存数据的临时变量
    while p2:
        tmp = p2.next  #如果没有他，p2.next这个节点以及后面的节点都将会消失
        p2.next = p1
        p1 = p2
        p2 = tmp
    head.next = None
    return p1
```

​             ![img](https://img-blog.csdnimg.cn/20190819213607201.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwODE1MjM3,size_16,color_FFFFFF,t_70)

- 

