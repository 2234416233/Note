# Docker 容器的挂载卷

## **Docker Volume**

- Docker镜像是由多个文件系统（只读层）叠加而成。当我们启动一个容器的时候，Docker会加载只读镜像层并在其上（即镜像栈顶部）添加一个读写层。如果运行中的容器修改了现有的一个已经存在的文件，那该文件将会从读写层下面的只读层复制到读写层，该文件的只读版本仍然存在，只是已经被读写层中该文件的副本所隐藏。当删除Docker容器，并通过该镜像重新启动时，之前的更改将会丢失（因为重新创建了容器）。在Docker中，只读层及在顶部的读写层的组合被称为 **UnionFile System（联合文件系统）** 。
- Docker中的数据可以存储在类似于虚拟机磁盘的介质中，在Docker中称为数据卷（Data Volume）。数据卷可以用来存储Docker应用的数据，也可以用来在Docker容器间进行数据共享。数据卷呈现给Docker容器的形式就是一个目录，支持多个容器间共享，修改也不会影响镜像。使用Docker的数据卷，类似在系统中使用`mount` 挂载一个文件系统。
- 为了能够保存（持久化）数据以及共享容器间的数据，Docker提出了Volume的概念。简单来说，Volume就是目录或者文件，它可以绕过默认的联合文件系统，而以正常的文件或者目录的形式存在于宿主机上。

**说明：**

- 1）一个数据卷是一个特别指定的目录，该目录利用容器的UFS文件系统可以为容器提供一些稳定的特性或者数据共享。数据卷可以在多个容器之间共享。

- 2）创建数据卷，只要在`docker run`命令后面跟上`-v`参数即可创建一个数据卷，当然也可以跟多个`-v`参数来创建多个数据卷，当创建好带有数据卷的容器后，就可以在其他容器中通过`--volumes-from`参数来挂载该数据卷了，而不管该容器是否运行。也可以在Dockerfile中通过VOLUME指令来增加一个或者多个数据卷。

- 3）如果有一些数据想在多个容器间共享，或者想在一些临时性的容器中使用该数据，那么最好的方案就是你创建一个数据卷容器，然后从该临时性的容器中挂载该数据卷容器的数据。 这样，即使删除了刚开始的第一个数据卷容器或者中间层的数据卷容器，只要有其他容器使用数据卷，数据卷都不会被删除的。

- 4）不能使用`docker export、save、cp`等命令来备份数据卷的内容，因为数据卷是存在于镜像之外的。备份的方法可以是创建一个新容器，挂载数据卷容器，同时挂载一个本地目录，然后把远程数据卷容器的数据卷通过备份命令备份到映射的本地目录里面。如下：

  ```bash
   # docker run --rm --volumes-from container -v ${pwd}:/backup ubuntu:latest tar cvf /backup/backup.tar -C /data
  ```
  
**解释**：**container** ：指定挂载卷绑定的容器（name | id）,`${pwd}`是docker支持的指定当前目录的方法, backup 是容器中的备份目录，backup.tar 是最终打包的报名+后缀，-C 后面指定打包数据对应的数据卷在容器中的目录；
  
- 5）也可以把一个本地主机的目录当做数据卷挂载在容器上，同样是在docker run后面跟-v参数，不过-v后面跟的不再是单独的目录了，它是`[host-dir]:[container-dir]:[rw|ro]`这样格式的， `host-dir`是一个绝对路径的地址，如果`host-dir`不存在，则docker会创建一个新的数据卷，如果`host-dir`存在，但是指向的是一个不存在的目录，则docker也会创建该目录，然后使用该目录做数据源。
  **解释**：:rw 读写（默认是读写），:ro 只读 如:-v /e/data:/data:ro

- 6）Volume可以将容器以及容器产生的数据分离开来，这样，当你使用docker rm my_container删除容器时，不会影响相关的数据，对于拥有挂载卷的容器，删除用docker rm -v my_container 的方式，避免不必要的文件的存在；

**Docker Volume数据卷可以实现：**

- 1）绕过“UFS”系统，以达到本地磁盘IO的性能，（比如运行一个容器，在容器中对数据卷修改内容，会直接改变宿主机上的数据卷中的内容，所以是本地磁盘IO的性能，而不是先在容器中写一份，最后还要将容器中的修改的内容拷贝出来进行同步）;
- 2）绕过“UFS”系统，有些文件不需要在docker commit打包进镜像文件;
- 3）数据卷可以在容器间共享和重用数据;
- 4）数据卷可以在宿主和容器间共享数据;
- 5）数据卷数据改变是直接修改的;
- 6）数据卷是持续性的，直到没有容器使用它们。即便是初始的数据卷容器或中间层的数据卷容器删除了，只要还有其他的容器使用数据卷，那么里面的数据都不会丢失;

**Docker数据的持久化：**

因为：容器在运行期间产生的数据是不会写在镜像里面的，重新用此镜像启动新的容器就会初始化镜像，会加一个全新的读写入层来保存数据。

- 数据持久化的方式：1：数据卷（Data volume），2：通过commit提交一个新的镜像来保存产生的数据；

## **创建数据卷：**

- 方式一：宿主机上未准备“数据卷”目录和文件，由容器默认指定

```bash
如下为容器添加一个数据卷，并将容器名改为container，这个数据卷在容器里的目录是/opt/data
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    latest              0ef2e08ed3fa        2 weeks ago         130 MB
 
[root@localhost ~]# docker run -it --name container -v /opt/data docker.io/ubuntu /bin/bash
root@2b9aebcf6ce8:/# cd /opt/data/
root@2b9aebcf6ce8:/opt/data# ls
root@2b9aebcf6ce8:/opt/data# echo "123" > txt1
root@2b9aebcf6ce8:/opt/data# echo "123123" > txt2
root@2b9aebcf6ce8:/opt/data# ls
 txt1  txt2
 
[root@localhost volumes]# docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                     NAMES
2b9aebcf6ce8        docker.io/ubuntu    "/bin/bash"           49 seconds ago      Up 48 seconds                                       container 
 
在宿主机上，查看对应上面的那个数据卷的目录路径：
[root@localhost ~]# docker inspect data|grep /var/lib/docker/volumes
                "Source": "/var/lib/docker/volumes/89d6562b9c1fe10dd21707cb697a5d481b3c1b000a69b762f540fa826a16972a/_data",
[root@localhost ~]# ls /var/lib/docker/volumes/89d6562b9c1fe10dd21707cb697a5d481b3c1b000a69b762f540fa826a16972a/_data
txt1  txt2
[root@localhost ~]# echo "asdhfjashdfjk" >> /var/lib/docker/volumes/89d6562b9c1fe10dd21707cb697a5d481b3c1b000a69b762f540fa826a16972a/_data/txt1
[root@localhost ~]#
 
root@2b9aebcf6ce8:/opt/data# ls
txt1  txt2
root@2b9aebcf6ce8:/opt/data# cat txt1  
asdhfjashdfjk
```

- 方式二：宿主机上提前准备“数据卷”目录和文件
  在宿主机上创建/e/data 目录，并创建一个look.txt文件，内容如下：

```
123
123
123
123
```

**查看镜像：**

```bash
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    latest              0ef2e08ed3fa        2 weeks ago         130 MB
```

**使用镜像创建容器：**

```bash
docker run -it --name container -p 8080:80 -p 8081:81 -v /e/data:/data  docker.io/ubuntu /bin/bash

[root@localhost volumes]# docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                     NAMES
2b9aebcf6ce8        docker.io/ubuntu    "/bin/bash"           49 seconds ago      Up 48 seconds                                       container 
 
root@2b9aebcf6ce8:# cd /data
root@2b9aebcf6ce8:/data# ls
look.txt
root@2b9aebcf6ce8:/data# cat look.txt
```

**说明：**

- 在用 docker run 命令的时候，可以指定挂载一个本地主机的目录到容器中去，可以使用多次-v选项为一个 docker
  容器运行挂载多个本地主机目录，如： `-v /e/data1:/data1 -v /e/data2:/data2`;
- 本地目录的路径必须是绝对路径，如果目录不存在 Docker 会自动为你创建它;
- 无论哪种方式都是做了同样的事情。它们告诉Docker在主机上创建一个目录（默认情况下是在`/var/lib/docker/volumes`下），然后将其挂载到指定的路径（例子中是：/data）。当删除使用该Volume的容器时，Volume本身不会受到影响，它可以一直存在下去。
- 如果在容器中不存在指定的路径，那么该目录将会被自动创建;
- docker版本及运行的宿主机系统不同，数据卷的查看和对应的具体路径会有差异;

## 挂载宿主机文件或目录到容器数据卷：

```bash
可以直接挂载宿主机文件或目录到容器里，可以理解为目录映射，这样就可以让所有的容器共享宿主机数据，从而只需要改变宿主机的数据源就能够影响到所有的容器数据。
 
注意：
-v后面的映射关系是"宿主机文件/目录:容器里对应的文件/目录"，其中，宿主机上的文件/目录是要提前存在的，容器里对应的文件/目录会自动创建。
 
数据卷权限：
挂载的数据默认为可读写权限。
但也可以根据自己的需求，将容器里挂载共享的数据设置为只读，这样数据修改就只能在宿主机上操作。如下实例：
 
1）挂载宿主机文件到容器上
[root@localhost ~]# cat /etc/web.list
192.168.1.100
192.168.1.101
192.168.1.103
[root@localhost ~]# docker run -it --name test -v /etc/web.list:/etc/web.list:ro docker.io/centos /bin/bash
[root@e21a3fefa3ae /]# cat /etc/web.list
192.168.1.100
192.168.1.101
192.168.1.103
[root@e21a3fefa3ae /]# echo "192.168.1.115" >> /etc/web.list
bash: /etc/web.list: Read-only file system
[root@e21a3fefa3ae /]#
  
在宿主机上修改共享数据
[root@localhost ~]# echo "192.168.1.115" >> /etc/web.list
[root@localhost ~]#
  
[root@e21a3fefa3ae /]# cat /etc/web.list
192.168.1.100
192.168.1.101
192.168.1.103
192.168.1.115
 
2）挂载宿主机目录到容器上
[root@localhost ~]# mkdir /var/huanqiupc
[root@localhost ~]# echo "test" > /var/huanqiupc/test
[root@localhost ~]# echo "test1" > /var/huanqiupc/test1
[root@localhost ~]# docker run -t -i --name hqsb -v /var/huanqiupc:/opt/huantime docker.io/centos /bin/bash
[root@87cf93ce46a9 /]# cd /opt/huantime/
[root@87cf93ce46a9 huantime]# ls
test  test1
[root@87cf93ce46a9 huantime]# cat test
test
[root@87cf93ce46a9 huantime]# cat test1
test1
[root@87cf93ce46a9 huantime]# echo "1231" >>test
[root@87cf93ce46a9 huantime]# echo "44444" >>test1
 
宿主机上查看
[root@localhost ~]# cat /var/huanqiupc/test
test
[root@localhost ~]# cat /var/huanqiupc/test1
test1
 
3）挂载多个目录
[root@localhost ~]# mkdir /opt/data1 /opt/data2
[root@localhost ~]# echo "123456" > /opt/data1/test1
[root@localhost ~]# echo "abcdef" > /opt/data2/test2
[root@localhost ~]# docker run --name data -v /opt/data1:/var/www/data1 -v /opt/data2:/var/www/data2:ro -t -i docker.io/ubuntu /bin/bash
root@cf2d57b9bee1:/# ls /var/www/data1
test1
root@cf2d57b9bee1:/# ls /var/www/data2
test2
root@cf2d57b9bee1:/# cat /var/www/data1/test1
123456
root@cf2d57b9bee1:/# cat /var/www/data2/test2
abcdef
root@cf2d57b9bee1:/# echo "date1" >> /var/www/data1/test1
root@cf2d57b9bee1:/# echo "date2" >> /var/www/data2/test2
bash: /var/www/data2/test2: Read-only file system
root@cf2d57b9bee1:/#
```

## 创建数据卷容器：

启动一个名为container容器，此容器包含两个数据卷/var/volume1和/var/volume2（这两个数据卷目录是在容器里的，容器创建的时候会自动生成这两目录）

```bash
注意一个细节：
下面的创建命令中，没有加-t和-i参数，所以这个容器创建好之后是登陆不了的！
-i：表示以“交互模式”运行容器
-t：表示容器启动后会进入其命令行
[root@linux-node2 ~]# docker run -v /var/volume1 -v /var/volume2 --name container  centos:latest  /bin/bash
[root@linux-node2 ~]#
 
所以要想创建容器后能正常登陆，就需要添加上面两个参数
[root@localhost ~]# docker run -it -v /var/volume1 -v /var/volume2 --name container centos:latest /bin/bash
[root@73a34f3c1cd9 /]#

查看宿主机上与数据卷对应的目录路径：
[root@localhost ~]# docker inspect container |grep /var/lib/docker/volumes
"Source": "/var/lib/docker/volumes/b8d2e5bcadf2550abd36ff5aa544c721a45464a4406fb50979815de773086627/_data",
"Source": "/var/lib/docker/volumes/a34fa3a0a7a2f126b0d30a32b1034f20917ca7bd0dda346014d768b5ebb68f6b/_data",
由上面命令结果可以查到，两个数据卷/var/volume1和/var/volume2下的数据在/var/lib/docker/volumes/下对于的两个目录的_data下面

创建App_Container容器，挂载container 容器中的数据卷
[root@linux-node2 ~]# docker run -t -i --rm --volumes-from container --name App_Container centos /bin/bash
[root@b9891bcdfed0 /]# ls /var/volume1                           //发现这两个数据卷都存在
[root@b9891bcdfed0 /]# ls /var/volume2
[root@b9891bcdfed0 /]# echo "this is volume1" > /var/volume1/test1
[root@b9891bcdfed0 /]# echo "this is volume2" > /var/volume1/test2

可以再创建一个容器，挂载App_Container中从container 挂载的数据卷。当然也可以直接挂载初始的container 容器数据卷
[root@linux-node2 ~]# docker run -t -i --rm --volumes-from App_Container --name LastApp_Container centos /bin/bash
[root@b4c27e360614 /]# ls /var/volume1
test1
[root@b4c27e360614 /]# ls /var/volume2
test2
[root@b4c27e360614 /]# cat /var/volume1/test1 
this is volume1
[root@b4c27e360614 /]# cat /var/volume2/test2 
this is volume2

即便是删除了初始的数据卷容器container ，或是删除了其它容器，但只要是有容器在使用该数据卷，那么它里面的数据就不会丢失！（除非是没有容器在使用它们）
```

## 备份数据卷:

```bash
docker run --rm --volumes-from test -v ${pwd}:/backup ubuntu:14.04 tar cvf /backup/test.tar /test
tar: Removing leading `/' from member names
/test/
/test/b
/test/d
/test/c
/test/a
  
以上命令表示：
启动一个新的容器并且从test容器中挂载卷，然后挂载当前目录到容器中为backup，并备份test卷中所有的数据为test.tar，执行完成之后删除容器--rm，此时备份就在当前的目录下，名为test.tar。
注意：后面的/test是数据卷的目录路径（即数据卷创建时在容器里的路径）

 可能出现的提示：
 file is the archive; not dumped --文件是归档文件，不是转储文件 归档文件不能再归档了
 backup是备份目录，执行完命令后，因为--rm所以容器删除了，备份目录同时也不存在了，备份目录是在备份的时候创建的，原本不存在该目录
  
$ ls
宿主机当前目录下产生了test卷的备份文件test.tar
  
---------------------------------------------看看下面的一个实例---------------------------------------------
先创建一个容器wang，包含两个数据卷/var/volume1和/var/volume2（这两个目录是在容器里的数据卷路径）
[root@localhost ~]# docker run -t -i -v /var/volume1 -v /var/volume2 --name wang docker.io/centos /bin/bash
[root@83eb43492ae7 /]#
  
根据Docker的数据持久化之数据卷容器可知,上面创建的wang数据卷容器挂载了/var/volume1和/var/volume2两个目录
然后在数据卷里写些数据，以供测试。
[root@83eb43492ae7 /]# cd /var/volume1
[root@83eb43492ae7 volume1]# echo "test1" > test1
[root@83eb43492ae7 volume1]# echo "test11" > test11
[root@83eb43492ae7 volume1]# echo "test111" > test111
[root@83eb43492ae7 volume1]# ls
test1  test11  test111
[root@83eb43492ae7 volume1]# cd ../volume2
[root@83eb43492ae7 volume2]# echo "test2" > test2
[root@83eb43492ae7 volume2]# echo "test22" > test22
[root@83eb43492ae7 volume2]# echo "test222" > test222
[root@83eb43492ae7 volume2]# ls
test2  test22  test222
[root@83eb43492ae7 volume2]#
  
然后进行这两个数据卷的备份
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                     NAMES
83eb43492ae7        docker.io/centos    "/bin/bash"           2 minutes ago       Up 2 minutes                                  wang
  
  
现在开始进行数据卷的备份操作：
为了利用数据卷容器备份,使用--volumes-from标记来创建一个加载wang容器卷的容器，并从主机挂载当前目录到容器的/backup目录。并备份wang卷中的数据，执行完成之后删除容器--rm，此时备份就在当前的目录下了。
  
1）备份wang容器中的/var/volume1数据卷数据<strong>（注意下面：命令中的-i和-t这两个参数加不加都可以；--rm加上，备份后就会自动删除这个容器，如果不加这个--rm参数，那么备份后的容器就会保留，docker ps -a就会查看到）</strong>
[root@localhost ~]# docker run -i -t --rm --volumes-from wang -v ${pwd}:/backup docker.io/centos tar cvf /backup/backup1.tar /var/volume1
tar: Removing leading `/' from member names
/var/volume1/
/var/volume1/test1
/var/volume1/test11
/var/volume1/test111
  
2）备份wang容器中的/var/volume2数据卷数据
[root@localhost ~]# docker run -i -t --rm --volumes-from wang -v ${pwd}:/backup docker.io/centos tar cvf /backup/backup2.tar /var/volume2
tar: Removing leading `/' from member names
/var/volume2/
/var/volume2/test2
/var/volume2/test22
/var/volume2/test222
  
3）备份wang容器中的/var/volume1和/var/volume2数据卷数据
[root@localhost ~]# docker run -i -t --rm --volumes-from wang -v ${pwd}:/backup docker.io/centos tar cvf /backup/backup.tar /var/volume1 /var/volume2
tar: Removing leading `/' from member names
/var/volume1/
/var/volume1/test1
/var/volume1/test11
/var/volume1/test111
/var/volume2/
/var/volume2/test2
/var/volume2/test22
/var/volume2/test222
[root@localhost ~]# ls
anaconda-ks.cfg  a.py  backup1.tar  backup2.tar  backup.tar  mkimage-yum.sh  pipework  var  wang.tar
  
这样,数据卷容器中的数据就备份完成了. 简言之就是：
先创建一个容器，并挂载要备份的容器数据卷，再挂载数据卷${pwd}:/backup目录到容器/bakcup，在容器中执行备份/data目录到/backup，也就是备份到宿主机${pwd}:/backup目录。
```

## 恢复或迁移数据卷:

```bash
可以恢复给同一个容器或者另外的容器，新建容器并解压备份文件到新的容器数据卷
[$ sudo 无root权限的时候可以带上sudo] docker run -t -i -d -v /test --name test4 ubuntu:14.04  /bin/bash
docker run --rm --volumes-from test4 -v ${pwd}:/backup ubuntu:14.04 tar xvf /backup/test.tar -C /
恢复之前的文件到新建卷中，执行完后自动删除容器 test/ test/b test/d test/c test/a
  
-----------------------------接着上面的实例进行数据卷恢复操作--------------------------
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                     NAMES
531c9d8adf4c        docker.io/centos    "/bin/bash"           2 minutes ago       Up 44 seconds                                 wang
  
1）恢复数据给同一个容器
测了测试效果，先删除数据卷（注意：数据卷目录删除不了，只能删除其中的数据。）
[root@localhost ~]# docker attach wang
[root@531c9d8adf4c ~]# ls /var/volume1
test1  test11  test111
[root@531c9d8adf4c ~]# ls /var/volume2
test2  test22  test222
[root@531c9d8adf4c ~]# rm -rf /var/volume1 /var/volume2
rm: cannot remove '/var/volume1': Device or resource busy 
rm: cannot remove '/var/volume2': Device or resource busy
[root@531c9d8adf4c ~]# ls /var/volume2
[root@531c9d8adf4c ~]# ls /var/volume1
  
现在进行数据卷恢复，恢复数据卷中的所有数据：
[root@localhost ~]# ls
anaconda-ks.cfg  a.py  backup1.tar  backup2.tar  backup.tar  mkimage-yum.sh  pipework  var  wang.tar
  
注意-C后面的路径，这个路径表示将数据恢复到容器里的路径。
命令中用"/"，即表示将backup.tar中的数据解压到容器的/路径下。后面跟什么路径，就解压到这个路径下。因此这里用"/"
[root@localhost ~]# docker run --rm --volumes-from wang -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
  
再次到容器里查看，发现数据卷里的数据已经恢复了
[root@531c9d8adf4c ~]# ls /var/volume1
test1  test11  test111
[root@531c9d8adf4c ~]# ls /var/volume2
test2  test22  test222
  
2）恢复数据给另外的容器，新建容器并解压备份文件到新的容器数据卷
即新建一个容器huihui，将上面备份的数据卷数据恢复到这个新容器里。
[root@localhost ~]# docker run -t -i -v /var/volume1 -v /var/volume2 --name huihui docker.io/centos /bin/bash
[root@f6ff380e0b7f var]# ls /var/volume1
[root@f6ff380e0b7f var]# ls /var/volume2
  
[root@localhost ~]# ls
anaconda-ks.cfg  a.py  backup1.tar  backup2.tar  backup.tar  mkimage-yum.sh  pipework  var  wang.tar
  
[root@localhost ~]# docker run --rm --volumes-from huihui -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
  
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                     NAMES
813afe297b60        docker.io/centos    "/bin/bash"           17 seconds ago      Up 16 seconds                                 huihui
  
  
这里注意一下：
新容器创建时挂载的数据卷路径最好是和之前备份的数据卷路径一致
如下：
1）新建容器挂载的数据卷只是备份数据卷的一部分，那么恢复的时候也只是恢复一部分数据。如下，新容器创建时只挂载/var/volume1
[root@localhost ~]# docker run -t -i -v /var/volume1 --name huihui docker.io/centos /bin/bash
[root@12dd8d742259 /]# ls /var/volume1/
[root@12dd8d742259 /]# ls /var/volume2
ls: cannot access /var/volume2: No such file or directory
  
[root@localhost ~]# docker run --rm --volumes-from huihui -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
[root@localhost ~]#
  
查看容器，发现只恢复了/var/volume1的数据，/var/volume2数据没有恢复，因为没有容器创建时没有挂载这个。
[root@localhost ~]# docker run -t -i -v /var/volume1 --name huihui docker.io/centos /bin/bash
[root@12dd8d742259 /]# ls /var/volume1/
[root@12dd8d742259 /]# ls /var/volume2
ls: cannot access /var/volume2: No such file or directory
  
2）新容器创建时只挂载/var/volume2
[root@localhost ~]# docker run -t -i -v /var/volume2 --name huihui docker.io/centos /bin/bash
[root@da3a3d2c95e0 /]# ls /var/volume2/
[root@da3a3d2c95e0 /]# ls /var/volume1
ls: cannot access /var/volume1: No such file or directory
  
[root@localhost ~]# docker run --rm --volumes-from huihui -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
[root@localhost ~]#
  
[root@da3a3d2c95e0 /]# ls /var/volume1
ls: cannot access /var/volume1: No such file or directory
[root@da3a3d2c95e0 /]# ls /var/volume2/
test2  test22  test222
  
3)如果新容器创建时挂载的数据卷目录跟之前备份的路径不一致
[root@localhost ~]# docker run -t -i -v /var/huihui --name huihui docker.io/centos /bin/bash
[root@9bad9b3bde71 /]# ls /var/huihui/
[root@9bad9b3bde71 /]#
  
如果解压时-C后面跟的路径不是容器挂载的容器，那么数据恢复不了，如下
[root@localhost ~]# docker run --rm --volumes-from huihui -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
  
发现容器内数据没有恢复
[root@9bad9b3bde71 /]# ls /var/huihui/
[root@9bad9b3bde71 /]#
  
但是如果解压时-C后面跟的是容器挂载的路径，数据就能正常恢复
[root@localhost ~]# docker run --rm --volumes-from huihui -v ${pwd}:/backup docker.io/centos tar xvf /backup/backup.tar -C /var/huihui
var/volume1/
var/volume1/test1
var/volume1/test11
var/volume1/test111
var/volume2/
var/volume2/test2
var/volume2/test22
var/volume2/test222
[root@localhost ~]#
  
发现容器内数据已经恢复了
[root@9bad9b3bde71 /]# ls /var/huihui/
var
[root@9bad9b3bde71 /]# ls /var/huihui/var/
volume1  volume2
[root@9bad9b3bde71 /]# ls /var/huihui/var/volume1
test1  test11  test111
[root@9bad9b3bde71 /]# ls /var/huihui/var/volume2
test2  test22  test222
```

## 删除数据卷：

```bash
Volume 只有在下列情况下才能被删除：
1）docker rm -v删除容器时添加了-v选项
2）docker run --rm运行容器时添加了--rm选项
否则，会在/var/lib/docker/volumes目录中遗留很多不明目录。
注意：即使用以上两种命令，也只能删除没有容器连接的Volume。连接到用户指定主机目录的Volume永远不会被docker删除，简而言之：宿主机上的目录不会被删除；
  
可以使用下面方式找出，然后删除_data目录下的数据文件
[root@localhost volumes]# docker inspect huihui|grep /var/lib/docker/volumes
 "Source": "/var/lib/docker/volumes/97aa95420e66de20abbe618fad8d0c1da31c54ce97e32a3892fa921c7942d42b/_data",
```

**总结：**

- 交互式和守护式需要加 /bin/bash ；
- 挂载间接数据卷容器：如 容器C 容器B（共享容器A数据卷的容器，挂载了容器卷A） 容器A（数据卷容器），那么容器C 可以通过挂载容器B间接挂载/共享容器A的挂载卷信息；
- 进入容器，执行df命令查看该容器挂载卷情况，如图：
  ![在这里插入图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20181019184559864)
- 还可以使用多个 --volumes-from 参数来从多个容器挂载多个数据卷;
- docker --help 查看docker的帮助命令,如图:
  ![在这里插入图片描述](https://cdn.jsdelivr.net/gh/2234416233/myImage/img/20181019184811839)