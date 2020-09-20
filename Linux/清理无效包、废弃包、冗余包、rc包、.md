# ebian/Ubuntu清理无效包、废弃包、冗余包、rc包



# 清理Debian、Ubuntu中的冗余包、废弃包

在升级系统版本之后，或是在删除了第三方源之后，一些包就会变为无法更新的冗余包。
一个常见的现象是升级系统前，某个程序A依赖了旧版本的库libX.1.1，在升级系统之后另一个程序B依赖了新版本的libX.1.2。再由于某些依赖原因，A没有被升级为依赖libX.1.2的新版本，导致包管理系统保留了2个版本的libX。也就是说默认的`apt-get autoremove`命令无法删除它们。
通常情况下除了浪费一点空间这并没有什么问题，但是有些时候当libX.1.1与libX.1.2之间不相互兼容时，或我们写的其他程序链接了共享版本的libX时，这会导致程序运行异常或无法运行，尤其是在分布式的程序。特别是libboost系列库。

## remove与purge

很多package是有配置文件的。不管你是使用apt还是aptitude，只要是用默认的remove删除，都只是删除程序文件，保留配置文件的；而使用purge则是一个不留的全部删除。

## 清理废弃包

使用aptitude来自动检测并删除已经废弃的包。
作废包通常是前述的又了更新版本的同名包，或者是source不存在的包。

```bash
# 查看 （以下3个等价）
aptitude search "~o"
aptitude search ~o
aptitude search ?obsolete
# 清除
sudo aptitude purge "~o"
123456
```

这个清除命令会考虑包之间的依赖关系，把依赖这些作废包的其他包也一并删除掉。对于更复杂的情况，它会给出一个保留哪些删除哪些的解决方案。

参考：https://raphaelhertzog.com/2011/02/07/debian-cleanup-tip-2-get-rid-of-obsolete-packages/

## 清理rc包

有些包卸载不彻底，也会导致各种问题。
debian系的底层包管理程序dpkg可以对于帮助我们找到这些包。

使用`dpkg --list`可以查看所有包的状态，其中前两位表示状态，第一位为期望的状态，第二位为实际的状态。可以大致理解为：当我们输入安装命令时，第一位置为i（表示install），当命令完成时，第二位置为i。
更细节的表示可以参考文档，或者查看：https://linuxprograms.wordpress.com/2010/05/11/status-dpkg-list/

一个常见的非理想状态是：第一位为r（removed），第二位为c（Cfg-file存在)。

```bash
# 查看rc状态的包
dpkg --list |grep "^rc"
# 清除
dpkg --list |grep "^rc" | cut -d " " -f 3 | xargs sudo dpkg --purge
# 或者使用aptitude
sudo aptitude purge "~c"
```