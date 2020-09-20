# pywin32 ImportError: DLL load failed: 找不到指定的模块



最近安装TPOT的时候，发现安装能成功，然而import tpot总是失败。百度上关于TPOT的安装资料也少的可怜，在各种鼓捣下，终于解决了这个问题。

问题描述如下：

```
>>>import tpot
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "D:\python\lib\site-packages\tpot\__init__.py", line 23, in <module>
    from .tpot import TPOTClassifier, TPOTRegressor
  File "D:\python\lib\site-packages\tpot\tpot.py", line 22, in <module>
    from .base import TPOTBase
  File "D:\python\lib\site-packages\tpot\base.py", line 64, in <module>
    import win32api
ImportError: DLL load failed: 找不到指定的模块。12345678910
```

**原因是pywin32的dll需要放到C:\Windows\System32目录下，
我的python默认目录为：D:\python\Lib\site-packages\pywin32_system32
该文件夹下有两个dll，“pythoncom35.dll” 和“pywintypes35.dll”，复制到C:\Windows\System32目录。**

这样系统便可找到该模块

```
Python 3.5.2 (v3.5.2:4def2a2901a5, Jun 25 2016, 22:18:55) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import tpot
>>>1234
```

上述的前提是把TPOT的依赖完全安装好，出现的问题。
TPOT官网：http://rhiever.github.io/tpot/installing/