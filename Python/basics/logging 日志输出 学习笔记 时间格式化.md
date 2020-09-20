# python logging 日志输出 学习笔记 时间格式化



# **一、logging介绍**

​    Logging是python自带的模块，这个模块支持输出不同级别的日志，可以输出到控制台和写入文件，支持TCP、HTTP、GET/POST、SMTP、Socket等协议，将日志信息发送到网络等等。



​    Logging提供5个等级的输出，CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET，如果把looger的级别设置为INFO， 那么小于INFO级别的日志都不输出， 大于等于INFO级别的日志都输出



​    logging库提供了多个组件：Logger、Handler、Filter、Formatter：

​        Logger    对象提供应用程序可直接使用的接口，供应用代码使用；
​        Handler   发送日志到适当的目的地；
​        Filter      提供了过滤日志信息的方法，控制输出； 
​        Formatter  指定日志输出和显示的具体格式。



# **二、Logging输出日志到控制台，同时写入文件**

```python
#coding:utf-8
# =======================================================================
# FuncName: console_out.py
# Desc: output log to console and file
# Date: 2016-02-19 17:32
# Author: johnny
# =======================================================================
import logging

def console_out(logFilename):
    ''' Output log to file and console '''
    # Define a Handler and set a format which output to file
    logging.basicConfig(
                    level    = logging.DEBUG,              # 定义输出到文件的log级别，             
                    format   = '%(asctime)s  %(filename)s : %(levelname)s  %(message)s',    # 定义输出log的格式
                    datefmt  = '%Y-%m-%d %A %H:%M:%S',                                     # 时间
                    filename = logFilename,                # log文件名
                    filemode = 'w')                        # 写入模式“w”或“a”
    # Define a Handler and set a format which output to console
    console = logging.StreamHandler()                  # 定义console handler
    console.setLevel(logging.INFO)                     # 定义该handler级别
    formatter = logging.Formatter('%(asctime)s  %(filename)s : %(levelname)s  %(message)s')  #定义该handler格式
    console.setFormatter(formatter)

    # Create an instance
    logging.getLogger().addHandler(console)           # 实例化添加handler
        
    # Print information              # 输出日志级别
    logging.debug('logger debug message')     
    logging.info('logger info message')
    logging.warning('logger warning message')
    logging.error('logger error message')
    logging.critical('logger critical message')
if __name__ == "__main__":
    console_out('logging.log')
```


控制台：

```python
2016-03-06 14:38:49,714  console_out.py : INFO  logger info message
2016-03-06 14:38:49,714  console_out.py : WARNING  logger warning message
2016-03-06 14:38:49,714  console_out.py : ERROR  logger error message
2016-03-06 14:38:49,714  console_out.py : CRITICAL  logger critical message
```



文件： logging.log

```python
2016-03-06 Sunday 14:38:49  console_out.py : DEBUG  logger debug message
2016-03-06 Sunday 14:38:49  console_out.py : INFO  logger info message
2016-03-06 Sunday 14:38:49  console_out.py : WARNING  logger warning message
2016-03-06 Sunday 14:38:49  console_out.py : ERROR  logger error message
2016-03-06 Sunday 14:38:49  console_out.py : CRITICAL  logger critical message
```

logging.basicConfig()函数中的具体参数：
filename：  指定的文件名创建FiledHandler，这样日志会被存储在指定的文件中；
filemode：  文件打开方式，在指定了filename时使用这个参数，默认值为“w”还可指定为“a”；
format：    指定handler使用的日志显示格式；
datefmt：   指定日期时间格式。，格式参考strftime时间格式化（下文）
level：     设置rootlogger的日志级别
stream：   用指定的stream创建StreamHandler。可以指定输出到sys.stderr,sys.stdout或者文件，默认为sys.stderr。
         若同时列出了filename和stream两个参数，则stream参数会被忽略。

format参数中可能用到的格式化信息：

| %(name)s            | Logger的名字                                                 |
| ------------------- | ------------------------------------------------------------ |
| %(levelno)s         | 数字形式的日志级别                                           |
| %(levelname)s       | 文本形式的日志级别                                           |
| %(pathname)s        | 调用日志输出函数的模块的完整路径名，可能没有                 |
| %(filename)s        | 调用日志输出函数的模块的文件名                               |
| %(module)s          | 调用日志输出函数的模块名                                     |
| %(funcName)s        | 调用日志输出函数的函数名                                     |
| %(lineno)d          | 调用日志输出函数的语句所在的代码行                           |
| %(created)f         | 当前时间，用UNIX标准的表示时间的浮 点数表示                  |
| %(relativeCreated)d | 输出日志信息时的，自Logger创建以 来的毫秒数                  |
| %(asctime)s         | 字符串形式的当前时间。默认格式是 “2003-07-08 16:49:45,896”。逗号后面的是毫秒 |
| %(thread)d          | 线程ID。可能没有                                             |
| %(threadName)s      | 线程名。可能没有                                             |
| %(process)d         | 进程ID。可能没有                                             |
| %(message)s         | 用户输出的消息                                               |



python中时间日期格式化符号：
%y 两位数的年份表示（00-99）
%Y 四位数的年份表示（000-9999）
%m 月份（01-12）
%d 月内中的一天（0-31）
%H 24小时制小时数（0-23）
%I 12小时制小时数（01-12）
%M 分钟数（00=59）
%S 秒（00-59）
%a 本地简化星期名称
%A 本地完整星期名称
%b 本地简化的月份名称
%B 本地完整的月份名称
%c 本地相应的日期表示和时间表示
%j 年内的一天（001-366）
%p 本地A.M.或P.M.的等价符
%U 一年中的星期数（00-53）星期天为星期的开始
%w 星期（0-6），星期天为星期的开始
%W 一年中的星期数（00-53）星期一为星期的开始
%x 本地相应的日期表示
%X 本地相应的时间表示
%Z 当前时区的名称
%% %号本身



**计算毫秒**

```python
#!/usr/bin/env python
#coding:utf-8
import  datetime,time

print time.strftime('%Y-%m-%d  %H:%M:%S'),datetime.datetime.now().strftime('%f')[:3]
print datetime.datetime.now().strftime('%Y%m%d %H:%M:%S.%f')
```



# **三、子文件日志汇总到主文件**

​    将多个py文件的logging内容都输出到同一个主文件的log文件中，并且格式都是设置好的，统一的只要主文件设置好logging.basicConfig相关参数，配置好StreamHandler等内容后，子文件，只需要导入logging模块，然后调用logging.info，logging.debug函数，即可实现将log信息，都输入到主文件的log中了。

想进一步了解请移步：[ python logging 替代print 输出内容到控制台和重定向到文件](http://blog.csdn.net/z_johnny/article/details/50740528)