---
title: RTL8821CE无线网卡驱动以及linux命令
tags: RTL8821CE linux
categories: gatework  linux
---

* TOC
{:toc}

​		安装Ubuntu系统后发现wifi的适配器不能发现，所以要下载相应的驱动并安装。
- 查找相应的网卡的信息

~~~
lspci      -查看系统中所有PCI总线以及连接到该总线上的设备的工具
电脑lspci显示结果为：
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v6/7th Gen Core Processor Host Bridge/DRAM Registers (rev 08) //主桥
00:02.0 VGA compatible controller: Intel Corporation UHD Graphics 620 (rev 07)            //显卡兼容控制器
00:08.0 System peripheral: Intel Corporation Xeon E3-1200 v5/v6 / E3-1500 v5 / 6th/7th Gen Core Processor Gaussian Mixture Model
00:14.0 USB controller: Intel Corporation Sunrise Point-LP USB 3.0 xHCI Controller (rev 21) 		      //USB控制器
00:14.2 Signal processing controller: Intel Corporation Sunrise Point-LP Thermal subsystem (rev 21)
00:16.0 Communication controller: Intel Corporation Sunrise Point-LP CSME HECI #1 (rev 21)         //通信控制器
00:17.0 SATA controller: Intel Corporation Sunrise Point-LP SATA Controller [AHCI mode] (rev 21) //硬盘模式
00:1c.0 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #1 (rev f1)
00:1c.4 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #5 (rev f1)                        //PCI桥
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #9 (rev f1)
00:1d.2 PCI bridge: Intel Corporation Device 9d1a (rev f1)
00:1d.3 PCI bridge: Intel Corporation Device 9d1b (rev f1)
00:1f.0 ISA bridge: Intel Corporation Intel(R) 100 Series Chipset Family LPC Controller/eSPI Controller - 9D4E (rev 21)    //数据局部总线
00:1f.2 Memory controller: Intel Corporation Sunrise Point-LP PMC (rev 21)
00:1f.3 Audio device: Intel Corporation Sunrise Point-LP HD Audio (rev 21)                    //声卡
00:1f.4 SMBus: Intel Corporation Sunrise Point-LP SMBus (rev 21）								  //系统管理总线
02:00.0 Display controller: Advanced Micro Devices, Inc. [AMD/ATI] Lexa PRO [Radeon RX 550/550X] (rev c0 ）                //显示控制器
03:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 10)
04:00.0 Non-Volatile memory controller: Toshiba America Info Systems Device 0113 (rev 01)
05:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8821CE 802.11ac PCIe Wireless Network        //网卡
06:00.0 SD Host controller: O2 Micro, Inc. SD/MMC Card Reader Controller (rev 01)
~~~

# 安装8821CE无线网卡驱动

- 升级ubuntu内核到4.14

  ```
  $ uname -sr
  ```
```
 		*若内核不是*4.14,则进行以下两个操作，否则跳过。
		- 安装Kernel4.14
	   对于64位操作系统,依次安装三个文件：
	  ​		$ cd /tmp/
      ​		$ wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.14/linux-headers-4.14.0-041400_4.14.0-041400.201711122031_all.deb
      ​		$ wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.14/linux-headers-4.14.0-041400-generic_4.14.0-041400.201711122031_amd64.deb
      ​		$ wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.14/linux-image-4.14.0-041400-generic_4.14.0-041400.201711122031_amd64.deb
      ​		$ sudo dpkg -i *.deb
      ​ 	$ sudo reboot
      ​		安装这些.debs后，重新启动即可。
        -  	   卸载之前版本的内核
       * 	  使用如下命令可以自动移除*Ubuntu 16.04 *系统不再需要的旧版内核和软件包：*
```
​         $ sudo apt autoremove --purge
  ```
	  - 	   安装gcc，后续make需要
  	  $		  sudo apt-get  install  gcc                --安装完了可以执行
      $		  gcc--version                                        --查看版本
	   - 	   下载RTL8821CE驱动
	  ​      打开https://github.com/hlcool/rtl8821ce，选择克隆和下载，然后解压。
	    -       关闭Secure Boot
  				关机重启时，连按F12 进入 bios，Tab 切换到管理界面，找到 Secure Boot 选项并关闭。
		-      安装RTL8821CE驱动
  				更改Makefile*文件中*"exportTopDIR ?= ..." *为*"TopDIR ?= PATH TO EXTRACTED DIRECTORY"(填写当前路径地址)。cd 到驱动的文件地址，在当前目录中执行命令。
  ```
 	 	make
	  	sudo make install
 	 	sudo modprobe -a 8821ce

# Linux常见的命令

- ls   查看指定目录的目录与文件

  ```
  ls  -a         显示指定目录下所有目录与文件，包括隐藏文件。
        -l          以列表的方式显示文件的详细信息(如创建时间,大小)。
        -l  -h   以列表的方式的方式显示文件大小 。
  注意：隐藏文件和隐藏文件夹是用来配置应用的。
  ```

- 通配符的使用

  ```
  *		    -----代表任意个数个字符。
  ？		 -----代表任意一个字符，至少一个。
  []		   ----- 表示可以匹配字符族中的任意一个 。
  [abc]  -----匹配a、b、c中的任意一个。
  [a-f]    -----匹配从a到f范围内的任意一个字符。
  ```

- cd    其功能为更改当前的工作目录，也是用户最常用的命令之一

  ```
  cd        回到家目录（/home/用户目录）。
  cd~     回到家目录（/home/用户目录）。
  cd /     回到根目录（/即本地c盘）。
  cd ..    回到上级目录。
  cd -    显示当前目录的地址。
  ```

- touch （文件名）  创建文件或修改文件时间

  如果文件不存在，可以创建一个空白文件。

  如果已经存在，可以修改文件的末次修改日期。

- mkdir  （文件夹名） 新建目录、文件夹

  mkdir -p  （文件夹名1   文件夹名2  ... ）    递归创建目录 。

  注意：新建目录的名称不能与当前目录中已有的目录或文件同名。

- rm   （文件名或目录名） 删除文件或目录

  ```
  -f  强制删除，忽略不存在的文件，无需提示 。   
  -r  递归地删除目录下的内容，删除文件夹时必须加此参数。
  注意：使用rm命令要小心，因为文件删除后不能恢复。
  ```

- tree （目录名）以树状图列出文件目录结构

  tree-d    只显示目录。

- cp （要拷贝的文件     拷贝到的地址）默认只拷贝文件

  ```
  cp   -f    已经存在的文件直接覆盖。
          -i    覆盖前提示。
          -r   递归复制文件和文件夹（也可只复制文件或文件夹）。
  ```

- mv  用来为文件或目录改名、或将文件或目录移入其它位置。

  | mv 文件名 文件名 | 将源文件名改为目标文件名。                                   |
  | ---------------- | ------------------------------------------------------------ |
  | mv 文件名 目录名 | 将文件移动到目标目录。                                       |
  | mv 目录名 目录名 | 目标目录已存在，将源目录移动到目标目录；目标目录不存在则改名。 |

  ​		-i     若指定目录已有同名文件，则先询问是否覆盖旧文件。

  ​		-f    在mv操作要覆盖某已有的目标文件时不给任何指示。

-  cat   （文件名） 查看文件内容

  cat   file1   file2  > file      将几个文件合并为一个文件。

  cat  >  filename      只能创建新文件,不能编辑已有文件。

- echo    用于字符串的输出

  ```
  echo string     显示普通字符串。
  echo "It is a test" > myfile       显示结果定向到文件，会覆盖原有的内容。
      							   >>                    表示追加，会将内容追加到已有文件的末尾。
  ```

- more   分屏显示文件内容分页显示

  more 命令类似 cat ，不过会以一页一页的形式显示，更方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示，而且还有搜寻字串的功能（与 vi 相似），使用中的说明文件，请按 h 。

- ifconfig       查看或配置网卡信息  

- ping（url） 测试网络是否正常

- 关机

  shutdown -r now  立刻重启。
  shutdown now      立刻关机。
  shutdown 20:25    20：25关机。
  shutdown +10  系统再过十分钟后关机。

- sudo命令用来以其他身份来执行命令，预设的身份为root。

  ```
  su - 用户名   切换用户，并且切换目录，-可以切换到用户家目录，否则保持位置不变。
  su 不接用户名，可以切换到root。
  ```

- tar  -czvf test.tar.gz  a.c   //压缩 a.c文件为test.tar.gz

  tar  -czvf test.tar.gz  		 //解压

- 安装
  sudo apt install 软件包。

- 卸载

  sudo apt remove 软件名。

- 在终端修改图片

  下载imagemagick      --  sudo apt-get install imagemagick。

  转换图片的大小            -- convert example.png -resize 200×100 example.png

  转换图片的格式			--convert example.png  example.jpg

  旋转图片90度                --convert howtogeek.jpg -rotate 90 howtogeek-rotating.jpg

  然后加入head.html中。



