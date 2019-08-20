---
title: ssh连接远程库和一些命令分享
tags: ssh git
categories: ssh git
---

* TOC
{:toc}

​今天主要学习markdown和git语言，

# Git 退回以前的仓库版本
```
// 本地新建old_master分支做备份  
$ git branch old_master  
// 将备份分支push到远程仓库  
$ git push origin old_master:old_master  
// 本地仓库回退到某个版本如 bae168  
$ git reset –-hard bae168  
// 删除远程仓库的master分支  
$ git push origin :master  
// 重新创建远程仓库master分支  
git push origin master
最后，要求所有成员删除本地仓库master分支，重新抓取避免执行 git push 后，又将远程版本更新。
```
 	
# 生成记事本快捷方式
在终端中：sudo degit
然后在“模板”中加入某种格式的空白文档

# 使用SSH方式连接远程仓库
    1. 生成SSH公钥
    ssh-keygen -t rsa -C "raoweijiapng"
    2. 进入公钥的保存路径，找到id_rsa.pub公钥文件，用记事本打开，将里面的内容全部复制到github中。
    登录后一次点击settings->SSH and GPG keys->New SSH key，然后将填写key的名称，将id_rsa.pub文件的内容全部复制到填写公钥的输入框中即可。
    3. 添加别名，别名绑定的远程仓库地址是SSH方式的地址
    如果别名已经存在，我们要先删除,再重新添加。
    git remote rm mystore
    git remote add mystore "git@github.com:18357136930/chenycstore.git"
    最后就可以使用git命令和远程仓库进行交互。

# 测试
```
 # fewfew   一级标题

### 饶唯甲   三级标题

### 2019-08-19   三级标题

### 今天任务     三级标题

1. markdown语法    有序列表
2. git命令
3. 博客加了音乐

#### 明日计划      三级标题
1. 复习git        有序列表
2. 复习markdown

#### 今天学习了markdown语言，和git的语言          三级标题

#### markdown事例                             三级标题

**shili**            粗体
*shili*              斜体
***shili***          粗斜体

|dsds|dsad|da|       列表
|--:|:--:|--|
|dwqd|dqwd|dqwd|

![baidu](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=1690699292,1481547313&fm=26&gp=0.jpg)        网络图片

![baidu](/home/raoweijia/图片/1.png)            本地图片

\`\`\`
dfqwjdwemdw            代码块
dwqdwqdwdq
dwqd
dwed
\`\`\`
``` 

