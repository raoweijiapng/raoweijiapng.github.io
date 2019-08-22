---
title: 基于jekyll使用git连接githut搭建个人博客
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

![img](https://img-blog.csdn.net/20180101101023382?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFuZ2hhbmdf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

新建一个公共库，库名为账号名.github.

# 用git连接Github

- 生成SSH秘钥

  ​        在终端输入：ssh-keygen -t rsa -C “raoweijia@outlook.com”，其中“raoweijia@outlook.com”是你的邮箱地址。
  ​								    ![æç¨ï¼ç¬¬ä¸æ¬¡ä»linuxä¸­ä¸ä¼ æä»¶å°GitHub](http://s8.sinaimg.cn/mw690/0060N6Yozy7greAPlzh87&690)

  逐行分析：

[![教程：第一次从linux中上传文件到GitHub](http://s4.sinaimg.cn/mw690/0060N6Yozy7greEfyxBb3&690) ](http://album.sina.com.cn/pic/0060N6Yozy7greEfyxBb3)

​		第3行：询问用户，创建的SSH-Key要保存在哪一个文件（默认/root/.ssh/id_rsa），按Enter确定，

[![教程：第一次从linux中上传文件到GitHub](http://s3.sinaimg.cn/mw690/0060N6Yozy7greG2SUq12&690)](http://album.sina.com.cn/pic/0060N6Yozy7greG2SUq12)

​		第4行：因为我实在根用户下操作，根用户的家目录没有.ssh文件夹，所以创建一个.ssh/id_rsa 来保 存SSh-Key。

[![教程：第一次从linux中上传文件到GitHub](http://s16.sinaimg.cn/mw690/0060N6Yozy7greHTJT1ff&690)](http://album.sina.com.cn/pic/0060N6Yozy7greHTJT1ff)

​		第5、6行：提示你设置密码，密码长度要求不少于五位，否则密码设置失败。

[![教程：第一次从linux中上传文件到GitHub](http://s13.sinaimg.cn/mw690/0060N6Yozy7greKojRa4c&690)](http://album.sina.com.cn/pic/0060N6Yozy7greKojRa4c)

​		后面的只是生成的一些SSH-Key信息，不用理会。

添加SSH Key到GitHub

在网页上打开你的GitHub：

[![教程：第一次从linux中上传文件到GitHub](http://s8.sinaimg.cn/mw690/0060N6Yozy7greRphyf17&690)](http://album.sina.com.cn/pic/0060N6Yozy7greRphyf17)
![教程：第一次从linux中上传文件到GitHub](http://s16.sinaimg.cn/mw690/0060N6Yozy7greMvPXh2f&690)

​         点击“SSH and GPG keys”，会出现（因为我的已经有一个了，所以在这里有显示；但是如果之前没有设置 的话，会提示“There are no SSH keys associated with your account.”）：

[![教程：第一次从linux中上传文件到GitHub](http://s2.sinaimg.cn/mw690/0060N6Yozy7greUfMGZ31&690)](http://album.sina.com.cn/pic/0060N6Yozy7greUfMGZ31)
[![教程：第一次从linux中上传文件到GitHub](http://s7.sinaimg.cn/bmiddle/0060N6Yozy7greW4q5826&690)](http://album.sina.com.cn/pic/0060N6Yozy7greW4q5826)

​         其中：“Title”块自己随便填，如果你有多台Linux，建议Title能体现你的SSH-key指的是哪一台Linux。

“Key”里面填的就是SSH-Key：在“一、  Linux上操作，生成SSH秘钥”中，我把SSH-key保存在Linux的 路径为/root/.ssh/id_rsa.pub 的文件里。所以，输入命令：vi /root/.ssh/id_rsa.pub。

[![教程：第一次从linux中上传文件到GitHub](http://s6.sinaimg.cn/mw690/0060N6Yozy7greZFzcp15&690)](http://album.sina.com.cn/pic/0060N6Yozy7greZFzcp15)

​        就可以看到SSH-key，它是以“ssh-rsa”开头  以邮箱结尾，把“ssh-rsa ……LI7x”即邮箱前的全部字符复制。将它们复制到GitHub上的“Key”栏中：

![教程：第一次从linux中上传文件到GitHub](http://s7.sinaimg.cn/mw690/0060N6Yozy7greZJaxEb6&690)

​		点击“Add SSH key”，会提示输入GitHub用户密码，输入后，即可完成。

- 测试密钥是否可行

  ​	逐行解释：

![教程：第一次从linux中上传文件到GitHub](http://s7.sinaimg.cn/mw690/0060N6Yozy7greZOtOCf6&690)

​			在linux终端输入ssh -T [git@github.com](mailto:git@github.com)     出现警告：

![教程：第一次从linux中上传文件到GitHub](http://s2.sinaimg.cn/mw690/0060N6Yozy7greZRSw1c1&690)

​			直接输入  yes    出现提示：

![教程：第一次从linux中上传文件到GitHub](http://s1.sinaimg.cn/mw690/0060N6Yozy7greZWy3u60&690)

​			输入设置SSH-key时的密码（步骤一的step2设置的密码），出现：

![教程：第一次从linux中上传文件到GitHub](http://s12.sinaimg.cn/bmiddle/0060N6Yozy7greZZgPFab&690)

​			算是成功了。

- 创建本地库

  ​	第一种方法:在本地某个文件中增加后缀为.github.io的文件夹 然后在此文件中执行git init。

  ​	第二种方法：克隆github的空白库到指定的位置。

  ```
  git clone https://github.com/ *用户名* / *用户名* .github.io	
  ```

- 下载jekyll主题

  ​	去官网http://jekyllthemes.org/ ，下载压缩包。

- 拷贝主题文件到克隆的库文件

  ​	把压缩包进行解压，然后把解压下的文件拷贝到本地库中，然后通过git指令把本地库的文件上传到GitHub的相应后缀为账户名.github.io的公共库中。

- 连接GitHub

![教程：第一次从linux中上传文件到GitHub](http://s14.sinaimg.cn/bmiddle/0060N6Yozy7grf057EFad&690)

​			git config --global user.name "raoweijiapng"

​			git config --global user.email raoweijia@outlook.com

![教程：第一次从linux中上传文件到GitHub](http://s9.sinaimg.cn/mw690/0060N6Yozy7grf0cjNeb8&690)

​			git init

​			git add . （注意：git add 后面有一个小数点）

​			git status  （查看状态，可选）

![教程：第一次从linux中上传文件到GitHub](http://s15.sinaimg.cn/bmiddle/0060N6Yozy7grf0pOlU2e&690)

​			git commit -m "It's my first time to send documents to GitHub in Linux."  （双引号里内容必须要 写，但内容不限）。

![教程：第一次从linux中上传文件到GitHub](http://s10.sinaimg.cn/mw690/0060N6Yozy7grf0xAN349&690)

​			git remote add origin https://github.com/Chenzhiyong47/pyflask.git

​			git push origin master   （如果第一次上传失败，在末尾的加  -f   参数是强制性上传，若GitHub 上面没有文	件与Linux上冲突，可不要这个参数；若GitHub上面有文件与这个冲突，则加上这 个参数。加上这个参数的作 	用：删掉GitHub上原有的文件，再上传Linux上的文件）。

​			按提示输入GitHub的账号和密码。

​			那么现在就可以上GitHub查看自己刚上传的文件了。

​      	最后启动浏览器并转到https：// 账户名.github.io，就进入到自己的blog。