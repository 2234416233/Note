# 根据名称查询容器ID镜像ID并停止删除

#### 根据容器名称查询容器ID并删除

```bash
# 第一种写法
docker stop `docker ps -a| grep test-project | awk '{print $1}' `
docker rm   `docker ps -a| grep test-project | awk '{print $1}' `

# 第二种写法
docker stop  `docker ps -aq --filter name=test-project`
docker rm    `docker ps -aq --filter name=test-project`
1234567
```

#### 根据镜像名称查询容器ID并删除

```bash
# 第一种写法
docker stop `docker ps -a| grep ygsama/test-project:1.0.2 | awk '{print $1}' `
docker rm   `docker ps -a| grep ygsama/test-project:1.0.2 | awk '{print $1}' `

# 第二种写法
docker stop  `docker ps -aq --filter ancestor=ygsama/test-project:1.0.2`
docker rm   `docker ps -aq --filter ancestor=ygsama/test-project:1.0.2`

12345678
```

#### 根据镜像名称查询镜像ID并删除

```bash
docker images -q --filter reference=ygsama/test-project*:*
docker image rm `docker images -q --filter reference=10.2.21.95:10001/treasury-brain*:*`
123
```

还可以根据端口号、状态等字段筛选容器，详细可以参考官方文档