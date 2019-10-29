---
title: 安装Ubuntu操作系统
tags: Ubuntu markdown
categories: linux
---

* TOC
{:toc}
​		主流的Linux发行版　Ubuntu， DebianGNU/Linux ，Fedora，Gentoo，MandrivaLinux ，PCLinuxOS，SlackwareLinux ，openSUSE，ArchLinux，Puppylinux，Mint,CentOS,Red Hat等Ubuntu是一个以桌面应用为主的Linux操作系统。

# 安装Ubuntu操作系统

- 下载ubuntu的ISO文件

  到ubuntu的官方网站，去下载到ubuntu 16.04的iso文件。

- 下载ultraiso工具

​		 ultraiso工具可以用来制作U盘启动盘，打开ultraiso软件，在菜单栏【文件】-【打开】，选择下载的ISO文件。然后菜单栏里【启动】-【写入硬盘映像】，选择了写入硬盘映像后，弹出窗口。在窗口中的硬盘驱动器选择你插入的U盘，点击【写入】开始制作U盘的启动盘，U盘里面以前的东西都会被格式化了。需要经过几分钟，进度条到100%，那就是启动盘制作成功了。

- 装系统

​        把制作好的U盘启动盘插入需要安装操作系统的那台电脑上面之后，重新启动或者开机。在电脑重新开启或关机时，按F12或者F2键进入BIOS系统设置启动项目为USB。装完之后$sudo apt-get update。如果网卡不好使，就重启网卡，sudo ifconfig eth0 down（关闭）、sudo ifconfig eth0 up。

# 了解Makdown语言

- 使用 **#**,可以表示 **1-6级** 标题。

  示例md代码:

  ```
  # 第一级标题 `<h1>` 
  
  ## 第二级标题 `<h2>` 
  
  ### 第三级标题 `<h3>` 
  
  #### 第二四级标题 `<h4>` 
  
  ##### 第五级标题 `<h5>` 
  
  ###### 第六级标题 `<h6>` 
  ```

示例效果：

# 第一级标题   
## 第二级标题   
### 第三级标题  
#### 第四级标题  
##### 第五级标题   
###### 第六级标题

- 段落：段落的前后要有空行，所谓的空行是指没有文字内容。若想在段内强制换行的方式是使用两个以上空格加上回车。

- 在段落的每行或者只在第一行使用符号 **>** ,还可使用多个嵌套引用。

  示例md代码:

  ```
  > 区块引用
  > > 嵌套引用
  > > >三嵌套引用
  > > > > 四嵌套引用
  ```

示例效果：

> 区块引用
>> 嵌套引用
>>> 三嵌套引用
>>>> 四嵌套引用

- 代码区块的建立是在每行加上4个空格或者一个制表符（如同写代码一样）。

- 使用 **.** 、**+**、或**-** 标记无序列表。

- 分割线最常使用就是三个或以上的 `*` ， `======`</font>还可以使用 `-` 和 `_`。

- 链接可以由两种形式生成，**行内式** 和 **参考式**。

  行内式：

  示例md代码:

  ```
  [GitHub](http://github.com)
  自动生成连接  <http://www.github.com/>
  ```

  超链接：

  ```
  [GitHub][1]
  [1]:http://github.com
  自动生成连接  <http://www.github.com/>
  ```

- 添加图片形式和链接相似，只需要在链接的基础上前方加一个 **！**号。

  示例md代码:
  
  ```
  ![blockchain](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
  u=702257389,1274025419&fm=27&gp=0.jpg "区块链")
  ```

# 安装typora		

```
wget  -qO  -  https://typora.io/linux/public-key.asc | sudo apt-key add -

sudo add-apt-repository'deb https://typora.io/linux ./'

sudo apt-get update

sudo apt-get install typora
```





