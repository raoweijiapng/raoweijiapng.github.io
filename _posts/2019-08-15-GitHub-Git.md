---
title: 基于jekyll使用git连接github搭建个人博客
tags: git github jekyll
categories: blog
---

* TOC
{:toc}

​		首先install Git ，注册GitHub账号，创建公共库，然后进入jekyll官网下载个人博客的主题，最后使用git指令在终端上传到github，这时可以登陆raoweijiapng.github.io进入个人博客。
- 终端安装Git指令

```
sudo apt-get install git
```

- 注册一个github

![注册](https://raoweijiapng.github.io/static/img/git/zhuce.png)

新建一个公共库，库名为账号名.github.

![git库](https://raoweijiapng.github.io/static/img/git/git-ku.webp)

# 用git连接Github

- 生成SSH秘钥

  ​在终端输入：ssh-keygen -t rsa -C “raoweijia@outlook.com”，其中“raoweijia@outlook.com”是你的邮箱地址。

  接着按3个回车:

  ​![生成秘钥](https://raoweijiapng.github.io/static/img/git/秘钥.png)

  最后在.ssh目录下得到了两个文件：id_rsa（私有秘钥）和id_rsa.pub（公有密钥）。


- 添加SSH Key到GitHub

  首先，去.ssh目录下找到id_rsa.pub这个文件夹打开复制全部内容。


  接着在网页上打开你的GitHub，进入你的Settings：

  ![setting](https://raoweijiapng.github.io/static/img/git/setting.png)

  然后在网页的左边会看到这些目录，点击SSH and GPG keys:
  
  ![SSH and GPG keys](https://raoweijiapng.github.io/static/img/git/ssh.png)

  创建New SSH key:

  ![New SSH key](https://raoweijiapng.github.io/static/img/git/new-ssh-key.png)

  粘贴你的密钥到你key输入框中:
  
  ![New SSH key](https://raoweijiapng.github.io/static/img/git/copy-key.png)
  
  点击“Add SSH key”，会提示输入GitHub用户密码，输入后，即可完成。

- 测试密钥是否可行

  ​在命令窗口上输入ssh -T **git@github.com** 按回车键，如看到以下信息:

  ![测试秘钥](https://raoweijiapng.github.io/static/img/git/ceshi-miyao.png)

​	那么，生成秘钥就成功了。

- 创建本地库

  ​	第一种方法:在本地某个文件中增加后缀为.github.io的文件夹 然后在此文件中执行git init。

  ​	第二种方法：克隆github的空白库到指定的位置。

  ```
  git clone https://github.com/ *用户名* / *用户名* .github.io	
  ```

- 下载jekyll主题

  ​	去官网http://jekyllthemes.org/ ，下载压缩包。

- 拷贝主题文件到克隆的库文件

  ​把压缩包进行解压，然后把解压下的文件拷贝到自己生成的raoweijia.github.io文件夹中，然后在raoweijia.github.io的文件夹里打开终端。

  通过git指令把本地库的文件上传到GitHub的相应后缀为账户名.github.io的公共库中。

  ```
  ​git init

  git config --global user.name "raoweijiapng"
  ​git config --global user.email raoweijia@outlook.com

  git remote add origin https://github.com/Chenzhiyong47/pyflask.git（地址是自己的GitHub库的地址）
  		
  ​git add . （注意：git add 后面有一个小数点）

  git commit -m "It's my first time to send documents to GitHub in Linux."  （双引号里内容必须要 写，但内容不限）。
  ​			
  git push origin master   
  （如果第一次上传失败，在末尾的加-f参数是强制性上传，若GitHub上面没有文件与Linux上冲突，可以不要这个参数；若GitHub上面有文件与这个冲突，则加上这个参数。加上这个参数的作用：删掉GitHub上原有的文件，再上传Linux上的文件）。

  最后按提示输入GitHub的账号和密码。等待一会儿。
  ```
  那么现在就可以上GitHub查看自己刚上传的文件了。

  最后启动浏览器并转到https：// 账户名.github.io，就进入到自己的blog。