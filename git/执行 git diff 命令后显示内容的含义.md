# 执行 git diff 命令后显示内容的含义

在Git 中执行 git diff 命令后会显示出修改前后两个文件的区别。如下图：

- ![](https://gcore.jsdelivr.net/gh/2234416233/myImage/img/20190122092441741.png)


```ruby
$  diff --git a/README.md b/README.md    
该行显示git 版本的diff 下两个文件的对比。a版本（修改前）的文件 README.md ，
和 b版本（修改后）文件 README.md  

$  index 9e5671b..2e6b95b 100644  
index后面两个数字表示两个文件的hash值(index区域的9e5671b对象与工作区域的2e6b95b对象对比)
最后面的数字表示文件的属性，权限（文件权限为644） 

$  --- a/README.md
$  +++ b/README.md
---表示修改前，+++表示修改后。 

$  @@ -4,3 +4,5 @@
该行表示接下来，下面显示的内容所在位置。
-表示修改前，+表示修改后；-4,3 表示修改前的README.md文件，从第4行开始显示，一直到第6行（上面的4为起始行，3为向后偏移的行数。即显示修改前该文件第4至第6行的内容）。
+4,5 则表示接下来要显示的内容为修改后的README.md文件，从第4行开始显示，一直到第8行（从第4行开始，延续到向后偏移5行）。

$     fix-B
$  
$     feature-c
$  + 
$  +  This change is done in feature-D
前3行为修改前的内容，显示为白色字体。上面一共5行，为修改后的内容；最后的带 + 号的内容为修改后所增加的内容（以绿色字体显示，表示与修改前内容有区别的部分）。 

修改前、后的README.md文件，内容如下图：
```



<center class="half">
    <img src="https://cdn.net/gh/2234416233/myImage/img/20190122100420330.png" width="400"/>
    <img src="https://gcore.jsdelivr.net/gh/2234416233/myImage/img/20190122100206207.png" width="420"/>
</center>






