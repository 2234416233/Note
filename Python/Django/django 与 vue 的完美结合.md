# django 与 vue 的完美结合



用django后端，前段用vue，做一个普通的简单系统，

首先是创建一个django项目



```
 django-admin startproject mysite   # 创建mysite项目
 django-admin startapp blog   # 创建blog应用
```

一、接下来就是安装关于vue 的东西了

1、首先安装node.js，官网地址：https://nodejs.org/zh-cn/download/

2、使用npm淘宝镜像，避免npm下载速度过慢的问题  npm install -g cnpm --registry=https://registry.npm.taobao.org 

3、使用cnpm 下载vue-cli    cnmp install -g cue-cli

二、在django项目中创建vue项目

首先，进去django项目的项目目录中，执行：

vue-init webpack firstvue## firstvue为前端项目的名称，可自定义。创建的项目会跟django中的app一样的目录登记。类似一个前端app一样。

mysite 文件夹 blog 文件夹  和 firstvue文件夹 要在同一目录级别

在创建时，会弹出很多选择项，根据自己需求自定义修改。也可以全部回车，使用默认的。这里我就直接全部回车。

三、编写vue前端项目，直接编写就是，调试则可以执行。也可先不编写，跳过这一步



1. cd firstvue## 进入到上一部创建的firstvue项目中 
2. cnpm install    ## 安装需要的依赖模块 
3. cnpm run dev    ## 运行调式的服务，会启动一个web服务，访问localhost:8080 即可调式 

四、vue项目写完后，打包vue项目，然后修改django配置，将vue集成到django中



1. cnpm run build    ## 打包vue项目，会将所有东西打包成一个dist文件夹 

五、接下来就是配置django中的setting文件了：

![img](https://img-blog.csdn.net/20180525144757231?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5fX3ll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

六、修改django的主目录的urls文件：

![img](https://img-blog.csdn.net/20180525145022450?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5fX3ll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

七、启动django服务，访问localhost:8000 则可以出现vue的首页。

python manage.py runserver 

![img](https://img-blog.csdn.net/20180525145242332?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1YW5fX3ll/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)