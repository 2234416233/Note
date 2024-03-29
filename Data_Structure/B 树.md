# B 树



**B 树和红黑树的动画小吴还在制作当中，比想象中的复杂好多好多好多，今天先来一个图解版的 B 树。。。**

### 1 引言

数据库的增删改查等操作是开发过程中最为常见也是尤为重要的，尤其是现在大数据的兴起，导致数据存储量急剧增加，提升数据的操作效率就变得尤为关键。

大部分数据库的索引都采用树的结构存储，这是因为树的查询效率相对较高，且保持有序。

对于二叉搜索树的时间复杂度是O(logN)，在算法以及逻辑上来分析，二叉搜索树的查找速度以及数据比较次数都是较小的。

但是我们不得不考虑一个新的问题。

数据量是远大于内存大小的，那我们在查找数据时并不能将全部数据同时加载至内存。既然不能全部加载至内存中就只能逐步的去加载磁盘中某个页，简而言之就是逐一的去加载磁盘，加数据分块的加载至内存进行查找与比较。

例如：在图1.1所示的树中查找10，树中的每个节点代表一个磁盘页。每次访问一个新节点代表一次磁盘IO。

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057668-53cd9e06db5354a.png)图1.0



![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057668-a07d1f5f7401902.png)图1.1

通过查找过程可以看出，磁盘IO次数与树的高度相关，在最坏情况下，磁盘IO次数等于树的高度。由于磁盘IO过程是相对耗时效率较低的，因此，在设计数据存储结构时需要降低树的高度，即将一棵“瘦高”的树变得“矮胖”。

当数据数目相同，在保持有序前提下，降低树高度，只需将节点中存储的key值增加，即二叉搜索树中每个节点只有一个key，现将一个节点中存储多个key，得到的树即为B树。

### 2 定义

**B树**也称B-树,B-树直接读作B树，不能因为有“-”号就读作B减树，它是一颗多路平衡查找树。我们描述一颗B树时需要指定它的阶数，阶数表示了一个结点最多有多少个孩子结点，一般用字母m表示阶数。当m取2时，就是我们常见的二叉搜索树，m为3时是2-3树。


**一颗m阶的B树定义如下：**

> （1）每个结点最多有m-1个关键字。
> （2）根结点最少可以只有1个关键字。
> （3）非根结点至少有Math.ceil(m/2)-1个关键字。Math.ceil(m/2)含义是向上取整。例如Math.ceil(4.5) = 5。
> （4）每个结点中的关键字都按照从小到大的顺序排列，每个关键字的左子树中的所有关键字都小于它，而右子树中的所有关键字都大于它。
> （5）所有叶子结点都位于同一层，或者说根结点到每个叶子结点的长度都相同。

### 3 查找

B-树的查找其实是对二叉搜索树查找的扩展， 与二叉搜索树不同的地方是，B-树中每个节点有不止一棵子树。在B-树中查找某个结点时，需要先判断要查找的结点在哪棵子树上，然后在结点中逐个查找目标结点。B树的查找过程相对简单，与二叉搜索树类似，因此不再赘述。

### 4 插入

B树的插入操作是指在树种插入一条新记录，即（key, value）的键值对。如果B树中已存在需要插入的键值对，则用需要插入的value替换旧的value。若B树不存在这个key，则一定是在叶子结点中进行插入操作。

#### 4.1 插入流程

**B树的插入流程如下：**
  （1）根据要插入的key的值，对B树执行查找操作，查找到待插入数据的当前节点位置。
  （2）判断当前结点key的个数是否小于等于m-1，若满足，则结束直接插入数据，否则，进行第（3）步。
  （3）以结点中间的key为中心分裂成左右两部分，然后将这个中间的key插入到父结点中，这个key的左子树指向分裂后的左半部分，这个key的右子支指向分裂后的右半部分，然后将当前结点指向父结点，继续进行第（3）步。

#### 4.2 实例图解

下面以5阶B树为例，介绍B树的插入操作，在5阶B树中，结点最多有4个key,最少有2个key。

**插入图解：**
  **1：插入38，此时为空树，直接插入，并作为根节点。继续插入22、76、40，符合情形（2），直接插入。继续插入51，符合情形（3），执行分裂。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057669-b5353afb45b0985.jpg)img


  **2：按照相同的步骤继续插入13、21。插入39，符合情形（3），导致节点分裂。选择中值22作为父节点，并将22节点上移，与40节点进行合并。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057669-a1c2cac25e0dcee.jpg)img


  **3：按照同样的插入规则，继续向树中插入key为30、27、33、36、35、34、24、29的数据。插入完成后，继续插入key为26的数据，插入之后需要执行节点分裂。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057669-b22a0900e6b4167.jpg)img


  **4：将key为27的数据节点上移至父节点，此时父节点已经有4个key，插入key27的数据后需要执行节点分裂。在插入key为26的数据后，导致根节点发生分裂，树的高度加1。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057670-d6983c5c937cca1.jpg)img

#### 4.3 性能分析

B树插入过程首先需要执行一次查找操作，B树的查找操作的时间复杂度为O(mlogmn)。其中m为B树的阶数，n为B树中key的数目。在插入过程，最耗时的情形即为：插入数据后导致根节点发生分裂，分裂节点的操作是常数级，分裂操作向上回溯的时间复杂度为O(h)。因此，B树的插入操作的时间复杂度近似于查找操作，即O(mlogmn)。

### 5 删除

#### 5.1 删除流程

**B树的删除流程如下：**
  （1）如果当前需要删除的key位于非叶子结点上，则用后继key（这里的后继key均指后继记录的意思）覆盖要删除的key，然后在后继key所在的子支中删除该后继key。此时后继key一定位于叶子结点上，这个过程和二叉搜索树删除结点的方式类似。删除这个记录后执行第2步
  （2）该结点key个数大于等于Math.ceil(m/2)-1，结束删除操作，否则执行第（3）步。
  （3）如果兄弟结点key个数大于Math.ceil(m/2)-1，则父结点中的key下移到该结点，兄弟结点中的一个key上移，删除操作结束。否则，将父结点中的key下移与当前结点及它的兄弟结点中的key合并，形成一个新的结点。原父结点中的key的两个孩子指针就变成了一个孩子指针，指向这个新结点。然后当前结点的指针指向父结点，重复第（2）步。

#### 5.2 实例图解

**删除图解：**
  **1：首先删除21，符合情形（2）直接删除。删除21后，继续删除27，符合情形（1），使用后继节点28替代27，并删除28。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057670-934c75cb2d07730.jpg)img


  **2：删除28后，当前节点只有一个key，因此需要按照情形（3）调整。当前节点的兄弟节点有3个key，父节点中key28下移，兄弟节点中key26上移，调整结束。调整完毕后继续删除32。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057671-2b7a20deb473d59.jpg)img


  **3：删除32后，需要按照情形（3）进行调整，当前节点的兄弟节点只有2个key，则将父节点下移，将当前节点与一个兄弟节点合并，调整完毕。继续删除39，删除39后按照情形（3）进行调整。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057671-6fcc402e13ed71b.jpg)img


  **4：当前节点变为只含有key40的节点，需要按照情形（3）继续调整，执行节点的合并，合并操作中包含根节点，导致合并之后的树的高度减1。**

![心里没点 B 树。。。](http://www.cxyxiaowu.com/wp-content/uploads/2019/10/1571057672-08ae588f527ea3f.jpg)img

#### 5.3 性能分析

B树的删除操作同样需要执行查找过程，时间复杂度为O(mlogmn)。删除数据过程与插入过程类似，最坏情况需要回溯O(h)。因此B树的删除操作的时间复杂度近似为O(mlogmn)。

### 6 总结

**B树是一种平衡的多路查找树。**其设计思路主要是通过节点中存储不止一个key，来降低树的高度。同等比较次数下，树的高度小保证磁盘IO次数相对较少，提高查找效率。