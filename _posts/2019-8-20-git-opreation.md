---
title: git基础命令分享
tags: git
categories: git
---

* TOC
{:toc}

# git clone
    1. 在当前目录下创建一个名libgit2的目录,并在这个目录下初始化一个.git文件夹,从远程仓库拉取下所有数据放入.git文件夹,然后从中读取最新版本的文件的拷贝。 
    ```
    $ git clone https://github.com/libgit2/libgit2
    ```
    2. 与上一个命令相同的操作,不过在本地创建的仓库名字变为mylibgit。
    ```
    $ git clone https://github.com/libgit2/libgit2 mylibgit
    ```
    3. 获取指定分支的代码。
    ```
    $ git clone -b 分支名称 --single-branch git仓库的地址
    ```
# git init
    1. 该命令创建一个空的Git仓库，基本上是创建一个具有objects，refs/head，refs/tags和模板文件的.git目录。
    2. 现有存储库中运行git init命令是安全的，它不会覆盖已经存在的东西。
# git add
    1. git add (file)
    通常是通过git add 把某个file添加到索引库中，可以是文件也可以是目录。
    2. git add .
    把所有文件和目录添加到索引库中。
# git mv
    1. git mv rename rename.md
    把文件rename重命名为rename.md。
    2. git mv text.txt mydir
    把文件text.txt转移道mydir文件夹。
# git reset
    1. git reset HEAD <file>
    如果我们暂存的文件不想提交，可以用这条命令让暂存区的目录树会被重写，被master分支指向的目录树所替换，但是工作区不受影响。
    2. git reset --hard
    重设（重置）索引和工作目录，自从<commit>以来在工作目录中的任何改变都被丢弃，并把HEAD指向<commit>,回退版本。
# git clean
    1. git clean -n
    是一次clean的演习，告诉你哪些文件会被删除。记住他不会真正的删除文件,只是一个提醒。
    2. git clean -f
    删除当前目录下所有没有track过的文件。他不会删除.gitignore文件里面指定的文件夹和文件,不管这些文件有没有被track过。
    3. git clean -df
    删除当前目录下没有被track过的文件和文件夹。
    4. git clean - xf
    删除当前目录下所有没有track过的文件。不管他是否是.gitignore文件里面指定的文件夹和文件。
# git rm
    1. git rm
    删除未缓存的文件，与rm是不一样的，git rm要提交删除记录给远程库，让它也删除。
    2. git rm -r 
    删除目录，并从git的仓库管理系统中移除。
    3. git rm -f
    强制删除文件。
    4. git rm -cached
    从索引中删除文件，但是本地文件还存在。
# git log   回顾提交历史
    1. git log --oneline  
    查看历史记录的简洁的版本。 
    2. git log --graph
    查看历史中什么时候出现了分支，合并。以下为相同的命令，开启了拓扑图选项。
    3. git log --author = Linus --oneline -5
    查看五条Git源码中Linus提交的历史。
    4. git log --before = {3.weeks.ago} --after = {2010-04-18}
                                        --until
    看Git项目中三周前且在四月十八日之后的所有提交。
    5. git log --no-merges
    查看历史，隐藏合并提交。
# git show
    1. git show v1.0.0
    显示标签v1.0.0的消息，以及标签指向的对象
    2. git show v1.0.0^{tree}
    显示标签v1.0.0指向的树，即commit是的文件及文件夹。
    3.  git show next~10:/README
    显示分支next第十次提交中的RENAME.md的内容，RENAME必须存在分支中。
# git status
    用于显示工作目录和暂存区的状态。使用此命令能看到那些修改被暂存到了, 哪些没有,哪些文件没有被Git tracked到。git status不显示已经commit到项目历史中去的信息。
    git status -s   显示简洁的状态。
    ```
    On branch master
    Changes to be committed:  (已经在stage区, 等待添加到HEAD中的文件)
    (use "git reset HEAD <file>..." to unstage)
    modified: hello.py

    Changes not staged for commit: (有修改, 但是没有被添加到stage区的文件)
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)
    modified: main.py

    Untracked files:(没有tracked过的文件, 即从没有add过的文件)
    (use "git add <file>..." to include in what will be committed)
    ```
# git branch
    ```
    查看当前有哪些分支:
    $ git branch
    master
    * wchar_support
    上面显示结果中，当前有两个分支：master和wchar_support，当前在wchar_support分支上，它前面有个星号（*）
    ```
    1. git branch dev2
    下面命令将创建一个分支：dev2。
    2. git checkout dev2
    切换到指定分支：dev2。
    3.  git branch -a   git branch -r  查看远程分支
    查看本地和远程分支。
    4. git branch -m dev2 dev3
    修改分支dev2的名字为dev3。
    5. git push origin --delete dev2
    删除远程分支dev2。
    6. git merge dev2
    合并分支：dev2到当前分支（master）。
    7. git branch -d abc
    删除本地分支abc。
# git checkout
    1. git checkout dev2
    切换到分支dev2 或从暂存区把dev2文件替换工作区内的dev2（如果工作区中没有相应的文件，就复制到工作区中）。
    2. git checkout -b abc
    创建分支abc 并切换到abc中。
    3. git checkout HEAD
    会用HEAD指向的master分支中的全部或者部分文件替换暂存区和以及工作区中的文件。
    4. git checkout -b mywork origin/master
    基于远程库分支”origin/master“，创建一个叫”mywork“的分支，并进入mywork分支。
# git commit
    将索引的当前内容与描述更改的用户和日志消息一起存储在新的提交中。
    1. git commit （-a -m）或 （-am）
    跳过提交缓存的流程，直接进行提交。
    2. git commit --amend
    如果commit注释写错了，可以用此命令改一下注释。
# git diff
    比较的是工作目录(Working tree)和暂存区域快照(index)之间的差异，即修改之后还没有暂存起来的变化内容。
    1. git diff --stat
    查看简单的diff结果。
    2. git diff --cached
    查看已经暂存起来的文件(staged)和上次提交时的差异，即暂存区与最新提交的改动。
    3. git diff HEAD
    查看已缓存的与未缓存的所有改动。
    4. git diff topic..master
    输出自topic和master分别开发以来，master分支上的变更。
    5. git diff test
    查看当前目录和另外一个分支(test)的差别。
    6. git diff SHA1 SHA2
    比较两个历史版本之间的差异，SHA1，SHA2是类似 COMMIT ID 的32位长度的值。
# git merge
    将分支dev合并到当前分支中，自动进行新的提交。
    1. git merge --no-commit dev
    将分支dev合并到当前分支中，自动进行新的提交。
# git rebase
    ```
    $ git checkout mywork
    $ git rebase origin
    这些命令会把你的”mywork“分支里的每个提交(commit)取消掉，并且把它们临时保存为补丁(patch)(这些补丁放到”.git/rebase“目录中),然后把”mywork“分支更新到最新的”origin“分支，最后把保存的这些补丁应用到”mywork“分支上。
    ```
    ```
    在另一个分支基础之上重新应用，用于把一个分支的修改合并到当前分支。
    之后用”git add“命令去更新这些内容的索引(index), 然后不需要执行git commit,只要执行：
    $ git rebase --continue
    在任何时候，可以用--abort参数来终止rebase的操作，并且”mywork“ 分支会回到rebase开始前的状态。
    $ git rebase --abort
    ```
# git tag
    列出现有的所有标签。
    1. git tag -a v1.4 -m'my version 1.4'
    创建一个含附注类型的标签。用-a指定标签名字，而-m选项则指定了对应的标签说明。
    2. git tag -s v1.5 -m 'my signed 1.5 tag'
    如果有自己的私钥，还可以用GPG来签署标签，再运行git show会看到对应的GPG签名也附在其内。
    3. git tag -d v1.0
    删除名称为：v1.0的标签。
    4. git tag v1.4-lw
    创建轻量级标签，这种标签实际上就是一个保存着对应提交对象的校验和信息的文件。
    5. git tag -a v1.2 9fceb02
    在提交之前忘记设标签，就可以使用此命令，后面的9fceb02 是版本的哈希值。
    6. git push origin v1.5
    git push origin --tags （分享所有标签）
    分享标签，发送到远程库中，其他人在使用这个远程库就可以看到标签。
# git fetch
    1. git fetch （主机名 如origin）
    将某个远程主机的更新，全部取回本地。默认情况下，git fetch取回所有分支的更新。
    2. git fetch origin master
    取回origin主机下的master分支的更新。所取回的更新，在本地主机上要用”远程主机名/分支名”的形式读取。以上就用origin/master读取。
    2. git fetch origin +pu:pu maint:tmp
    此更新(或根据需要创建)通过从远程存储库的分支(分别)pu和maint提取来分支本地存储库中的pu和tmp。
    即使没有快进，pu分支将被更新，因为它的前缀是加号; tmp不会。
# git pull
    1. git pull origin next
    取回origin/next分支，再与当前分支合并。
    ```
    等同于
    $ git fetch origin
    $ git merge origin/next
    ```
    2. git pull origin next:master
    要取回origin主机的next分支，与本地的master分支合并。

    在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系(tracking)。比如，在git clone的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动”追踪”origin/master分支。

    3. git branch --set-upstream master origin/next
    上面命令指定master分支追踪origin/next分支。
    4. git pull origin
    上面命令表示，本地的当前分支自动与对应的origin主机”追踪分支”(remote-tracking branch)进行合并。如果当前分支只有一个追踪分支，连远程主机名都可以省略。
# git push
    用于将本地分支的更新，推送到远程主机。
    1. git push origin master
    上面命令表示，将本地的master分支推送到origin主机的master分支。如果master不存在，则会被新建。
    2. git push origin --delete master
    上面命令表示删除origin主机的master分支。
    3. git push --all origin
    上面命令表示，将所有本地分支都推送到origin主机。
    4. git push origin --tags
    向远程库推送标签(tag)。
    ```
    推送tag
    $ git push origin tag_name

    删除远程标签
    $ git push origin:tag_name
    ```