---
layout: post
title: 利用docker安装oracle
tags: docker oracle
categories: oracle
---

* TOC
{:toc}

# 安装docker

```
  sudo apt-get update
```
- 移除旧版本的 Docker

```
  sudo apt-get remove docker docker-engine docker.io
```

- 安装以下软件包以允许apt通过HTTPS使用仓库

```
  sudo apt-get install apt-transport-https ca-certificates curl
```

- 添加Docker的官方GPG密钥

```
  curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

- 验证密钥是不是9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

```
  sudo apt-key fingerprint 0EBFCD88
```
- 正确密钥应该如下显示

```
   pub   4096R/0EBFCD88 2017-02-22
   Key fingerprint =9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
   uid                  Docker Release (CE deb) docker@docker.com
   sub   4096R/F273FCD8 2017-02-22 
```

- 添加 Docker 官方仓库、切换root用户执行

```
  sudo su
  echo -e "deb [arch=amd64] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian stretch     stable" >> /etc/apt/sources.list
```

- 安装 Docker-CE

```
  sudo apt-get update && sudo apt-get install docker-ce -y
```

# 安装oracle

- 切换到root账号下，如果是普通账号，下面操作指令前面加sudo

- 搜索oracle镜像

```
  docker search oracle
```

- 下载oracle镜像

```
  docker pull registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```

- 查看镜像

```
  docker images
  docker images registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```

- 从下载的镜像创建并启动容器

```
  docker run -d -p 1521:1521 --name oracle11 registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
  这里把新建的容器命名为oracle11
```

- 查看当前运行的容器

```
  docker ps
```

- 进入已经启动的Oracle的容器

```
  docker exec -it oracle11 bash
```

- 进入sqlplus

```
  sqlplus /nolog
```

- 使用sysdba角色登录sqlplus

```
  conn sys/oracle as sysdba
```

- 退出sqlplus

```
  exit
```

- 退出容器

```
  exit 或Ctrl+D
```

- 重新进入已经运行的oracle容器,重新执行下面指令

```
  docker exec -it oracle11 bash
```

- 如果容器停用了，则重新启动, 用你实际的容器ID代替下面的container_ID

```
 docker start container_ID
```
- 停用正在运行的容器

```
  docker stop container_ID
```
