docker磁盘空间不足解决办法
导入docker镜像时，错误提示：磁盘空间不足。
1.查看docker镜像存放目录空间大小
```
du -hs /var/lib/docker/
```
2.停止docker服务。
```
systemctl stop docker
```
3.查看磁盘容量大的空间，且在上面创建新的docker目录。
```
df -h
mkdir -p /data/docker/lib
```
4.迁移/var/lib/docker目录下的文件到新创建的目录/data/docker/lib
```
rsync -avz /var/lib/docker /data/docker/lib/
```
5.编辑 /etc/docker/daemon.json 添加如下参数
```
{
  "graph": "/data/docker/lib/docker"
}
```
6.重新加载docker，并重启docker服务。
```
systemctl daemon-reload && systemctl restart docker
```
7.检查docker是否变更为新目录/data/docker/lib/docker
```
[root@localhost ~]# docker info
...
Docker Root Dir: /data/docker/lib/docker

Debug Mode (client): false

Debug Mode (server): false

Registry: https://index.docker.io/v1/
...
```
8.删掉docker旧目录
```
rm -rf /var/lib/docker
```
常见docker清理方法
```
docker system df 类似于Linux上的df命令，用于查看Docker的磁盘使用情况:

docker system prune 可以用于清理磁盘，删除关闭的容器、无用的数据卷和网络，以及dangling镜像(即无tag的镜像)。

docker system prune -a 清理得更加彻底，
```
可以将没有容器使用Docker镜像都删掉。注意，这两个命令会把你暂时关闭的容器，以及暂时没有用到的Docker镜像都删掉了…所以使用之前一定要想清楚.。我没用过，因为会清理 没有开启的 Docker
