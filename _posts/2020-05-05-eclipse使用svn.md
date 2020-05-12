---
title: svn在eclipse中的安装和使用
tags: svn eclipse
categories: svn eclipse
---

* TOC
{:toc}

## eclipse中svn插件的安装

- 打开Eclipse,进入Help- Eclipse Marketplace搜索Subversive，然后安装。

##	创建项目并提交到SVN服务器 

- 右键项目,选择Team→Share Project,选择分享在SVN

![svn的提交1](https://raoweijiapng.github.io/static/img/eclipse/svn的提交1.jpg)
			
![svn的提交2](https://raoweijiapng.github.io/static/img/eclipse/svn的提交2.jpg)	
				
- 可以选择创建资源库也可以选择使用已有的,我们现在选择创建新的资源库

![svn的提交3](https://raoweijiapng.github.io/static/img/eclipse/svn的提交3.jpg)

- 将我们的资源库路径粘贴进来,点击Next

![svn的提交4](https://raoweijiapng.github.io/static/img/eclipse/svn的提交4.jpg)
		
- 可以选择将项目名当成资源库中的文件夹名,点Finish结束

![svn的提交5](https://raoweijiapng.github.io/static/img/eclipse/svn的提交5.jpg)		

- 进行忽略文件的选取,右键单击我们刚刚分享到服务器的项目,选中Team→设置属性

![svn的提交6](https://raoweijiapng.github.io/static/img/eclipse/svn的提交6.jpg)

- 现在属性名的下拉选择框中选中”svn:ignore”,然后在”属性内容”的大框框中输入以下文本

![svn的提交7](https://raoweijiapng.github.io/static/img/eclipse/svn的提交7.jpg)

- 右键位于SVN管理的项目,选择Team→提交

![svn的提交8](https://raoweijiapng.github.io/static/img/eclipse/svn的提交8.jpg)

## 将项目从SVN服务器拉取到Eclipse

- 将SVN资源库选项卡展示出来,打开菜单栏的Window→Show View→Other

![svn的提交9](https://raoweijiapng.github.io/static/img/eclipse/svn的提交9.jpg)

- 在弹出的窗口文本输入框输入svn,选中SVN目录下的SVN资源库,点击OK

![svn的提交10](https://raoweijiapng.github.io/static/img/eclipse/svn的提交10.jpg)

- 可以看到在底部视窗增加了SVN资源库分支窗口,在空白位置鼠标右键单击,选择新建→资源库位置

![svn的提交11](https://raoweijiapng.github.io/static/img/eclipse/svn的提交11.jpg)

- 在弹出窗口中输入我们的资源库位置,然后点击Finish,然后输入账户密码

- 这样就看到了我们的资源库位置添加到了Eclipse的SVN资源库选项卡之内

![svn的提交12](https://raoweijiapng.github.io/static/img/eclipse/svn的提交12.jpg)

- 右键单击资源库中的项目,点击”检出为”

![svn的提交13](https://raoweijiapng.github.io/static/img/eclipse/svn的提交13.jpg)	

![svn的提交14](https://raoweijiapng.github.io/static/img/eclipse/svn的提交14.jpg)	

- 在弹出的窗口选择我们项目的类型,接着配置该项目的属性

## 多次提交项目

- 点击整个项目，右键—team—与资源库同步

![svn的提交15](https://raoweijiapng.github.io/static/img/eclipse/svn的提交15.jpg)	

- 跳转到Team Synchronizing界面，这个时候eclipse会自动检测本地的代码和服务器上的代码有没有冲突，没有如下图,然后点击下图中更新代码的按钮将服务器代码更新下来,点击下图中上传代码的按钮将本地代码提交上去

![svn的提交17](https://raoweijiapng.github.io/static/img/eclipse/svn的提交17.jpg)   

- 有的话会在eclipse的左侧显示出来

![svn的提交16](https://raoweijiapng.github.io/static/img/eclipse/svn的提交16.png)

- 双击击用冲突的文件,然后把下图左侧库中的别人修改的代码复制到右侧,然后更新

![svn的提交18](https://raoweijiapng.github.io/static/img/eclipse/svn的提交18.png)	

- 返回编程界面找到冲突文件,右键选择标记解决

![svn的提交19](https://raoweijiapng.github.io/static/img/eclipse/svn的提交19.jpg)

- 弹窗中选择倒数第二个,如果不确定,可以选择倒数第三个,但要把自己编写的代码提前备份