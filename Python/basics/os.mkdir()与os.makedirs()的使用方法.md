# os.mkdir()与os.makedirs()的使用方法



## os.mkdir() 函数

语法格式：
os.mkdir(path, mode=0o777, *, dir_fd=None)

使用数字模式mode创建名为path的目录。如果目录已经存在，则抛出异常FileExistsError。
在一些系统中，可以忽略mode，在一些平台可以通过调用chmod()来指定它们。mode就是所创建目录对应的文件及文件夹的读写权限，可以参考Linux命令下的chmod命令来理解。
*，dir_fd=None为命名关键字参数，具体作用不详。

## os.makedirs()函数

语法格式：
os.makedirs(name, mode=0o777, exist_ok=False)

递归目录创建函数，和mkdir()很像，但是所有中间级目录都要包含叶目录。
递归创建目录，题中应有之意即路径中哪一层不存在，则自动创建。
区别于os.mkdir(path, mode=0o777, *, dir_fd=None)，只创建最后一层的目录。
name:所要创建的目录
参数mode传递给mkdir()；
如果exist_ok是False（默认），当目标目录（即要创建的目录）已经存在，会抛出一个OSError