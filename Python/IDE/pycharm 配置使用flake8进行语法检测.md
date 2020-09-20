# pycharm 配置使用flake8进行语法检测



- 安装flake8

```
pip install flake8
1
```

- 配置pycharm 支持flake8
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190221140533585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NyYXp5X3poYW5nY29uZw==,size_16,color_FFFFFF,t_70)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190221140614274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NyYXp5X3poYW5nY29uZw==,size_16,color_FFFFFF,t_70)

```
Program:    $PyInterpreterDirectory$/python
Arguments:		-m flake8 --max-line-length=130 --exclude venv,migrations $ProjectFileDir$
Working directory: $ProjectFileDir$

说明：
	$PyInterpreterDirectory$		当前项目使用的python环境bin目录
	--exclude 可以过滤掉不检测的目录或文件
	$ProjectFileDir$	当前项目所在目录
	
123456789
```

- 使用flake8进行代码检测
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190221142505213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NyYXp5X3poYW5nY29uZw==,size_16,color_FFFFFF,t_70)