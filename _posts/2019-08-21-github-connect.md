---
title: git测试与协议以及今日分享
tags: git python
categories: git
---

* TOC
{:toc}

# 和别人协同合作项目
  1. 首先fork你要的项目
    点击fork时，就会跳到自己的账号里，然后就把别人的一个库复制到成了自己的库。
    
  2. git clone （git@github.com:raoweijiapng/hellocountry.git）使用ssh类型的地址。

  3. 将别人的项目地址添加为远程仓库
      进入刚刚克隆的目录中：
  git remote add upstream git@github.com:17824909342/hellocountry.git（别人的项目地址）
  使用git remote -v
  可以看到我们有两个仓库一个origin，咱们自己的github仓库；一个upstream，原作者的远程仓库。当然也可以不用upstream这个名字。
  
  4. 新建分支，在分支上修改 
    git checkout -b test  
   然后就可以添加我们的修改了。
  
  5. 同步别人的项目仓库
```
  git fetch orgin master:tmp //将远程仓库master分支获取最新，在本地建立tmp分支
  git diff tmp //将当前分支和tmp进行对比
  git merge origin/master //合并tmp分支到当前分支
```
​     git add和git commit 一下
  6. 将修改push到我们的github上
    git push origin test:test
  7. pull request
    修改已经push到了github下了，但是要向原作者请求合并到原项目中，如果原作者合并了，也就意味着你是此项目的贡献者了。
  到你的github上点击如下按钮 pull request
  当然你也可以选择test分支，，然后点击项目右边的创建一个pull request。提交完pull request，原作者就会看到你的合并请求，采不采纳就是他的事了。

# git的四种协议
  - 本地协议
  最基本的协议，其远程仓库其实就是硬盘内部的一个目录（例如D:\\project）。常见于团队内的人对一个共享的文件系统（例如NFS）具有访问权限，或者多人共用一台电脑的情况。
  ```
  可以使用git clone /d/project来克隆本地的远程仓库。还可以使用带file协议的路径：git clone file:///d/project克隆本地远程仓库。
  ```
  本地协议的优点：搭建简单。直接使用了现有的文件权限和网络访问权限，如果已经有了共享文件系统，建立版本库会十分容易，只需要像设置其他共享目录一样，将一个Git仓库放在大家都能够访问到的路径并设置好读写权限就可以了。
  缺点：不方便从多个位置访问，例如你在公司的电脑上搭建了本地仓库，你想在家里访问就有点困难了。不能保证Git仓库的安全，由于每一个人都具有仓库目录完整的shell权限，没有方法可以阻止他们删除或者破坏仓库。

  - HTTP协议
 （DUMP）HTTP协议（旧，很少使用了）：只支持只读模式。
  智能（smart）HTTP协议（新，GitHub目前就使用了这种协议）：支持读写模式。既可以像git://协议一样设置匿名服务，又可以像SSH一样提供传输时的授权和加密，而且只用一个URL就可以。
  得到git://和SSH的功能，省去了为不同的需求设置不同的URL。 HTTP协议推送(push)以及拉取（pull），服务器都会询问你的用户名和密码。
  HTTP协议的优点：不同的访问方式只需要一个URL，服务器只在需要授权时提示输入授权信息。
  相对于SSH而言，不需要生成SSH秘钥对再把秘钥上传到服务器上。
  HTTP/S被广泛采用，一般的企业防火墙都会允许这些端口的数据通过。
  缺点：在一些服务器上，架设HTTP/S协议会比SSH协议棘手一些；每次需要输入用户名和密码，管理这些凭证会比较麻烦一些（当然你可以使用凭证存储工具，keychain（OSX））。

  - SSH协议
　　架设Git服务器时常用SSH协议作为传输协议，因为大多数环境都支持SSH访问，即使不支持也比较容易搭建。
   $ git clone ssh://user@server/project.git //使用ssh协议clone一个仓库
   优点：1.架设简单；2.数据传输时是安全的（所有数据传输时都经过授权和加密）；3.高效，和其他3种协议一样，在传输数据时也会尽量压缩数据。
   缺点：不能通过它实现匿名访问。即使使用者只需要读取数据，使用者也要有通过SSH访问你主机的权限，不利于开源项目。

  - Git协议
  包含在Git里的一个特殊的守护进程中，监听一个特定的端口9418（类似于SSH服务，但是无需任何访问授权）。
  优点：目前是Git所使用的协议里面最快的。如果你的项目不需要为写进行用户授权，可以使用Git协议。
  缺点：1.缺乏授权机制，一旦放开推送操作，意味着网络上的任何人都能向你的项目推送代码；2.架设难。

# git测试
  考试内容：
```
raoweijia@raoweijia-ThinkPad-E580:~$ mkdir xiongdihui
raoweijia@raoweijia-ThinkPad-E580:~$ cd xiongdihui/
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git init
已初始化空的 Git 仓库于 /home/raoweijia/xiongdihui/.git/
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ touch 2019-08-21-git.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ vim 2019-08-21-git.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git add 2019-08-21-git.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git commit -m"diyicitijiao"
[master（根提交） 8622081] diyicitijiao
 1 file changed, 1 insertion(+)
 create mode 100644 2019-08-21-git.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git remote add xiongdihui git@github.com:raoweijiapng/github.git
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git branch test
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout test
切换到分支 'test'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git mv 2019-08-21-git.md 2019-08-21-git-test.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push -u xiongdihui test
枚举对象: 3, 完成.
对象计数中: 100% (3/3), 完成.
写入对象中: 100% (3/3), 230 bytes | 230.00 KiB/s, 完成.
总共 3 （差异 0），复用 0 （差异 0）
To github.com:raoweijiapng/github.git
 * [new branch]      test -> test
分支 'test' 设置为跟踪来自 'xiongdihui' 的远程分支 'test'。
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout master
A 2019-08-21-git-test.md
D 2019-08-21-git.md
切换到分支 'master'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git fetch xiongdihui test
来自 github.com:raoweijiapng/github
 * branch            test       -> FETCH_HEAD
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git diff xiongdiui/test
fatal: 有歧义的参数 'xiongdiui/test'：未知的版本或路径不存在于工作区中。
使用 '--' 来分隔版本和路径，例如：
'git <命令> [<版本>...] -- [<文件>...]'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git diff xiongdihui/test
diff --git a/2019-08-21-git.md b/2019-08-21-git-test.md
similarity index 100%
rename from 2019-08-21-git.md
rename to 2019-08-21-git-test.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git merge xiongdihui/test
已经是最新的。
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui
fatal: 当前分支 master 没有对应的上游分支。
为推送当前分支并建立与远程上游的跟踪，使用

    git push --set-upstream xiongdihui master

raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui master
总共 0 （差异 0），复用 0 （差异 0）
remote: 
remote: Create a pull request for 'master' on GitHub by visiting:
remote:      https://github.com/raoweijiapng/github/pull/new/master
remote: 
To github.com:raoweijiapng/github.git
 * [new branch]      master -> master
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git tag -a v1.0
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui v1.0
枚举对象: 1, 完成.
对象计数中: 100% (1/1), 完成.
写入对象中: 100% (1/1), 157 bytes | 157.00 KiB/s, 完成.
总共 1 （差异 0），复用 0 （差异 0）
To github.com:raoweijiapng/github.git
 * [new tag]         v1.0 -> v1.0
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git remote -v
xiongdihui  git@github.com:raoweijiapng/github.git (fetch)
xiongdihui  git@github.com:raoweijiapng/github.git (push)
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout -b test2
A 2019-08-21-git-test.md
D 2019-08-21-git.md
切换到一个新分支 'test2'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ vim 2019-08-21-git-test.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git tag -a v2.0 -m"dewd"
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui master
Everything up-to-date
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui v2.0
枚举对象: 1, 完成.
对象计数中: 100% (1/1), 完成.
写入对象中: 100% (1/1), 157 bytes | 157.00 KiB/s, 完成.
总共 1 （差异 0），复用 0 （差异 0）
To github.com:raoweijiapng/github.git
 * [new tag]         v2.0 -> v2.0
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout master
A 2019-08-21-git-test.md
D 2019-08-21-git.md
切换到分支 'master'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ vim 2019-08-21-git-test.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui test3
error: 源引用表达式 test3 没有匹配
error: 推送一些引用到 'git@github.com:raoweijiapng/github.git' 失败
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push -f -u xiongdihui test3
error: 源引用表达式 test3 没有匹配
error: 推送一些引用到 'git@github.com:raoweijiapng/github.git' 失败
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui master:test3
总共 0 （差异 0），复用 0 （差异 0）
remote: 
remote: Create a pull request for 'test3' on GitHub by visiting:
remote:      https://github.com/raoweijiapng/github/pull/new/test3
remote: 
To github.com:raoweijiapng/github.git
 * [new branch]      master -> test3
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout test3
A 2019-08-21-git-test.md
D 2019-08-21-git.md
分支 'test3' 设置为跟踪来自 'xiongdihui' 的远程分支 'test3'。
切换到一个新分支 'test3'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ vim 2019-08-21-git-test.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git push xiongdihui test3
Everything up-to-date
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git log
commit 86220815cf1000aafaa913e2542dbb3d0332a228 (HEAD -> test3, tag: v2.0, tag: v1.0, xiongdihui/test3, xiongdihui/test, xiongdihui/master, test2, test, master)
Author: raoweijia-png <raoweijia@outlook.com>
Date:   Wed Aug 21 19:35:48 2019 +0800

    diyicitijiao
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git reset --hard e580356   版本回退
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git checkout test2
A 2019-08-21-git-test.md
D 2019-08-21-git.md
切换到分支 'test2'
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ touch test2.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ vim test2.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git add test2.md 
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git commit -m"abc"
[test2 142c544] abc
 2 files changed, 1 insertion(+)
 rename 2019-08-21-git.md => 2019-08-21-git-test.md (100%)
 create mode 100644 test2.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git commit -amend
error: 你的意思是 `--amend`（有两个短线？）
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git commit --amend
[test2 e580356] abcdd
 Date: Wed Aug 21 20:00:56 2019 +0800
 2 files changed, 1 insertion(+)
 rename 2019-08-21-git.md => 2019-08-21-git-test.md (100%)
 create mode 100644 test2.md
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git revert （e580356）   撤消指定的提交
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git remote rm xiongdihui
raoweijia@raoweijia-ThinkPad-E580:~/xiongdihui$ git log

```
  错误
```
  git revert （哈希值）   撤消指定的提交。
  git reset --hard （哈希值或标签名）  -回退版本  要进行强行提交，使用git push -f -u (主机名) (分支名)
  git push （主机名）（标签名）  提交标签到远程库
  git remote rm （主机名）  删除远程库地址
  git push （主机名）（本地分支）：（远程分支） 把某个本地分支提交到远程分支。
  git reset HEAD (file)  不想提交的缓存文件，用上一次提交的树替换缓存区。
  git checkout --（file）
  git checkout HEAD（file）用master的树替换工作区和缓存区。
```
[相关的命令博客](https://blog.csdn.net/shimazhuge/article/details/52759429)

![git命令速查表](https://raoweijiapng.github.io/static/img/git.png)

# Python与人工智能
  人工智能包含常用机器学习和深度学习两个很重要的模块，而python拥有matplotlib、Numpy、sklearn、keras等大量的库，像pandas、sklearn、matplotlib这些库都是做数据处理、数据分析、数据建模和绘图的库，基本上机器学习中对数据的爬取（scrapy）、对数据的处理和分析（pandas）、对数据的绘图（matplotlib）和对数据的建模（sklearn）在python中全都能找到对应的库来进行处理。