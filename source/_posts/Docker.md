---
title: Docker
date: 2023-08-24 20:18:49
tags: Docker
categories: Java
cover: https://wiki-media-1253965369.cos.ap-guangzhou.myqcloud.com/img/20210116153041.png
description: Docker是一个开源的容器引擎，是一种操作系统级别的虚拟化技术，Docker让开发者可以打包自己的应用以及依赖到一个可移植的容器当中，然后发布到任何的Linux机器上面。
---

## Docker概述

### Docker为什么出现？

一款产品：开发-上线 两套环境！应用环境！应用配置！

开发 ··· 运维。问题：我的电脑用运行！版本更新，导致服务不可用！对于运维来说，考研太大了

环境配置是十分麻烦，每一个机器都要部署环境(集群redis,es、Hadoop)！费时费力

发布一个项目(jar + Redis Mysql + jdk ES),项目能不能都带上环境安装打包



Docker给以上的问题，提出了解决方案

### Docker能做什么：

**虚拟机技术缺点：**

1. 资源占用十分多
2. 冗余步骤多
3. 启动很慢

> 容器化技术

`容器化技术不是模拟一个完整的系统`

比较docker和虚拟机技术的不同：

* 传统虚拟机，虚拟处一条硬件，运行一个完整的操作系统，然后在这个系统上安装软件
* 容器内的应用直接运行在宿主机的内，容器没有自己的内核，也没有虚拟我们的硬件，所以轻便了
* 每一个容器是相互隔离的，每个容器内都有一个数据自己的文件系统，互不影响

> DevOps（开发、运维）

**应用更快速的交付和部署:**

传统：一堆帮助文档，安装程序

Docker:打包镜像发布测试，一键运行

**更便捷的升级和扩缩容:**

使用了Docker之后，我们部署应用就像搭积木一样！

项目打包为一个镜像，扩展服务器A!服务器B！

**更简单的系统运维:**

在容器化之后，我的开发，测试环境都是高度一致的

**更高效的计算机资源利用:**

Docker是内核级别的虚拟化，可以再一个物理机上运行很多的容器实例！服务器性能可以压榨到极致

## Docker安装

### Docker基本组成

**镜像(image)：**

docker镜像就好比一个模板，可以通过这个模板来创建容器服务，tomcat镜像==>run==>tomcat容器(提供服务器)

**容器(container)：**

docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建。启动，停止，删除，基本命令！

目前可以把这个容器理解为就是一个简易的linux系统

**仓库(repository)：**

仓库就是存放镜像的地方

仓库分为公有和私有

Docker Hub（默认是国外的）

阿里云……都有容器服务器(配置镜像加速)

### 安装Docker

> 环境准备

1.需要会一点点linux的基础

2.CentOs7

3.连接服务器

> 环境查看

查看系统内核版本

```bash
# 系统内核是3.10以上
[root@localhost /]# uname -r
3.10.0-1160.el7.x86_64
```

系统版本

```bash
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

帮助文档

```bash
#卸载旧的docker
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#需要的安装包
yum install -y yum-utils
#设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
#阿里云的镜像
 yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#更新yum软件包索引
yum makecache fast
#安装docker相关的 docker-ce社区 ee企业版
yum install docker-ce docker-ce-cli containerd.io
#启动Dockers
systemctl start docker
# 查看docker版本
docker version
#hello-world
docker run hello-world
```

```bash
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:80f31da1ac7b312ba29d65080fddf797dd76acfb870e677f390d5acba9741b17
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

```
#查看下载的hello-world镜像
[root@localhost /]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
```

删除docker

```bash
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker
```

### Docker为什么比VM快

1、Docker有着比虚拟机更少的抽象层

2、Docker利用的是宿主机的内核，vm需要是Guest OS

所以说，新建一个容器时，docker不需要虚拟机一样需要重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS，分钟级别的，而docker是利用宿主机的操作系统内核，省略了这个复杂的过程，秒级

## Dokcer的常用命令

### 帮助文档

```bash
docker version #显示dokcer的版本系统
docker info #显示dokcer的系统信息，包括镜像和容器的数量
docker 命令 --help #万能命令
```

### 镜像命令

```bash
docker images #查看所有的镜像
#解释
REPOSITORY 镜像的仓库源
TAG		   镜像的标签
IMAGE ID   镜像的id
CREATED    镜像的创建时间
SIZE       镜像的大小

#可选项
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像id
```

#### docker search镜像搜索

```bash
[root@localhost lyj]# docker search mysql
NAME                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                          MySQL is a widely used, open-source relation…   12562     [OK]       
mariadb                        MariaDB Server is a high performing open sou…   4830      [OK]       
percona                        Percona Server is a fork of the MySQL relati…   576       [OK]       
phpmyadmin                     phpMyAdmin - A web interface for MySQL and M…   536       [OK]       
bitnami/mysql                  Bitnami MySQL Docker Image                      71                   [OK]
# 可选项，通过搜索来过滤
--filter=STARS=3000
[root@localhost lyj]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   12562     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4830      [OK] 
```

#### docker pull下载镜像

```
#下载镜像docker pull 镜像名[:tag]
[root@localhost lyj]# docker pull mysql
Using default tag: latest
```

#### dokcer rmi 删除镜像

```bash
#全部删除
[root@localhost lyj]# docker rmi -f $(docker images -aq)
#单个删除
docker rmi -f 镜像id
```

### 容器命令

**说明：有了镜像才可以创建容器，Linux，下载一个centos镜像来测试学习**

```bash
docker pull centos
```

#### 新建容器并启动

```bash
docker run [可选参数] image
# 参数说明
--name=“Name”容器名字， tomcat01,tomcat02,用来区分容器
--d       后台方式运行
--it      使用交互方式运行，进入容器查看内容
--p       指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口：容器端口(常用)
	-p 容器端口
	容器端口
-p        随机指定端口
# 测试，启动并进入容器
[root@localhost lyj]# docker run -it centos /bin/bash
[root@a7546e01fc42 /]# ls #查看容器内的centos，基础版本，很多命令都是不完善的
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
#从容器中退回主机
[root@a7546e01fc42 /]# exit
exit
```

#### 列出所有的运行中的容器

```bash
# docker ps 命令
-a  #列出当前正在运行的容器+带出历史运行过的容器
-n=? #显示最近创建的容器
-q   #只显示容器的编号
[root@localhost lyj]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost lyj]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED             STATUS                          PORTS     NAMES
a7546e01fc42   centos         "/bin/bash"   3 minutes ago       Exited (0) About a minute ago             condescending_williams
9913150f68cb   feb5d9fea6a5   "/hello"      About an hour ago   Exited (0) About an hour ago              ecstatic_hodgkin
```

#### 退出容器

```bash
exit #直接停止并退出容器
Ctrl + P + Q #容器不停止但退出
```

#### 删除容器

```bash
docker rm 容器id #删除指定内容，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq) #删除所有内容
docker ps -a -q|xargs docker rm #删除所有容器
```

#### 启动和停止容器的操作

```
docker start 容器id #启动容器
docker restart 容器id #重启容器
docker stop 容器id #停止当前正在运行的容器
docker kill 容器id #强制停止当前容器
```

### 常用的其他命令

#### 后台启动容器

```bash
# 命令 docker run -d 镜像名
[root@localhost lyj]# docker run -d centos
0f13a674e8d67ff74b2498339d3c21d3ff2f99b3993a4121af9fa86246be916d
[root@localhost lyj]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止，容器容器后发现自己没有提供服务，就会立刻停止，就是没有程序了
```

#### 查看日志

```bash
docker logs -f -t --tail 容器，没有日志
#自己编写一段shell脚本
"while true;do echo lyj;slepp 1;done"
#显示日志
-tf           #显示日志
--tail number #显示日志条数
docker logs -ft --tail 10 24ac83cd18b4
```

#### 查看容器中的进程信息

```bash
#命令 docker top 容器id
[root@localhost lyj]# docker top 24ac83cd18b4
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                67867               67847               0                   17:00               ?                   00:00:00            /bin/sh -c while true;do echo lyj;sleep 1;done
root                68185               67867               0                   17:03               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

#### 查看镜像的元数据

```bash
# 命令
[root@localhost lyj]# docker inspect 24ac83cd18b4
[
    {
        "Id": "24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427",
        "Created": "2022-05-13T09:00:28.314887816Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo lyj;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 67867,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-05-13T09:00:28.676332024Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427/hostname",
        "HostsPath": "/var/lib/docker/containers/24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427/hosts",
        "LogPath": "/var/lib/docker/containers/24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427/24ac83cd18b425e9f503e5899842dd1603559c0443926a393e814b7259aeb427-json.log",
        "Name": "/elastic_gagarin",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/5c73a261c173cec8db9c2dd48143a5b963b2cce23dda4eb01262802efc898c54-init/diff:/var/lib/docker/overlay2/993f875b14fb02906ce0a4cc96d2fd0bd51ac4c3137d23be448fb77fefd26c14/diff",
                "MergedDir": "/var/lib/docker/overlay2/5c73a261c173cec8db9c2dd48143a5b963b2cce23dda4eb01262802efc898c54/merged",
                "UpperDir": "/var/lib/docker/overlay2/5c73a261c173cec8db9c2dd48143a5b963b2cce23dda4eb01262802efc898c54/diff",
                "WorkDir": "/var/lib/docker/overlay2/5c73a261c173cec8db9c2dd48143a5b963b2cce23dda4eb01262802efc898c54/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "24ac83cd18b4",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo lyj;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "c3c2cb8267717831d8702df2cc45333b92f175b66461de902323aa9d61e94252",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/c3c2cb826771",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "f73444ad58e812ca8039b7ccd3c683dbaa63873183a14c1a2d395136f6414d5b",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "b3b838684f4e337f0a1e6fb386dd7b7a6860ba5e5b34bf1b2d32fd1833a058b5",
                    "EndpointID": "f73444ad58e812ca8039b7ccd3c683dbaa63873183a14c1a2d395136f6414d5b",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

#### 进入当前正在运行的容器

```bash
#我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置
#命令
docker exec -it 容器id bashShell

#测试
[root@localhost lyj]# docker exec -it 24ac83cd18b4 /bin/bash
[root@24ac83cd18b4 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@24ac83cd18b4 /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 09:00 ?        00:00:06 /bin/sh -c while true;do echo lyj;sleep 1;done
root       9470      0  0 11:39 pts/0    00:00:00 /bin/bash
root       9501      1  0 11:39 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/binroot       9502   9470  0 11:39 pts/0    00:00:00 ps -ef

#方式二
docker attach 容器id
#测试
[root@localhost lyj]# docker attach  24ac83cd18b4
#正在执行当前的代码

#docker exec 进入容器后开启一个新的终端，可以在里面操作（常用）
#docker attach 进入容器正在执行的终端，不会启动新的进程
```

#### 从容器内拷贝文件到主机上

```bash
docker cp 容器id:容器内路径  目的的主机路径

#测试
[root@localhost home]# docker attach e1c59c6358fd
[root@e1c59c6358fd /]# cd /home
[root@e1c59c6358fd home]# ls
[root@e1c59c6358fd home]# touch test.java
[root@e1c59c6358fd home]# ls
test.java
[root@e1c59c6358fd home]# exit
exit
[root@localhost home]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost home]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
e1c59c6358fd   centos    "/bin/bash"   2 minutes ago   Exited (0) 8 seconds ago             romantic_aryabhata
[root@localhost home]# docker cp e1c59c6358fd:/home/test.java /home
[root@localhost home]# ls
lyj  lyj.java  test.java
```

### docker练习

> Docker  安装nginx

```bash
# 1.搜索镜像 search 
# 在docker hub上搜索nginx
# 2.下载镜像 pull
[root@localhost lyj]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete 
a9edb18cadd1: Pull complete 
589b7251471a: Pull complete 
186b1aaa4aa6: Pull complete 
b4df32aa5a72: Pull complete 
a0bcbecc962e: Pull complete 
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
[root@localhost lyj]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    605c77e624dd   4 months ago   141MB
centos       latest    5d0da3dc9764   8 months ago   231MB
# -d 后台运行 
# --name 给容器命令
# -p 宿主机端口，容器内部端口
[root@localhost lyj]# docker run -d --name nginx01 -p 3344:80 nginx
65c821bada5be37eddada13eb537c095857535a5393d9d45dc8505798c80f259

# 进入容器
[root@localhost lyj]# docker exec -it nginx01 /bin/bash
root@65c821bada5b:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@65c821bada5b:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@65c821bada5b:/# cd /ect/nginx
bash: cd: /ect/nginx: No such file or directory
root@65c821bada5b:/# cd /etc/nginx
root@65c821bada5b:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
```

> Docker布局tomcat

```bash
# 官方的使用
docker run -it --rm tomcat:9.0 #用来测试，用完即删
# 我们之前的启动都是后台，停止容器之后，容器还是可以查到
# 下载在启动
docker pull tomcat:9.0
# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat
# 测试访问没有问题

# 进入容器
[root@localhost lyj]# docker exec -it tomcat01 /bin/bash
# 发现问题：1.Linux命令太少了。 2.没有webapps. 阿里云镜像的原因，默认是最小的镜像，所以不必要的都剔除了
# 保证最小可运行镜像
```

> Docker 部署es+kibana

```bash
# es 暴露的端口很多！
# es 十分耗内存
# es 的数据一般需要放置到安全目录
# --net somenetwork ？ 网络设置

docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

# 启动了 linux就卡住了 docker stats 查看cpu的状态
# es 是十分耗内存的
# 查看 docker stats
```

```bash
# 关闭es,修改配置文件
```

####  可视化

*  portainer(先用这个)

```bash
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

* Rancher(CI/CD再用)

##### 什么portainer?

Docker图形化界面管理工具！提供一个后台面板供我们操作！

测试：访问外网

## Docker镜像原理

### 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含了运行某个软件所需的所有的内容，包括代码、运行时、库、环境变量和配置文件。

#### 如何得到镜像

* 从远程仓库下载
* 朋友拷贝给你
* 自己制作一个镜像DockerFile

### Docker 镜像加载原理

> UnionFS（联合文件系统）

**UnionFS（联合文件系统）：**UnionFS文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改，作为一次提交一层层的叠加，同时可以将不同的目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual file system）。Union文件系统所是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

**特性：**一次同时加载多个文件系统，但才外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这个层级的文件系统UnionFS。

bootfs(boot file system)主要包含bootloader和kernel，bootloader主要是引导加载kernel，linux刚启动后会加载bootfs文件系统，在docker镜像的最底层是bootfs。这一层与我们典型的linux/unix系统是一样的，包括boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system）,在bootfs智商，包含的就是典型的linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版本，比如ubuntu，centos等等

平时我们安装虚拟机的CentOs都是好几个g，为什么docker这里才几百兆

对于一个精简的os，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版本可用公用bootfs。

### 分层理解

> 分层的镜像

为什么Docker镜像要采用这种分层的结构？

```bash
[root@localhost lyj]# docker pull redis
Using default tag: latest
latest: Pulling from library/redis
a2abf6c4d29d: Already exists 
c7a4e4382001: Pull complete 
4044b9ba67c9: Pull complete 
c8388a79482f: Pull complete 
413c8bb60be2: Pull complete 
1abfd3011519: Pull complete 
Digest: sha256:db485f2e245b5b3329fdc7eff4eb00f913e09d8feb9ca720788059fdc2ed8339
Status: Downloaded newer image for redis:latest
docker.io/library/redis:latest
```

最大的好处，莫过于资源共享了！比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需要在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层可以通过docker image inspect命令！

```bash
[root@localhost lyj]# docker image inspect redis:latest
```

### commit镜像

```bash
docker commit 提交内容成为一个新的副本
# 命令和git类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[TAG]
```

**实践**

```bash
# 启动一个默认的tomcat
# 发现默认的tomcat是没有webapps应用的，原因在于官方的镜像webapps下的文件
# 将文件复制到webapps下
p -r webapps.dist/* webapps
# 将修改过的容器通过commit操作提交为一个镜像，以后就可以使用修改过的镜像
[root@localhost lyj]# docker commit -a="lyj" -m="add webapps app content" 79602de12c77 tomcat02:1.0
sha256:60a918d863158b5ceb76c17bac25c9e974b2e0f6e107255c6538b100e80549c7
[root@localhost lyj]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
tomcat02              1.0       60a918d86315   3 seconds ago   684MB
nginx                 latest    605c77e624dd   4 months ago    141MB
tomcat                9.0       b8e65a4d736d   4 months ago    680MB
tomcat                latest    fb5657adc892   4 months ago    680MB
redis                 latest    7614ae9453d1   4 months ago    113MB
centos                latest    5d0da3dc9764   8 months ago    231MB
portainer/portainer   latest    580c0e4e98b0   14 months ago   79.1MB
elasticsearch         7.6.2     f29a1ee41030   2 years ago     791MB
```

## 容器数据卷

### 什么是容器数据卷

**docker的理念：**将应用和环境打包成一个镜像

容器之间由一个数据共享的技术，Docker容器中产生的数据，可以同步到本地

卷技术：将容器目录挂载到linux上面

### 使用数据卷

> 直接使用命令挂载

```bash
docker run -it -v 主机目录：容器目录
[root@localhost home]# docker run -it -v /home/test:/home centos /bin/bash
#查看是否挂载成功
docker inspect 5ccb1ac870bf
```

> 实践：安装Mysql

```bash
# 下载镜像
[root@localhost /]# docker pull mysql:5.7
5.7: Pulling from library/mysql

# 运行容器，挂载数据
# 安装启动mysql需要设置密码
# 官方：  docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 启动成功
# -v 数据卷挂载
# -d 后台启动
# -p 端口映射
# -e 环境配置
# --name 容器名字
docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 启动成功之后，连接docker的mysql
# 通过navicat连接数据库
# 创建一个数据库，测试文件是否挂载成功
[root@localhost data]# ls
auto.cnf    client-cert.pem  ibdata1      ibtmp1              private_key.pem  server-key.pem
ca-key.pem  client-key.pem   ib_logfile0  mysql               public_key.pem   sys
ca.pem      ib_buffer_pool   ib_logfile1  performance_schema  server-cert.pem  test
# 容器删除之后，挂载的数据卷依旧没有丢失文件
```

### 具名和匿名挂载

```bash
# 匿名挂载
-v 容器内路径
-P 随机端口
docker run -d -P --name nginx01 -v /etc/nginx nginx

[root@localhost /]# docker volume ls
DRIVER    VOLUME NAME
local     d13150

# 剧名挂载
[root@localhost /]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
e35b3cf78a4964b5bcc6836c43c5e60665316c310e04580ee2ab42dde5473eb7
[root@localhost /]# docker volume ls
DRIVER    VOLUME NAME
local     d131501ea9c086991b05e9e59fa9902d265b46a339c15202c59bc9c023f4ab2f
local     juming-nginx
```

```bash
# 查看具名挂载的位置
[root@localhost /]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2022-05-14T15:49:21+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有docker容器内的卷，没有指定目录都是在`/var/lib/docker/volumes/xxx/_data`

通过具名挂载可以方便的找到卷，大多数使用的都是`具名挂载`

```bash
# 如何确定是具名挂载合适匿名挂载，还是指定路径挂载
-v 容器内路径  #匿名挂载
-v 卷名：容器内路径 #具名挂载
-v /宿主机路径：容器内路径 #指定路径挂载
```

**拓展：**

```bash
# 通过 -v 容器内路径 ro rw 改变读写权限
# ro  readonly 只读
# rw  readwrite 可读可写
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

#ro 说明这个路径只能通过宿主机来操作，容器内部无法操作
```

### 初识DockerFile

DockerFile就是用来构建docker镜像的构建文件！

通过脚本生成镜像，镜像是一层一层的，脚本是一个个命令，每一个命令就是一层

```bash
# 创建一个dockerfile文件，名字可以随便
# 文件中的内容 指令(大写) 参数
FORM centos

VOLUME ["volume01","volume02"]

CMD echo "----end----"
CMD /bin/bash
```

```bash
[root@localhost docker-test-volume]# docker build -f /home/docker-test-volume/dockerfile1 -t lyj/centos:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 5d0da3dc9764
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in adf475a0af98
Removing intermediate container adf475a0af98
 ---> 4dd2b9a5d3cc
Step 3/4 : CMD echo "----end----"
 ---> Running in 3f617792852a
Removing intermediate container 3f617792852a
 ---> 6b2f0b022f0d
Step 4/4 : CMD /bin/bash
 ---> Running in 216f3f309b22
Removing intermediate container 216f3f309b22
 ---> ac1b086b26d7
Successfully built ac1b086b26d7
Successfully tagged lyj/centos:1.0
```

**启动自己的容器**

```bash
 docker run -it ac1b086b26d7 /bin/bash
```

**自己容器挂载的路径**

```bash
"Mounts": [
            {
                "Type": "volume",
                "Name": "c9e87917b6ac7b8b8e659dc3aa18454c67480f8b18328873c8eb4005126cc260",
                "Source": "/var/lib/docker/volumes/c9e87917b6ac7b8b8e659dc3aa18454c67480f8b18328873c8eb4005126cc260/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "6716d8d8d2108ef678bd3d68632ec45a1f8b1086a3e9cab08965c0b73edde3f4",
                "Source": "/var/lib/docker/volumes/6716d8d8d2108ef678bd3d68632ec45a1f8b1086a3e9cab08965c0b73edde3f4/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
```

### 数据卷容器

多个容器同步数据

```bash
# 启动三个镜像，通过我们自己写的镜像去启动

[root@localhost lyj]# docker run -it --name docker01 lyj/centos:1.0
[root@af22ef053837 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var       volume02
dev  home  lib64  media       opt  root  sbin  sys  usr  volume01
[root@af22ef053837 /]# ls -l
total 0
lrwxrwxrwx.   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root 360 May 17 02:18 dev
drwxr-xr-x.   1 root root  66 May 17 02:18 etc
drwxr-xr-x.   2 root root   6 Nov  3  2020 home
lrwxrwxrwx.   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx.   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------.   2 root root   6 Sep 15  2021 lost+found
drwxr-xr-x.   2 root root   6 Nov  3  2020 media
drwxr-xr-x.   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x.   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x. 186 root root   0 May 17 02:18 proc
dr-xr-x---.   2 root root 162 Sep 15  2021 root
drwxr-xr-x.  11 root root 163 Sep 15  2021 run
lrwxrwxrwx.   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x.   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x.  13 root root   0 May 17 02:13 sys
drwxrwxrwt.   7 root root 171 Sep 15  2021 tmp
drwxr-xr-x.  12 root root 144 Sep 15  2021 usr
drwxr-xr-x.  20 root root 262 Sep 15  2021 var
drwxr-xr-x.   2 root root   6 May 17 02:18 volume01
drwxr-xr-x.   2 root root   6 May 17 02:18 volume02
```

删除docker01的容器之后数据依然存在

> 容器之间买配置信息的传递，数据卷容器的生命周期一致持续到没有容器使用为止

## DockerFile

### DokerFile介绍

dockerFile是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1. 编写一个dockerfile文件
2. docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像(Docker Hub、阿里云镜像仓库)

`很多官方镜像都是基础包，很多功能没有，我们通常会自己搭建镜像`

### DockerFile构建过程

**基础知识：**

1、每个保留关键字(指令)都是必须大写字母

2、执行顺序是从上到下的

3、# 表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交！

dockerFile是面向开发的，以后发布项目，做镜像，就需要编写dockerfile文件，这个文件很简单，dockerfil逐渐成为企业交付的标准，必须要掌握！

步骤：开发、部署、运维缺一不可

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过dockerfile构建生成的镜像，最终发布和运行的产品

Docker容器：容器就是镜像运行起来提供服务器

### 指令：

```bash
FROM	# 基础镜像，一切从这里开始构建
MAINTAINER	# 镜像是谁写的，姓名+邮箱
RUN		# 镜像构建的时候需要运行的命令
ADD 	# 步骤，tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR 	# 镜像的工作目录
VOLUEM 		# 挂载的目录
EXPOSE 		# 保留端口配置
CMD			# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT  # 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD 	# 当构建一个被继承DockerFile这个时候就会运行ONBUILD的指令
COPY 		# 类似ADD,将我们的文件拷贝到镜像中
ENV 		# 构建的时候设置环境变量！
```

> 创建一个属于自己的centos

```bash
# 编写dockerfile的文件
[root@localhost dockerfile]# vim mydockerfile-centos
[root@localhost dockerfile]# cat mydockerfile-centos 
FROM centos
MAINTANER lyj-2063074967@qq.com
ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN  yum -y install vim
RUN  yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

# 构建镜像
[root@localhost dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .

# 测试运行
docker run -it mycentos:0.1

# 查看历史变更
ot@localhost dockerfile]# docker history 5d0da3dc9764
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
5d0da3dc9764   8 months ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      8 months ago   /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B        
<missing>      8 months ago   /bin/sh -c #(nop) ADD file:805cb5e15fb6e0bb0…   231MB   
```

> CMD 和 ENTRYPOINT 区别

测试cmd

```bash
[root@localhost dockerfile]# vim dockerfile-cmd-test
# 脚本
FROM centos:7
CMD ["ls","-a"]

# 构建
[root@localhost dockerfile]# docker build -f dockerfile-cmd-test  -t cmdtest .

# 命令生效
[root@localhost dockerfile]# docker run 744c3d93e4a3
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# 想追加命令 il, ls -al
[root@localhost dockerfile]# docker run 744c3d93e4a3 -l
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-l": executable file not found in $PATH: unknown.

# cmd情况下-l 替代了ls -a
```

测试ENTRYPOINT

```bash
[root@localhost dockerfile]# vim dockerfile-cmd-entryponit
# 脚本
FROM centos:7
ENTRYPOINT ["ls","-a"]

[root@localhost dockerfile]# docker build -f dockerfile-cmd-entryponit -t entrypoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos:7
 ---> eeb6ee3f44bd
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in 84619c168165
Removing intermediate container 84619c168165
 ---> a0a8da7d7890
Successfully built a0a8da7d7890
Successfully tagged entrypoint-test:latest
[root@localhost dockerfile]# docker run a0a8da7d7890
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
[root@localhost dockerfile]# docker run a0a8da7d7890 -l
total 12
drwxr-xr-x.   1 root root     6 May 17 03:33 .
drwxr-xr-x.   1 root root     6 May 17 03:33 ..
-rwxr-xr-x.   1 root root     0 May 17 03:33 .dockerenv
-rw-r--r--.   1 root root 12114 Nov 13  2020 anaconda-post.log
lrwxrwxrwx.   1 root root     7 Nov 13  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   340 May 17 03:33 dev
drwxr-xr-x.   1 root root    66 May 17 03:33 etc
drwxr-xr-x.   2 root root     6 Apr 11  2018 home
lrwxrwxrwx.   1 root root     7 Nov 13  2020 lib -> usr/lib
lrwxrwxrwx.   1 root root     9 Nov 13  2020 lib64 -> usr/lib64
drwxr-xr-x.   2 root root     6 Apr 11  2018 media
drwxr-xr-x.   2 root root     6 Apr 11  2018 mnt
drwxr-xr-x.   2 root root     6 Apr 11  2018 opt
dr-xr-xr-x. 187 root root     0 May 17 03:33 proc
dr-xr-x---.   2 root root   114 Nov 13  2020 root
drwxr-xr-x.  11 root root   148 Nov 13  2020 run
lrwxrwxrwx.   1 root root     8 Nov 13  2020 sbin -> usr/sbin
drwxr-xr-x.   2 root root     6 Apr 11  2018 srv
dr-xr-xr-x.  13 root root     0 May 17 02:13 sys
drwxrwxrwt.   7 root root   132 Nov 13  2020 tmp
drwxr-xr-x.  13 root root   155 Nov 13  2020 usr
drwxr-xr-x.  18 root root   238 Nov 13  2020 var
```

Dockerfile中很多命令都十分相似，我们需要了解它们的区别，我的最好的学习就是对比测试

### 实践：Tomcat镜像

1、准备镜像文件tomcat压缩包，jdk的压缩包

2、准备dockerfile文件，官方命令`Dockerfile`,build时会自动寻找这个文件，就不需要-f指定了

```bash
FROM centos:7
MAINTAINET lyj-2063074967@qq.com

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u181-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.63.tar.gz /usr/lcoal/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_181
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.63
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.63
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.63/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.63/bin/logs/catalina.out
```

3、构建镜像

```bash
# docker build -t diytomcat .
```

## Docker网络

### 理解Docker0

清空所有环境（镜像和容器）

```bash
# docker 如何处理容器网络访问

[root@localhost /]# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址 ip addr
# linux可以Ping 容器内部
```

> 原理

1、每启动一个docker容器，docker就会给docker容器分配一个ip,只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是evth-pair技术

2、在启动一个容器又多了一个网卡

```bash
[root@localhost /]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:32:a0:bc brd ff:ff:ff:ff:ff:ff
    inet 192.168.108.110/24 brd 192.168.108.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::32c9:7680:6e1:ea04/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:c7:4f:55 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:c7:4f:55 brd ff:ff:ff:ff:ff:ff
5: br-11e4c4d39e6c: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:20:e8:3d:98 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-11e4c4d39e6c
       valid_lft forever preferred_lft forever
6: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:cc:cd:5d:3d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:ccff:fecd:5d3d/64 scope link 
       valid_lft forever preferred_lft forever
8: vethbd6aa9d@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether ba:cc:e4:b3:69:1e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::b8cc:e4ff:feb3:691e/64 scope link 
       valid_lft forever preferred_lft forever
10: veth9702194@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 96:99:e6:6c:aa:b6 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::9499:e6ff:fe6c:aab6/64 scope link 
       valid_lft forever preferred_lft forever
```

容器带来的网卡，都是一对的，evth-pair就是一对虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连，正因为这个特性，evth-pari充当一个桥接，连接各种虚拟网络设备的，Docker容器之间的连接，都是使用evth-paur技术

### link

```bash
docker run -P  --name tomcat03 --link tomcat02 tomcat
```

```bash
[root@localhost /]# docker exec -it tomcat02 cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.3      555cc43e71d6
```

link的本质：就是在hosts配置增加了171.17.0.3 容器id

### 自定义网络

> 查看所有docker 网络

```ba
[root@localhost /]# docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
835678176bfb   bridge        bridge    local
33bd4092d423   host          host      local
c67b96605b65   none          null      local
11e4c4d39e6c   somenetwork   bridge    local
```

#### 网络模式

bridge：桥接docker（默认）自己创建网络也是用bridge模式

none：不配置网络

host：和宿主机共享网络

container：容器网络联通（用的少，局限很大）

**测试**

```bash
# 直接启动的命令， --net bridge,而这个就docker0
docker run -d -P --name tomcat01
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点，默认，域名不用访问，--link可以打通连接

# 自定义一个网络
# --diver 桥接模式
# --subnet 子网
# --gateway 网关
[root@localhost /]# docker network create --driver bridge  --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
dc5bf1caafaa16013e9f9f0654982ed97cd589713f5925ddbd2908c0ccb1351b
[root@localhost /]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
835678176bfb   bridge    bridge    local
33bd4092d423   host      host      local
dc5bf1caafaa   mynet     bridge    local
c67b96605b65   none      null      local

# 
```

```shell
[root@localhost /]# docker run -d -P --name tomcat-net-01 --net mynet
"docker run" requires at least 1 argument.
See 'docker run --help'.

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container
[root@localhost /]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
a07c51438812bcfc529fd503c929866a6f77d30a7e8716c72f9548ffc5025877
[root@localhost /]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
ecdda4c64e007c8c7ef24de2dd581cbe967593c2fc730805f769c68930b82f6a
[root@localhost /]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "dc5bf1caafaa16013e9f9f0654982ed97cd589713f5925ddbd2908c0ccb1351b",
        "Created": "2022-05-18T11:06:01.965660686+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "a07c51438812bcfc529fd503c929866a6f77d30a7e8716c72f9548ffc5025877": {
                "Name": "tomcat-net-01",
                "EndpointID": "de83ba706e481876e2973336408e5c071da47133ce309c7801bf8935bb391618",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "ecdda4c64e007c8c7ef24de2dd581cbe967593c2fc730805f769c68930b82f6a": {
                "Name": "tomcat-net-02",
                "EndpointID": "066c8632e98ebfb0d8cd3ebc3da9bc28df18749532ebd71fda9561e810e0a49b",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

自定义的网络docker已经帮我们维护好了对应的关系

好处：

redis-不同的集群使用不同的网络，保证了集群是安全和健康的

mysql-不同的集群使用不同的网络，保证了集群是安全和健康的

### 网络连通

```shell
[root@localhost /]# docker network --help

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.
```

```shell
# 测试打通tomcat01到mynet
[root@localhost /]# docker network connect mynet tomcat01

# 连通之后就是tomcat01放到了mynet网络下
[root@localhost /]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "dc5bf1caafaa16013e9f9f0654982ed97cd589713f5925ddbd2908c0ccb1351b",
        "Created": "2022-05-18T11:06:01.965660686+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "3e25bc8a0e7586f3105c12ca550d5a28b0407ba5aa9bd362c4993422eb1729dd": {
                "Name": "tomcat01",
                "EndpointID": "5e6c258c064273d14450c2acfb98a34455524b11af4980378b62de1ae2249271",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "a07c51438812bcfc529fd503c929866a6f77d30a7e8716c72f9548ffc5025877": {
                "Name": "tomcat-net-01",
                "EndpointID": "de83ba706e481876e2973336408e5c071da47133ce309c7801bf8935bb391618",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "ecdda4c64e007c8c7ef24de2dd581cbe967593c2fc730805f769c68930b82f6a": {
                "Name": "tomcat-net-02",
                "EndpointID": "066c8632e98ebfb0d8cd3ebc3da9bc28df18749532ebd71fda9561e810e0a49b",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

```bash
# 移除网络
[root@localhost /]# docker network rm somenetwork
```

### 部署redis集群

创建redis网卡

```shell
[root@localhost /]# docker network create redis --subnet 172.38.0.0/16
```

通过脚本创建六个reids配置

```shell
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379 
bind 0.0.0.0
cluster-enabled yes 
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done
```

```shell
# 启动容器
for port in $(seq 1 6); \
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf; \
done

#进入一个容器
docker exec -it redis-1 /bin/sh

# 创建集群
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: 9d90cb6cfe72a617664d5cc5ed83f654c2e9865b 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: ff448dc8dea2517b2c77b2558e04cbeda7fab184 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: fc808d7350a36679cdc645dbaa8ec18702ca9e99 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: a595255f05ffe1317a217143bc87ec97cc287432 172.38.0.14:6379
   replicates fc808d7350a36679cdc645dbaa8ec18702ca9e99
S: 88f5fca469eab471e64edebb7a75f4422c9ada08 172.38.0.15:6379
   replicates 9d90cb6cfe72a617664d5cc5ed83f654c2e9865b
S: 73f4b703c96d5c36e5480574bee473aef9914da8 172.38.0.16:6379
   replicates ff448dc8dea2517b2c77b2558e04cbeda7fab184
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: 9d90cb6cfe72a617664d5cc5ed83f654c2e9865b 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: ff448dc8dea2517b2c77b2558e04cbeda7fab184 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: a595255f05ffe1317a217143bc87ec97cc287432 172.38.0.14:6379
   slots: (0 slots) slave
   replicates fc808d7350a36679cdc645dbaa8ec18702ca9e99
S: 73f4b703c96d5c36e5480574bee473aef9914da8 172.38.0.16:6379
   slots: (0 slots) slave
   replicates ff448dc8dea2517b2c77b2558e04cbeda7fab184
S: 88f5fca469eab471e64edebb7a75f4422c9ada08 172.38.0.15:6379
   slots: (0 slots) slave
   replicates 9d90cb6cfe72a617664d5cc5ed83f654c2e9865b
M: fc808d7350a36679cdc645dbaa8ec18702ca9e99 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
```

### SpringBoot项目打包Docker镜像

1、构建springboot项目

2、打包应用

3、编写Dockerfile

4、构建镜像

5、发布运行

## Docker Compose

### 简介：

Docker

DockerFile build run 手动操作，单个容器！

微服务，100个微服务！依赖关系

Docker Compose 来轻松高效的管理容器，定义运行多个容器。

Compose是docker官方的开源项目，需要安装，

`Dockerfile`让程序可以在任何地方运行，web服务、redis、MySQL、nginx...多个容器

Compose

```shell
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

docker-compose -up 100个服务

Compose：重要的概念

* 服务services，容器，应用。（web、redis、mysql）
* 项目project，一组关联的容器。博客，web,mysql

### Compose 安装

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.5.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

授权

```shell
chmod +x docker-compose

[root@localhost bin]# docker-compose version
Docker Compose version v2.5.0
```

### 体验

python应用，计数器，redis。

```shell
 mkdir composetest
 cd composetest
 
 vim app.py
 
 # 文件内容
 import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
    
vim requirements.txt

# 文件内容
flask
redis

vim Dockerfile

# 文件内容
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
RUN echo -e http://mirrors.ustc.edu.cn/alpine/v3.12/main/ > /etc/apk/repositories
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --no-cache-dir -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]

vim docker-compose.yml

# 文件内容
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
    
# 运行
docker-compose up
```

### yaml 规则

docker-compose.yaml核心

```shell
# 3层

version: '' #版本
services: #服务
	服务1： web
		# 服务配置
		images
		build
		network
	服务2： redis
	服务3： redis
# 其他配置 网络/卷、 全局规则
```

### 开源项目

下载程序，安装数据库、配置……

compose应用。=》一键启动

```shell
mkdir my_wordpress
cd my_wordpress
vim docker-compose.yml
# 文件内容
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

前台启动

```shell
docker -d
docker-compose -d
[+] Running 2/2
 ⠿ Container my_wordpress-db-1         Started                                                         0.5s
 ⠿ Container my_wordpress-wordpress-1  Started                                                         1.2s
```

### 测试：

```java
@RestController
public class HelloController {

    @Autowired
    StringRedisTemplate redisTemplate;

    @GetMapping("/hello")
    public String hello(){
        Long views = redisTemplate.opsForValue().increment("views");
        return  "hello lyj views:" + views;
    }
}
```

```properties
# application.propertiees
server.port=8080
spring.redis.host=redis
```

```shell
# Dockerfile
FROM java:8

COPY *.jar /app.jar

CMD ["--server.port=8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

```shell
# docker-compose.yml
version: "3.9"
services:
  lyjapp:
    build: .
    image: lyj
    depends_on:
      - redis
    ports:
    - "8080:8080"
  redis:
    image: "library/redis:alpine"
```

项目要重新打包

```shell
docker-compose up --build
```