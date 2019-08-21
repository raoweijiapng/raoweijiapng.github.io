
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
​         git add和git commit 一下
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
  错误
```
  git revert （哈希值）   撤消指定的提交。
  git reset --hard （哈希值或标签名）  -回退版本  要进行强行提交，使用git push -f -u (主机名) (分支名)
  git push （主机名）（标签名）  提交标签到远程库
  git remote rm （主机名）  删除远程库地址
  git push （主机名）（本地分支）：（远程分支） 把某个本地分支提交到远程分支。
```
![git命令速查表](https://raoweijiapng.github.io/static/img/git.png)