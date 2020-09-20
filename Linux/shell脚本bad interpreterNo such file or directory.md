# 执行shell脚本时提示bad interpreter:No such file or directory的解决办法



故障现象：在终端直接cd /var正常，在shell脚本中执行则报错。原因是脚本是在windows平台下写的，换行符与linux不同，造成脚本不能正确执行

出现bad interpreter:No such file or directory（没有那个文件或目录）的原因，是文件格式的问题。这个文件是在Windows下编写的。换行的方式与Unix不一样，但是在vim下面如果不Set一下又完全看不出来。



问题分析：
1、将windows 下编写好的SHELL文件，传到linux下执行，提示出错。
2、出错信息：bad interpreter: 没有那个文件或目录。

问题原因：
因为操作系统是windows，在windows下编辑的脚本，所以有可能有不可见字符。脚本文件是DOS格式的
即每一行的行尾以\r\n来标识, 其ASCII码分别是0x0D, 0x0A.

解决方法：
可以有很多种办法看这个文件是DOS格式的还是UNIX格式的, 还是MAC格式的
（1） vim filename
然后用命令 :set ff
可看到dos或unix的字样，如果的确是dos格式的, 那么用set ff=unix把它强制为unix格式的,，然后存盘退出后就可运行。

（set ff=unix : 告诉 vi 编辑器，使用unix换行符，个人使用以上方法解决，简单方便，推荐此方式）


\###############分割线##############

转换不同平台的文本文件格式可以用
\1. unix2dos或dos2unix这两个小程序来做. 很简单. 在djgpp中这两个程序的名字叫dtou和utod, u代表unix, d代表dos
\2. 也可以用sed 这样的工具来做:

复制代码代码如下:

sed ‘s/^M//' filename > tmp_filename
mv -f tmp_filename filename


特别说明：^M并不是按键shift + 6产生的^和字母M, 它是一个字符, 其ASCII是0x0D, 生成它的办法是先按CTRL+V, 然后再回车(或CTRL+M)

另外, 当SHELL程序报告command not found时, 总是去检查一下你的PATH里面有没有程序要用到的每一个命令(没指定绝对路径的那种). 你这么小的程序, 可以一行一行核对。



附：少写一个/引发的没有那个文件或目录问题

  今天在翻看以前写的简单的shell脚本时,发现一个问题:

  当./运行时总是提示: (bash: ./hello.sh: bin/bash: 坏的解释器: 没有那个文件或目录),但是当用sh运行时正确.

  原来的脚本:

  (试试看你能否一眼看出错误)

复制代码代码如下:  \#!bin/bash

  echo "Hello Linux!"

  后来几番检查发现自己写的丢了一些东西.

  应该把第一行改成 #!/bin/bash ，少写了一个/