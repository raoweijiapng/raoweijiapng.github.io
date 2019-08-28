---
title: 流程图、思维导图、tcp/ip协议、qq安装以及nodejs安装
tags: tcp ip
categories: tcp ip
---

* TOC
{:toc}

- 流程图

![流程图](https://raoweijiapng.github.io/static/img/liuchengtu.png)

流程图是流经一个系统的信息流、观点流或部件流的图形代表。在企业中，流程图主要用来说明某一过程。这种过程既可以是生产线上的工艺流程，也可以是完成一项任务必需的管理过程。
流程图有时也称作输入-输出图。该图直观地描述一个工作过程的具体步骤。流程图对准确了解事情是如何进行的，以及决定应如何改进过程极有帮助

![图框含义](https://raoweijiapng.github.io/static/img/kuangtuhanyi.jpg)

- 思维导图

![图框含义](https://raoweijiapng.github.io/static/img/siweidaotu.jpg)

思维导图，英文是The-Mind-Map，又叫心智导图，是表达发散性思维的有效图形思维工具，它简单却又很有效，是一种实用性的思维工具。
思维导图又称脑图、心智地图、脑力激荡图、灵感触发图、概念地图、树状图、树枝图或思维地图，是一种图像式思维的工具以及一种利用图像式思考辅助工具。思维导图是使用一个中央关键词或想法引起形象化的构造和分类的想法；它用一个中央关键词或想法以辐射线形连接所有的代表字词、想法、任务或其它关联项目的图解方式。

# tcp/ip协议
TCP/IP传输协议，即传输控制/网络协议，也叫作网络通讯协议。  
![图框含义](https://raoweijiapng.github.io/static/img/七层协议.png)
TCP/IP协议是Internet最基本的协议,其中应用层的主要协议有Telnet、FTP、SMTP等，是用来接收来自传输层的数据或者按不同应用要求与方式将数据传输至传输层；传输层的主要协议有UDP、TCP，是使用者使用平台和计算机信息网内部数据结合的通道，可以实现数据传输与数据共享；网络层的主要协议有ICMP、IP、IGMP，主要负责网络中数据包的传送等；而网络访问层，也叫网路接口层或数据链路层，主要协议有ARP、RARP，主要功能是提供链路管理错误检测、对不同通信媒介有关信息细节问题进行有效处理等。

## tcp与udp

- UDP协议定义了端口，同一个主机上的每个应用程序都需要指定唯一的端口号，并且规定网络中传输的数据包必须加上端口信息，当数据包到达主机以后，就可以根据端口号找到对应的应用程序了。UDP协议比较简单，实现容易，但它没有确认机制，数据包一旦发出，无法知道对方是否收到，因此可靠性较差，为了解决这个问题，提高网络可靠性，TCP协议就诞生了。

- TCP即传输控制协议，是一种面向连接的、可靠的、基于字节流的通信协议。简单来说TCP就是有确认机制的UDP协议，每发出一个数据包都要求确认，如果有一个数据包丢失，就收不到确认，发送方就必须重发这个数据包。 

## tcp优点

1. 协议标准是完全开放的，可以供用户免费使用，并且独立于特定的计算机硬件与操作系统。
2. 独立于网络硬件系统，可以运行在广域网，更适合于互联网。
3. 网络地址统一分配，网络中每一设备和终端都具有一个唯一地址。
4. 高层协议标准化，可以提供多种多样可靠网络服务。

## tcp缺点

1. 该模型没有明显地区分服务、接口和协议的概念。因此，对于使用新技术来设计新网络，TCP/IP模型不是一个太好的模板。
2. TCP/IP模型完全不是通用的，并且不适合描述除TCP/IP模型之外的任何协议栈。
3. 链路层并不是通常意义上的一层。它是一个接口，处于网络层和数据链路层之间。接口和层间的区别是很重要的。
4. TCP/IP模型不区分物理层和数据链路层。这两层完全不同，物理层必须处理铜缆、光纤和无线通信的传输特征；而数据链路层的工作是确定帧的开始和结束，并且按照所需的可靠程度把帧从一端发送到另一端。

# QQ的安装

- 安装 wine
```
  git clone https://gitee.com/wszqkzqk/deepin-wine-for-ubuntu.git
  cd deepin-wine-for-ubuntu 
  sudo sh install.sh
```

- 安装 QQ
```
  wget http://mirrors.aliyun.com/deepin/pool/non-free/d/deepin.com.qq.im/deepin.com.qq.im_8.9.19983deepin23_i386.deb
  sudo dpkg -i deepin.com.qq.im_8.9.19983deepin23_i386.deb 
  sudo apt-get install -f
```

# nodejs的安装

- 方法一
```
  curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
  sudo apt-get install -y nodejs
```

- 方法二

1. 下载压缩包
  wget https://nodejs.org/dist/v8.1.0/node-v8.1.0-linux-x64.tar.xz

2. 解压
  tar -xvf node-v8.1.0-linux-x64.tar.xz

3. 切换并查看当前node所在路径
  cd node-v8.1.0-linux-x64/bin
  pwd

4. 查看node版本
  ./node -v

5. 将node和npm设置为全局
  sudo ln /home/ubuntu/node-v8.1.0-linux-x64/bin/node /usr/local/bin/node
  sudo ln /home/ubuntu/node-v8.1.0-linux-x64/bin/npm /usr/local/bin/npm
  pwd

推荐使用方法一，直接安装在系统环境/usr/bin目录下，之后使用npm -g安装其他插件也会安装到/usr/lib/node_modules’(需要使用sudo权限)‘。如果使用方法二，将nodejs路径链接到/usr/local/bin目录下，则每次npm-g安装插件都会安装在nodejs原路径下的node_modules(比如/home/ubuntu/node-v8.1.0-linux-x64/lib/node_modules)，每次代码中引用插件也需要到此目录下去找。

