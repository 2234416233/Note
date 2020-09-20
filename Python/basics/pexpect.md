# pexpect(4.6.0)文档



## 说明

文档参考(翻译)自官方文档([地址](https://pexpect.readthedocs.io/en/stable/index.html)),加入了一些我在使用过程中的心得体会。简略了我认为不重要的内容。简略了我不懂的内容。水平有限，仅供参考。

## API结构

- Core pexpect components(核心组件)
  - spawn class
  - run function
  - Exceptions
- Utility functions(小工具)
- fdpexpect - use pexpect with a file descriptor
  - fdspawn class
- popen_spawn - use pexpect with a piped subprocess
  - PopenSpawn class
- replwrap - Control read-eval-print-loops
- pxssh - control an SSH session
  - pxssh class

## 核心组件

pexpect有两个主要接口：函数run()和类spawn。类spawn更强大。run()比spawn类简单，适合快速调用程序。当调用run()时，它执行给定的程序，然后返回输出。这是对os.system（）的一个方便的替换。
例如:

```java
pexpect.run（'ls-la')
1
```

spawn类是到pexpect系统的更强大的接口。您可以使用它来产生一个子程序，然后通过发送输入和期望响应（等待子程序输出中的模式）与它进行交互。

```java
child = pexpect.spawn('scp foo user@example.com:.')
child.expect('Password:')//会阻塞，直到匹配到指定内容。
child.sendline(mypassword)
123
```

### 创建Spawn

构造方法

```java
class pexpect.spawn(command, args=[], timeout=30, maxread=2000, searchwindowsize=None, logfile=None, cwd=None, env=None, ignore_sighup=False, echo=True, preexec_fn=None, encoding=None, codec_errors='strict', dimensions=None, use_poll=False)
1
```

创建spawn举例:

```java
child = pexpect.spawn('/usr/bin/ftp')
child = pexpect.spawn('/usr/bin/ssh user@example.com')
child = pexpect.spawn('ls -latr /tmp')
123
```

也可以这样:

```java
child = pexpect.spawn('/usr/bin/ftp', [])
child = pexpect.spawn('/usr/bin/ssh', ['user@example.com'])
child = pexpect.spawn('ls', ['-latr', '/tmp'])
123
```

`args`
`args=[]` 是命令的参数。也可以直接写在命令里面

注意，pexpect不解释shell元字符，例如重定向、管道或通配符（>、或*）。这是一个常见的错误。如果想运行一个命令并通过管道通过发送给另一个命令，那么还必须启动一个shell。例如：

```java
child = pexpect.spawn('/bin/bash -c "ls -l | grep LOG > logs.txt"')
child.expect(pexpect.EOF)
12
```

第二种形式的spawn（传递参数列表`args`）可以使语法更加清晰。例如，以下内容与前一个示例等效：

```java
shell_cmd = 'ls -l | grep LOG > logs.txt'
child = pexpect.spawn('/bin/bash', ['-c', shell_cmd])
child.expect(pexpect.EOF)
123
```

`maxread`
`maxread`属性设置读取缓冲区大小。这是pexpect一次尝试从tty读取的最大字节数。将maxread大小设置为1将关闭缓冲。与java等语言类似，缓冲功能可以提高io性能，此功能与SearchWindowsize结合使用非常有用。
`searchwindowsize`
当关键字参数searchwindowsize为none（默认值）时，将在每次接收传入数据的迭代中搜索整个缓冲区。这个字段可以提高搜索性能。

`timeout`
单位是second。当关键字参数timeout被指定为数字（默认值：30）时，对于expect()方法，经过timeout还未匹配到指定内容，则引发超时。

`logfile`
logfile可以支持日志记录。所有输入和输出都将复制到logfile中。将logfile设置为none则不记录(默认设置)。将logfile设置为sys.stdout以将所有内容回送到标准输出。每次写入后都会刷新logfile。
举例:

```java
child = pexpect.spawn('some_command')
//默认的输入输出是二进制内容
fout = open('mylog.txt','wb')
child.logfile = fout
1234
```

输出到控制台:

```java
child = pexpect.spawn('some_command', encoding='utf-8')
//默认的输入输出是二进制内容
child.logfile = sys.stdout
123
```

还可以分别指定输入和输出日志文件。例如：

```java
child = pexpect.spawn('some_command',encoding='utf-8)
child.logfile_read = sys.stdout
child.logfile_send = fout
123
```

`ignore_sighup`
如果ignore_sighup为真，子进程将忽略sighup信号(挂断信号)。从pexpect 4.0中默认值为false，这意味着sighup将由子进程正常处理。
`delaybeforesend`
delaybeforesend有助于克服许多用户遇到的奇怪行为。典型的问题是，用户希望出现“password:”提示，然后立即调用sendline()发送密码。然后用户会看到他们的密码被回传给他们。密码通常不会回显。问题是由于大多数应用程序都会打印出“密码”提示，然后关闭stdin echo，但如果在应用程序关闭echo之前发送密码，则会得到回显的密码。通常情况下，用真正的键盘与人交流不会有问题。如果你在写作前稍微拖延一下，那么这似乎可以解决问题。对于许多用户来说，这是一个很常见的问题，所以我决定默认的pexpect行为应该是在写入子应用程序之前就睡眠。1/20秒（50毫秒）似乎足以解决问题。可以将delaybeforesend设置为none以返回旧行为。

如果要获取child的退出状态，必须调用close（）方法。子级的退出或信号状态将存储在self.exit status或self.signal status中。如果子项正常退出，则exitstatus将存储退出返回代码，信号状态将为无。如果用信号异常终止子级，则信号状态将存储信号值，并且退出状态将为无：

```java
child = pexpect.spawn('some_command')
child.close()
print(child.exitstatus, child.signalstatus)
123
```

如果需要更多详细信息，还可以读取self.status成员，该成员存储os.waitpid返回的状态。可以参考os.wifexited/os.wexitstatus或os.wifsignaled/os.termsig。
`echo`
echo属性可以设置为false以禁用输入的echo。作为一个伪终端，所有由“键盘”（send（）或sendline（））回送的输入都将重复输出。在许多情况下，不希望启用echo，稍后可以使用setecho（false）和waitnoecho（）禁用它。但是，对于某些平台（如Solaris），这是不可能的，应该在生成时立即禁用。当你要使用bash提示字符串"user@hostname: /home/$"来匹配输出结果时，echo=False是必须的。
`prexec_fn`
如果给定了prexec_fn，则在启动给定命令之前，将在子进程中调用它。这对重置继承的信号处理程序很有用。
`dimensions`
dimensions属性指定子进程所看到的伪终端的大小，并指定为两条目的元组（行、列）。如果未指定，将应用ptyprocess中的默认值。
`use_poll`
use_poll属性允许在select.select（）上使用select.poll（）进行套接字处理。如果系统的FDS大于1024，这很方便.

### 方法

[重要]`expect(patterns, timeout=-1, searchwindowsize=-1, async_=False, **kw)`
在stream中查找，直到匹配到给定paterns。patern可以是string、EOF、正则表达式或任何这些类型的列表。返回值是匹配到的paterns元素的index。如果paterns不是列表，则在成功匹配时返回index=0。该方法可能会引发EOF或timeout。为了避免EOF或timeout，请将EOF或timeout添加到patterns中,这将导致expect与EOF或超时条件匹配，而不是引发异常。

```java
the input is 'foobar'
index = p.expect(['bar', 'foo', 'foobar'])
returns 1('foo') even though 'foobar' is a "better" match
123
```

请注意结果**不是**一定如此:

```java
the input is 'foobar'
index = p.expect(['foobar', 'foo'])
returns 0('foobar') if all input is available at once,
but returns 1('foo') if parts of the final 'bar' arrive late
1234
```

为了避免异常可以将EOF和超时标志加入patern列表 :

```java
index = p.expect(['good', 'bad', pexpect.EOF, pexpect.TIMEOUT])
if index == 0:
    do_something()
elif index == 1:
    do_something_else()
elif index == 2:
    do_some_other_thing()
elif index == 3:
    do_something_completely_different()
instead of code like this:

try:
    index = p.expect(['good', 'bad'])
    if index == 0:
        do_something()
    elif index == 1:
        do_something_else()
except EOF:
    do_some_other_thing()
except TIMEOUT:
    do_something_completely_different()
123456789101112131415161718192021
```

如果只是等待命令则可以:

```java
//请注意有些命令完成时没有EOF
p = pexpect.spawn('/bin/ls')
p.expect(pexpect.EOF)
print(p.before)
1234
```

如果试图优化速度，请参阅`expect_list()`

在安装了Asyncio的python 3.4或python 3.3上，传递async_=true将使此返回一个asyncio coroutine，您可以从中获得与此方法通常直接给出的结果相同的结果。因此，在协程中，可以替换以下代码：
index = p.expect(patterns)
使用此非阻塞代码：
index = yield from p.expect(patterns,async_=True)

`expect_exact(pattern_list, timeout=-1, searchwindowsize=-1, async_=False, **kw)`
这与expect()类似，但在“pattern_list”中使用纯字符串匹配而不是编译的正则表达式。“模式列表”可以是字符串、列表或其他字符串序列，也可以是超时和EOF。
此调用可能比expect()快，原因有两个：字符串搜索比重新匹配快，并且可以将搜索限制在输入缓冲区的末尾。

`expect_list（pattern_list，timeout=-1，searchwindowsize=-1，async_u=false，**kw)`
这将获取已编译正则表达式的列表，并将索引返回到与子输出匹配的模式列表中。该列表还可以包含eof或timeout（不是编译的正则表达式）。此方法与expect（）方法类似，只是expect_list（）不会在每次调用时重新编译模式列表。如果您试图优化速度，这可能会有所帮助，否则只需使用expect（）方法。

`compile_pattern_list(patterns)`
expect()方法会首先编译匹配字符串，单独抽出一个方法，可以在循环之前主动调用此方法，提高程序性能。

`send(s)`
发送字符串到子进程。返回写入的字节数。如果指定了log_file，则会同时写入该文件。
如果s过大(超出linux默认设置)，则需要发送相关的设置命令，可参考官方文档的send(s)方法说明。
[重要]`sendline(s='')`
包装send()，将字符串s发送到子进程，并自动附加os.linesep(换行符)。返回写入的字节数。在默认终端模式下，每行只能发送有限的字节数
`write(s)`
同send(),只是没有返回值
`writelines(sequence)`
为序列中的每个元素调用write（）。序列可以是生成字符串的任何可遍历对象，通常是字符串列表。不会添加换行符。没有返回值
[重要]`sendcontrol(char)`
发送“Ctrl-C”：sendcontrol(‘c’)
类似的方法: sendintr()（发送sigint到子进程） and sendeof()(发送EOF到子进程)

`read(size=-1)`
这将从文件中读取最多size个字节。如果SIZE参数为负数或省略，则读取所有数据，直到达到EOF。字节作为字符串对象返回。直接遇到EOF时返回空字符串。
`readline(size=-1)`
这将读取并返回一整行。行尾的换行符作为字符串的一部分返回，除非文件结尾没有换行符。如果立即遇到EOF，则返回空字符串。
如果size参数为0，则返回空字符串，其他情况size不起作用.
这个方法可用于查看输出结果。如果一行结果都没有，会阻塞。

> 注意如果没有行尾部则会阻塞。例如要求用户输入密码,则没有行尾部，会一直阻塞。

[不要使用]`readlines()`
pexpect提供了readlines()但文档没有介绍，我尝试了一下，不要使用readlines()，它的作用是读取所有的输出行，问题是如果子进程还没有关闭，它肯定会造成阻塞。
`read_nonblocking(size=1, timeout=-1)`
timeout是指每次读取一个字节的超时设置。这是非阻塞读取。
`eof()`
如果曾经引发过EOF异常，则返回true
`interact(escape_character='\x1d', input_filter=None, output_filter=None)`
这将子进程的控制权交给交互用户（键盘上的人）。键盘按键事件将发送到子进程，并打印子进程的stdout和stderr输出。转义字符不发送.

[重要]`最后请注意`
spawn类以字节(binary)模式生成时，日志文件必须以binary模式打开。在Unicode模式下，它们应该打开以写入Unicode文本。请参见https://pexpect.readthedocs.io/en/stable/api/pexpect.html#unicode

## Controlling the child process

这里是与child process状态与控制相关的方法。
class pexpect.spawn
[不要使用]`kill(sig)`
这个接口实际上是发送sig(信号)到子进程。名字具有误导性。
`terminate(force=False)`
这是真正的终止子进程。成功终止会返回True
`isalive()`
子进程是否正在运行
`wait()`
This waits until the child exits。bolcking call。这个方法实际的意思是等待子进程退出。
`close(force=True)`
关闭与子应用程序的连接
`getwinsize()`
返回子tty的终端窗口大小。返回值是（行、列）的元组
`setwinsize(rows, cols)`
设置子tty的终端窗口大小
`setecho(state)`
打开或关闭(服务器上)终端的echo mode。设置后会使input buffer内容丢失.
以下程序将能够按照预期运行：

```java
p = pexpect.spawn('cat') # Echo is on by default.
p.sendline('1234') # We expect see this twice from the child...
p.expect(['1234']) # ... once from the tty echo...
p.expect(['1234']) # ... and again from cat itself.
p.setecho(False) # Turn off tty echo
p.sendline('abcd') # We will set this only once (echoed by cat).
p.sendline('wxyz') # We will set this only once (echoed by cat)
p.expect(['abcd'])
p.expect(['wxyz'])
123456789
```

由于在setecho之前发送的行将丢失，因此以下内容将不起作用：

```java
p = pexpect.spawn('cat')
p.sendline('1234')
p.setecho(False) # Turn off tty echo
p.sendline('abcd') # We will set this only once (echoed by cat).
p.sendline('wxyz') # We will set this only once (echoed by cat)
p.expect(['1234'])
p.expect(['1234'])
p.expect(['abcd'])
p.expect(['wxyz'])
123456789
```

`waitnoecho(timeout=-1)`
可用于输入密码的场合,如:

```java
p = pexpect.spawn('ssh user@example.com')
p.waitnoecho()
p.sendline(mypassword)
123
```

`pid`
子进程的pid
`child_fd`
The file descriptor used to communicate with the child process.

[重要]`Handling unicode`
默认情况下，spawn是一个字节接口：它的read方法返回二进制字节，它的write/send和expect方法也适用二进制字节。如果将encoding参数传递给构造函数，它可以使用Unicode编码。在这种模式下，expect()和expect_exact（）的模式也应该是unicode。
在4.0版本中更改：spawn提供字节和unicode接口。在pexpect 3.x中，unicode接口由单独的spawu类提供。
为了向后兼容，在字节模式下允许使用某些Unicode:发送方法在将任意Unicode发送到子进程之前将其编码为UTF-8，并且其Expect方法只能接受ASCII Unicode字符串。
注释
使用pexpect的Unicode处理在python 2和3上的工作方式相同，尽管名称不同。即。：
bytes模式在python 2上与str一起使用，在python 3上与bytes一起使用，
unicode模式在python 2上使用unicode，在python 3上使用str。

## run function

run()是spawn的简化版本.方法字段的意义同spawn是相同的。
`pexpect.run(command, timeout=30, withexitstatus=False, events=None, extra_args=None, logfile=None, cwd=None, env=None, **kwargs)`
运行给定的命令；等待它完成；然后以字符串形式返回所有输出。输出中包含stderr。如果未给出命令的完整路径，则搜索该路径。
run()是spawn的简化版本，例如:

```java
from pexpect import *
child = spawn('scp foo user@example.com:.')
child.expect('(?i)password')
child.sendline(mypassword)
1234
```

可替代为:

```java
from pexpect import *
run('scp foo user@example.com:.', events={'(?i)password': mypassword})
12
```

## Exceptions

`class pexpect.EOF(value)`
读取到EOF时引发。这通常意味着子进程已经退出。

`class pexpect.TIMEOUT`
超时异常
`class pexpect.ExceptionPexpect`
异常的基类

## Utility Functions

`pexpect.which(filename, env=None)`
同linux which 命令
`pexpect.split_command_line(cmd)`
这个方法会把命令按照空格分割，而且会处理命令当中的特殊字符(引号、双引号和转义字符).

## fdpexpect

类似于pexpect.spawn类。fd=file descriptor,用于处理文件相关的，不是很需要。

## popen_spawn

类似于pexpect.spawn类。不懂Linux的popen，暂时不研究了。

## replwrap

用于交互式编程环境REPL(Read-Eval-Print Loop).

## pxssh

### 基本用法

此类扩展pexpect.spawn以专门设置ssh连接。pexpect.spawn也可实现ssh连接功能。
此类的好处是替我们处理了在ssh连接中许多不难但是非常麻烦问题。比如使用spawn在登录ssh时需要匹配已登录的关键字，发送命令后，需要匹配命令已完成的关键字。这些关键字都可能因为系统的不同而不同，每次都一个一个配，虽然是可行的，但是十分麻烦。所以涉及ssh的地方，建议使用pxssh。
如果在ssh使用过程中，服务器行为有一些非典型的linux shell行为(如堡垒机首页，甚至不支持linux命令)，总之pxssh的默认设置不再有效时，pxssh用起来也不方便，甚至不如直接使用spawn。
`class pexpect.pxssh.pxssh(timeout=30, maxread=2000, searchwindowsize=None, logfile=None, cwd=None, env=None, ignore_sighup=True, echo=True, options={}, encoding=None, codec_errors='strict', debug_command_string=False)`
举例:

```java
from pexpect import pxssh
import getpass
try:
    s = pxssh.pxssh()
    hostname = raw_input('hostname: ')
    username = raw_input('username: ')
    password = getpass.getpass('password: ')
    s.login(hostname, username, password)
    s.sendline('uptime')   # run a command
    s.prompt()             # match the prompt
    print(s.before)        # print everything before the prompt.
    s.sendline('ls -l')
    s.prompt()
    print(s.before)
    s.sendline('df')
    s.prompt()
    print(s.before)
    s.logout()
except pxssh.ExceptionPxssh as e:
    print("pxssh failed on login.")
    print(e)
123456789101112131415161718192021
```

`PROMPT`
这是shell提示符，如果默认的提示符不能正常工作，可能需要手动设置该字段。
`options`
这是ssh登录时的参数设置

### 重要方法

`java login(server, username, password='', terminal_type='ansi', original_prompt='[#$]', login_timeout=10, port=None, auto_prompt_reset=True, ssh_key=None, quiet=True, sync_multiplier=1, check_local_ip=True, password_regex='(?i)(?:password:)|(?:passphrase for key)', ssh_tunnels={}, spawn_local_ssh=True, sync_original_prompt=True, ssh_config=None)`
`original_prompt`
这是原始提示符匹配,一般服务器是"#“或”$"。
`auto_prompt_reset`
默认是True，如果设置为Fasle,则prompt将会变成唯一的，并且在使用prompt()方法前需要手动设置PROMPT.
`password_regex`
如果登录欢迎词含有单词"password"时，可能需要设置这个字段以进行严格匹配。
`spawn_local_ssh`
默认True.如果在当前ssh连接中使用ssh命令，默认会使用一个新的会话，设置spawn_local_ssh=False则依然使用当前会话
`logout()`
退出,同linux命令logout
`prompt(timeout=-1)`
匹配shell提示符，当执行完一条命令后，shell会返回提示符模式，所以这个方法的作用就是匹配命令已完成。它封装了expect()方法。同时会删除before属性的内容。
`sync_original_prompt(sync_multiplier=1.0)`
调用这个方法child会尝试发送‘enter’，每次‘enter’都会得到shell prompt，此方法的目的就是通过比较多次的prompt得到准确的prompt
`set_unique_prompt()`
这会将远程提示设置为比‘$#’更独特的内容。这使得prompt()更容易明确匹配shell提示。此方法由login()自动调用，但如果以某种方式重置shell提示，则可能需要手动调用它。例如，如果“su”到另一个用户，则需要手动重置提示。
或者，可以使用设置的提示模式。在这种情况下，应该使用auto_prompt_reset=false调用login()，然后手动设置PROMPT属性。之后，prompt()将按照设置的PROMPT属性匹配。