# GCC 7.3.0编译安装



科大开源镜像站 GCC源码
https://mirrors.ustc.edu.cn/gnu/gcc/

1. 下载gcc-7.3.0
   `wget https://mirrors.ustc.edu.cn/gnu/gcc/gcc-7.3.0/gcc-7.3.0.tar.gz`

2. 解压&进入目录
   `tar -xf gcc-7.3.0.tar.gzcd gcc-7.3.0`

3. 运行 download_prerequisites 脚本
   `./contrib/download_prerequisites`

4. 建立编译输出目录，将所有的中间文件都放到该目录
   `mkdir gcc-build-7.3.0cd gcc-build-7.3.0`

5. 配置
   `../configure --enable-checking=release --enable-languages=c,c++ --disable-multilib`
   参数解释：
   –enable-checking=release 增加一些检查，也可以–disable-checking生成的编译器在编译过程中不做额外检查
   –enable-languages=c,c++ 你要让你的gcc支持的编程语言
   –disable-multilib 取消多目标库编译(取消32位库编译)

6. 编译
   `sudo make`
   这个编译了我一个小时，建议你看个电影

7. 编译完成之后，需要把原来的GCC卸载，换成7.3.0版本的
   找到已安装的gcc和gcc-c++包
   `rpm -q gccrpm -q gcc-c++`

   删除已经安装低版本
   `rpm -e [包名]`
   包名为上一步中显示的包，形如：
   gcc-4.8.5-16.el7_4.2.x86_64
   gcc-c++-4.8.5-16.el7_4.2.x86_64

   那就删除吧
   `rpm -e gcc-4.8.5-16.el7_4.2.x86_64rpm -e gcc-c++-4.8.5-16.el7_4.2.x86_64`

8. 安装
   `sudo make install`

9. 验证版本
   查看gcc的安装位置：which gcc
   然后再查看版本 /usr/local/bin/gcc -v，通常gcc都安装在该处位置
   ![gcc-v](https://img-blog.csdn.net/20180315111824832?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1JCUGljc2Ru/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

10. 对了，还有一点，我直接尝试`gcc -v`的时候，提示没有找到命令，应该是系统变量中没有，那就手动添加一下gcc的安装目录

    - 直接用export命令：
      `export PATH=$PATH:/usr/local/bin`
      查看是否已经设好，可用命令`export`查看，或者直接`echo $PATH`
    - 这个应该更靠谱一点，要不然有时候sudo的时候会提示找不到gcc命令
      `sudo ln -s /usr/local/bin/gcc /usr/bin/gcc`
      (建立/usr/bin/gcc到编译安装gcc的软链接)

升级成功，enjoy~