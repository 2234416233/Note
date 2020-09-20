# Docker改变image存放位置


ubuntu上存放docker的位置在/val/lib/docker,这个根目录通常分配的空间较小。这里128G的ssd只有16G的空间
![df-l](https://img-blog.csdn.net/20180602135101453?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FMRE5PQUhfWkVSTw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
想扩充容量的方式很多，这里提供一种较为简陋的解决方案。

- 备份已有镜像

```bash
docker save -o 文件名.tar 镜像名1
```

- 指定新位置

```bash
mkdir /etc/systemd/system/docker.service.d
touch /etc/systemd/system/docker.service.d/docker-overlay.conf #文件名应该可以随便取
vim /etc/systemd/system/docker.service.d/docker-overlay.conf123
```

- docker-overlay.conf内容 #替换”/home/docker”为自己建的新目录

```bash
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --graph="/home/docker" --storage-driver=overlay123
```

- 重启docker

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker12
```

- 恢复刚刚备份的镜像

```bash
docker load -i [docker备份文件.tar]1
```

“移动”完成，之前/var/lib/docker就可以删掉了,当然空间有的多可以保留以防万一

```bash
sudo rm -r /var/lib/docker1
```

- 删除后，docker镜像转移到了home/docker目录，可以下更多镜像啦(ps:容量还是不够用，能接个大容量机械硬盘就好了)
  ![df-l](https://img-blog.csdn.net/20180602135149802?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FMRE5PQUhfWkVSTw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)