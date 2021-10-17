------

# git 显示中文和解决中文乱码



## 解决git status不能显示中文

**现象：**

`git status`查看有改动但未提交的文件时总只显示数字串，显示不出中文文件名，非常不方便。如下图：

![](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/v2-bc86ea90ae02e6b27713420b3d52bc3e_1440w.png)



**原因：**

在默认设置下，中文文件名在工作区状态输出，中文名不能正确显示，而是显示为八进制的字符编码。

**解决办法：**

将git配置文件 `core.quotepath`项设置为`false`。`quotepath`表示引用路径，加上`--global`表示全局配置

`git bash`终端输入命令：

```text
git config --global core.quotepath false
```

## 解决git bash 终端显示中文乱码

要注意的是，这样设置后，你的`git bash`终端也要设置成中文和`utf-8`编码。才能正确显示中文.

在`git bash`的界面中右击空白处，弹出菜单，选择`选项->文本->本地Locale`，设置为`zh_CN`，而旁边的字符集选框选为`UTF-8`。

英文显示则是：
`Options->Text->Locale`改为`zh_CN`，`Character set`改为`UTF-8`

如图：

![](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/v2-735f07fdbb0da3b73ac3678815026761_1440w.png)



## 通过修改配置文件来解决中文乱码

如果你的git bash终端没有菜单选项显示，还可以通过直接修改配置文件的方式来解决中文乱码问题。

进入git的安装目录

编辑`etc\gitconfig`文件，也有些windows系统是存放在`C:\Users\Administrator\.gitconfig`路径或`安装盘符:\Git\mingw64\etc\gitconfig`，在文件末尾增加以下内容：

```text
[gui]  
    encoding = utf-8  
    # 代码库统一使用utf-8  
[i18n]  
    commitencoding = utf-8  
    # log编码  
[svn]  
    pathnameencoding = utf-8  
    # 支持中文路径  
[core]
    quotepath = false 
    # status引用路径不再是八进制（反过来说就是允许显示中文了）
```

编辑`etc\git-completion.bash`文件,在文件末尾增加以下内容：

```text
# 让ls命令能够正常显示中文
alias ls='ls --show-control-chars --color=auto' 
```

编辑`etc\inputrc`文件，修改`output-meta`和`convert-meta`属性值：

```text
set output-meta on  # bash可以正常输入中文  
set convert-meta off  
```

编辑`profile`文件，在文件末尾添加如下内容：

```text
export LESSHARESET=utf-8
```

------