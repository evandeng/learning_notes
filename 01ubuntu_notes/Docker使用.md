

# [Docker使用]([Ubuntu Docker 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/ubuntu-docker-install.html))

Docker 中的三个概念：镜像，容器，仓库

1. 镜像（image）：Docker 镜像就是一个只读的模板，镜像可以用来创建 Docker 容器。Docker 提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一个已经做好的镜像来直接使用。
   镜像是一种文件结构。Dockerfile中的每条命令都会在文件系统中创建一个新的层次结构，文件系统在这些层次上构建起来，镜像就构建于这些联合的文件系统之上。Docker官方网站专门有一个页面来存储所有可用的镜像，网址是：[index.docker.io](https://www.qcloud.com/community/article/93)。
2. 容器（ Container）：容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个简易版的 Linux 环境，Docker 利用容器来运行应用。镜像是只读的，容器在启动的时候创建一层可写层作为最上层。
3. 仓库：仓库是集中存放镜像文件的场所，仓库注册服务器（Registry）上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。目前，最大的公开仓库是 Docker Hub，存放了数量庞大的镜像供用户下载。

Docker仓库用来保存我们的images，当我们创建了自己的image之后我们就可以使用push命令将它上传到公有或者私有仓库，这样下次要在另外一台机器上使用这个image时候，只需要从仓库上pull下来就可以了。Docker 仓库的概念跟 Git 类似，注册服务器可以理解为 GitHub 这样的托管服务

#### docker安装

使用官方安装脚本自动安装，安装命令如下：

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

测试 Docker 是否安装成功，输入以下指令，打印出以下信息则安装成功:

```
sudo docker run hello-world
```

#### 卸载 docker

删除安装包：

```
sudo apt-get purge docker-ce
```

删除镜像、容器、配置文件等内容：

```
sudo rm -rf /var/lib/docker
```

#### 常用命令

[Docker的基本命令_苍穹尘的博客-CSDN博客_docker 命令](https://blog.csdn.net/lizhiqiang1217/article/details/89070075)

1. 查看镜像

```
docker images查看docker镜像
docker run -d --name mynginx nginx   #启动nginx镜像，没有会自动pull
#手动pull
docker pull scrin/dev-spconv
```

`docker images -a`列出本地所有的镜像
`docker images -p`只显示镜像ID
`docker images --digests`显示镜像的摘要信息
`docker images --no-trunc`显示完整的镜像信息

`docker rmi hello-world`从Docker中删除hello-world镜像
`docker rmi -f hello-world`从Docker中强制删除hello-world镜像
`docker rmi -f hello-world nginx`从Docker中强制删除hello-world镜像和nginx镜像
`docker rmi -f $(docker images -p)`通过docker images -p查询到的镜像ID来删除所有镜像

拉取镜像

通过命令可以从镜像仓库中拉取镜像，默认从[Docker Hub](https://hub.docker.com/) 获取。

命令格式：

```
 docker image pull <repository>:<tag>
 #查看拉取的镜像
 docker image ls
```

2. 启动容器

Docker容器启动的时候，如果要挂载宿主机的一个目录，可以用-v参数指定。譬如我要启动一个centos容器，宿主机的/test目录挂载到容器的/soft目录，可通过以下方式指定：

```
docker run -it --name=pcdet -v /home/evandeng/project/dockers/openpcdet:/project/openpcdet image_name /bin/bash
```

拉一个GPU docker的正确姿势：

```
sudo docker run -it --gpus all --name=pcdet -e NVIDIA_DRIVER_CAPABILITIES=compute,utility -e NVIDIA_VISIBLE_DEVICES=all -v /home/evandeng/project/dockers/spconv:/root/project scrin/dev-spconv /bin/bash
```

以上出现错误，docker: Error response from daemon: could not select device driver "" with capabilities: [[gpu]].

删除container，先安装如下，再重新配置。

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
这个大家应该比较熟，老版本的docker安装都会使用这个进行添加GPG key
# 以下分别适用于centos和ubuntu
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.repo | sudo tee /etc/yum.repos.d/nvidia-docker.repo
或者curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```



3. 进入到docker容器

```
docker exec -it container_name /bin/sh
# or
docker exec -it container_ID /bin/sh
```

4. 关闭启动重启

```
exit
docker restart container_id
docker exec -it container_ID /bin/sh

docker stop container_id
docker start container_id
docker restart container_id
```

查看所有容器，包括关闭的

```
docker ps -a
```

5. 提交新的镜像

```
# 先关闭对应的容器，才能删除images
docker commit container_id new_name
```



注意：便容器销毁了，新建的挂载目录不会消失。

```
docker stop bfd094233f96   #停止一个容器，根据容器 id 进行删除 
docker rm bfd094233f96   #删除一个容器 
docker attach d20f3dc6cd92  #进入一个正在运行的容器
```

存储镜像：

```
# docker save -o ubuntu_14.04.tar ubuntu:14.04
```

载入镜像：

```
# docker load < ubuntu_14.04.tar  或者使用 
# cat ubuntu.tar |  docker import - test/ubuntu:v1.0
```

移除本地镜像：

```
# docker rmi training/sinatra
```


清理所有未打过标签的本地镜像：

```
# docker rmi $(docker images -q -f "dangling=true")
```

docker ps列出当前所有正在运行的容器
docker ps -a列出所有的容器
docker ps -l列出最近创建的容器
docker ps -n 3列出最近创建的3个容器
docker ps -q只显示容器ID
docker ps --no-trunc显示当前所有正在运行的容器完整信息
exit退出并停止容器
Ctrl+p+q只退出容器，不停止容器
docker start 容器ID或容器名称启动容器
docker restart 容器ID或容器名称重新启动容器
docker stop容器ID或容器名称停止容器
docker kill 容器ID或容器名称强制停止容器
docker rm 容器ID或容器名称删除容器
docker rm -f 容器ID或容器名称强制删除容器
docker rm -f $(docker ps -a -q)删除多个容器
docker logs -f -t --since --tail 容器ID或容器名称查看容器日志
如：docker logs -f -t --since=”2018-09-10” --tail=10 f9e29e8455a5
 -f : 查看实时日志
 -t : 查看日志产生的日期
 --since : 此参数指定了输出日志开始日期，即只输出指定日期之后的日志
 --tail=10 : 查看最后的10条日志
docker top 容器ID或容器名称查看容器内运行的进程
docker inspect 容器ID或容器名称查看容器内部细节
docker attach 容器ID进到容器内
docker exec 容器ID进到容器内
docker cp 容器ID:容器内的文件路径 宿主机路径从容器内拷贝文件到宿主机.
如：docker cp f9e29e8455a5:/tmp/yum.log /root

## 常见问题

1. 进入docker，命令行只有井号

解决办法,输入前面的两个命令

```
cp /etc/skel/.bash* /root/
su
```

