# Linux

​		什么是Linux？Linux是人们熟知的三大操作系统之一。三大操作系统分别是Windows、MacOS和Linux。特点是免费、开源(源代码开放)、多用户(同时允许多个用户操作同一个Linux系统)、多任务(同时允许多个任务执行)。

​		那为什么需要学习Linux操作系统呢？因为Linux操作系统提供了许多的发行版本，广泛应用于服务器等领域。程序员开发出来的项目，最终要上线运行，就需要将项目部署在Linux服务器中。

![image-20230621174411138](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230621174411138.png)

​		这里主要学习Linux系统的四个方面：

+ 环境搭建：在公司中，一般不会将Linux系统安装在本机电脑上，而是会装在一台专门的服务器上。而需要操作想要使用的服务器，就需要用到远程连接工具了。

+ 常用命令：在本机上连接上服务器后，通过各种命令操作Linux服务器，重点掌握以下操作：

A. 文件、目录的基本操作(查看,删除,创建等)

B. 文件及文件夹的拷贝、移动、重命名

C. 文件的打包、压缩、解压缩操作

D. 文本的编辑

+ 安装软件：在Linux服务器中，安装一些开发常用的软件并配置环境，例如：JDK、Tomcat、MySQL等等。
+ 项目部署：把基础的软件及环境安装配置好之后，我们就可以将我们开发的项目部署在服务器中进行运行并测试。

### 1.Linux简介

​		针对于操作系统，主要分为几种操作系统，分别是：桌面操作系统、服务器操作系统、移动操作系统、嵌入式操作系统。介绍几种代表性的操作系统。

+  桌面操作系统

| 操作系统 | 特点                                   |
| -------- | -------------------------------------- |
| Windows  | 用户数量最多                           |
| MacOS    | 操作体验好，办公人士首选               |
| Linux    | 用户数量少(桌面操作系统,Linux使用较少) |

+ 服务器操作系统

| 操作系统       | 特点                       |
| -------------- | -------------------------- |
| Unix           | 安全、稳定、付费           |
| Linux          | 安全、稳定、免费、占有率高 |
| Windows Server | 付费、占有率低             |

+ 移动设备操作系统

| 操作系统 | 特点                                                         |
| -------- | ------------------------------------------------------------ |
| Android  | 基于 Linux 、开源，主要用于智能手机、平板电脑和智能电视      |
| IOS      | 苹果公司开发、不开源，用于苹果公司的产品，例如：iPhone、 iPad |

+  嵌入式操作系统

| 操作系统 | 特点                   |
| -------- | ---------------------- |
| Linux    | 机顶盒、路由器、交换机 |

##### 1.1Linux版本

​		Linux系统的版本分为两种，分别是：内核版和发行版。

**内核版**

- 由Linus Torvalds及其团队开发、维护

- 免费、开源

- 负责控制硬件

**发行版**

- 基于Linux内核版进行扩展

- 由各个Linux厂商开发、维护

- 有收费版本和免费版本

### 2.Linux安装

​		Linux安装一般有两种方式：

| 方式       | 概述                             | 场景                                                         |
| ---------- | -------------------------------- | ------------------------------------------------------------ |
| 物理机安装 | 直接将操作系统安装到服务器硬件上 | 企业开发中，我们使用的服务器基本都是采用这种方式             |
| 虚拟机安装 | 通过虚拟机软件安装               | 在学习阶段，没有自己服务器，而我们又需要学Linux，就可以安装在虚拟机上 |

​		什么是虚拟机呢？**虚拟机**（Virtual Machine）指通过**软件**模拟的具有完整硬件系统功能、运行在完全隔离环境中的完整计算机系统。相当于在电脑中又虚拟了一台全新的电脑。常用虚拟机软件： 

**注意：**安装了VMware虚拟机软件后，就要在里面安装Linux系统了。有些地方需要留意。

**安装Linux流程：**在VMware首页点击新建虚拟机；选择”典型配置“；选择”稍后安装操作系统（s）“；选择Linux系统，版本选择CentOS 7  64 位；设置虚拟机名称以及虚拟机系统文件存放地址；设置磁盘容量；自定义硬件信息：

![image-20230621181736775](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230621181736775.png)

创建好新虚拟机后打开配置系统，**流程如下：**选择Install CentOS7；语言选择简体中文；选择自动配置分区；软件选择”最小安装“;设置root用户密码（**不能忘记**）。安装好后重启虚拟机并登录root用户。

##### 2.1设置网卡

​		重启虚拟机后可以通过 ip addr 命令来获取Linux系统的IP地址，如果没有的话，说明：**启动服务器时未加载网卡，导致IP地址初始化失败。那么就需要来修改网络初始化配置，设定网卡在系统启动时执行初始化。**

​		修改网卡的配置：

```java
cd /				进入根目录
cd etc				进入etc目录
cd sysconfig		进入sysconfig目录
cd network-scripts	进入network-scripts
vi ifcfg-ens33		编辑ifcfg-ens33文件


进入文件后执行如下操作: 
1. 按 i 键 		 进入编辑状态
2. 按↑↓键来移动光标, 删除no,输入yes 
3. 按 ESC 键
4. 输入 :wq
5. 按 ENTER	保存退出
```

##### 2.2 安装Moba

​		Moba是什么？是用来远程操控Linux系统的工具。是属于SSH工具中的一种。

##### 2.3Linux目录

​		Linux目录结构和Windows的目录存在较大差异。众所周知Windows种存在盘符的概念，文件都归于某个盘符（C盘、D盘等等）。而LInux的目录结构具有以下特点：

+  / 是所有目录的顶点
+ 目录结构像一颗倒挂的树



Windows目录结构和Linux目录结构对比：

![image-20230627175655431](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627175655431.png)

​		下面是Linux中常用目录的作用以及说明：

| 编号 | 目录  | 含义                                       |
| ---- | ----- | ------------------------------------------ |
| 1    | /bin  | 存放二进制可执行文件                       |
| 2    | /boot | 存放系统引导时使用的各种文件               |
| 3    | /dev  | 存放设备文件                               |
| 4    | /etc  | 存放系统配置文件                           |
| 5    | /home | 存放系统用户的文件                         |
| 6    | /lib  | 存放程序运行所需的共享库和内核模块         |
| 7    | /opt  | 额外安装的可选应用程序包所放置的位置       |
| 8    | /root | 超级用户目录                               |
| 9    | /sbin | 存放二进制可执行文件，只有root用户才能访问 |
| 10   | /tmp  | 存放临时文件                               |
| 11   | /usr  | 存放系统应用程序                           |
| 12   | /var  | 存放运行时需要改变数据的文件，例如日志文件 |

##### 2.4设置静态IP

​		在安装好Linux系统后，默认IP地址是动态获取的，后续在部署Linux服务器项目时，如果IP是动态获取的话，那么每次重启服务器后，访问Linux项目IP地址都可能会变动，十分繁琐，所以需要设置静态IP地址。

+ 设置静态IP地址需要修改 /etc/sysconfig/network-scripts/ifcfg-ens33 配置文件，内容如下： 

+ ```properties
  TYPE=Ethernet
  PROXY_METHOD=none
  BROWSER_ONLY=no
  BOOTPROTO=static
  IPADDR="192.168.192.141"        # 设置的静态IP地址
  NETMASK="255.255.255.0"         # 子网掩码
  GATEWAY="192.168.192.2"         # 网关地址
  DNS1="192.168.192.2"            # DNS服务器
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_FAILURE_FATAL=no
  IPV6_ADDR_GEN_MODE=stable-privacy
  NAME=ens33
  UUID=afd0baa3-8bf4-4e26-8d20-5bc426b75fd6
  DEVICE=ens33
  ONBOOT=yes
  ZONE=public
  ```

  **注意：设置的第三个网段为192，并非随意指定的，需要与虚拟机中的虚拟网络编辑器中的NAT模式配置的网关保持一致。**

  ![image-20230628154128362](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628154128362.png)

### 3.Linux命令

##### 3.1Linux简单命令

​		Linux常用的操作命令有哪些呢？常用命令需要熟练掌握，如下：

| 序号 | 命令           | 对应英文             | 作用                    |
| ---- | -------------- | -------------------- | ----------------------- |
| 1    | ls [目录名]    | list                 | 查看当前目录下的内容    |
| 2    | pwd            | print work directory | 查看当前所在目录        |
| 3    | cd [目录名]    | change directory     | 切换目录                |
| 4    | touch [文件名] | touch                | 如果文件不存在,创建文件 |
| 5    | mkdir [目录名] | make directory       | 创建目录                |
| 6    | rm [文件名]    | remove               | 删除指定文件            |
|      |                |                      |                         |

**使用常用命令时还有一些小技巧：**

1. Tab键自动补全命令。

2. 连续两次Tab键，给出操作提示。
3.  使用上下箭头快速调出曾经使用过的命令。
4.  使用clear命令或者Ctrl+l快捷键实现清屏。

##### 3.2操作文件命令

+ **ls**

```
作用：显示指定目录下的内容
语法：ls [-al] [dir]
说明：1、 -a  显示所有文件以及目录（包括隐藏文件）
	 2、 -l  除了显示文件名以外，同时将文件形态、权限、拥有者和文件大小等详细信息列出
	 
提示：因为在使用ls命令时会经常用到-l，所以Linux将“ ls -l” 简写成 ll。
```

示例：

![image-20230627182127655](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627182127655.png)

+ **cd**

```
作用：用于切换当前工作目录
语法：cd [dirName]
说明： ~  表示用户的home目录
	  .  表示当前目录
	  .. 表示当前目录位置上的上级目录
```

示例：

![image-20230627182638306](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627182638306.png)

+ **cat**

```
作用: 用于显示文件内容
语法: cat [-n] fileName

说明:
	-n: 由1开始对所有输出的行数编号

举例:
	cat /etc/profile		查看/etc目录下的profile文件内容
```

示例：

![image-20230627193955589](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627193955589.png)

+ **more**

```
作用: 以分页的形式显示文件内容
语法: more fileName

操作说明:
    回车键 	向下滚动一行
    空格键 	向下滚动一屏
    b 		返回上一屏
    q或者Ctrl+C	退出more
	
举例：
	more /etc/profile		以分页方式显示/etc目录下的profile文件内容
```

+ tail

```
作用: 查看文件末尾的内容
语法: tail [-f] fileName

说明:
	-f : 动态读取文件末尾内容并显示，通常用于日志文件的内容输出
	
举例: 
tail /etc/profile		默认显示/etc目录下的profile文件末尾10行的内容
tail -20 /etc/profile	显示/etc目录下的profile文件末尾20行的内容
tail -f /itcast/my.log	动态读取/itcast目录下的my.log文件末尾f行的内容并显示
```

示例：

![image-20230627194913149](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627194913149.png)

+ **mkdir**

```
作用: 创建目录
语法: mkdir [-p] dirName

说明: 
	-p: 确保目录名称存在，不存在的就创建一个。通过此选项，可以实现多层目录同时创建

举例: 
    mkdir itcast  在当前目录下，建立一个名为itcast的子目录
    mkdir -p itcast/test   在工作目录下的itcast目录中建立一个名为test的子目录，若itcast目录不存在，则建立一个
```

+ **rmdir**

```
作用: 删除空目录
语法: rmdir [-p] dirName

说明:
	-p: 当子目录被删除后使父目录为空目录的话，则一并删除

举例:
    rmdir itcast   删除名为itcast的空目录
    rmdir -p itcast/test   删除itcast目录中名为test的子目录，若test目录删除后itcast目录变为空目录，则也被删除
    rmdir itcast*   删除名称以itcast开始的空目录
```

+ **rm**

```
作用: 删除文件或者目录
语法: rm [-rf] name

说明: 
    -r: 将目录及目录中所有文件（目录）逐一删除，即递归删除
    -f: 无需确认，直接删除
	
举例: 
    rm -r itcast/     删除名为itcast的目录和目录中所有文件，删除前需确认
    rm -rf itcast/    无需确认，直接删除名为itcast的目录和目录中所有文件
    rm -f hello.txt   无需确认，直接删除hello.txt文件
```

##### 3.3拷贝移动命令

+ **cp**

```
作用: 用于复制文件或目录
语法: cp [-r] source dest

说明: 
	-r: 如果复制的是目录需要使用此选项，此时将复制该目录下所有的子目录和文件

举例: 
    cp hello.txt itcast/            将hello.txt复制到itcast目录中
    cp hello.txt ./hi.txt           将hello.txt复制到当前目录，并改名为hi.txt
    cp -r itcast/ ./itheima/    	将itcast目录和目录下所有文件复制到itheima目录下
    cp -r itcast/* ./itheima/ 	 	将itcast目录下所有文件复制到itheima目录下
```

+ **mv**

```
作用: 为文件或目录改名、或将文件或目录移动到其它位置
语法: mv source dest

举例: 
    mv hello.txt hi.txt                 将hello.txt改名为hi.txt
    mv hi.txt itheima/                  将文件hi.txt移动到itheima目录中
    mv hi.txt itheima/hello.txt   		将hi.txt移动到itheima目录中，并改名为hello.txt
    mv itcast/ itheima/                 如果itheima目录不存在，将itcast目录改名为itheima
    mv itcast/ itheima/                 如果itheima目录存在，将itcast目录移动到itheima目录中
```

示例：

​		mv 命令既能够改名，又可以移动，具体是改名还是移动，会根据输入的参数进行判定(如果第二个参数dest是一个已存在的目录,将执行移动操作,其他情况都是改名)。

![image-20230627201503213](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627201503213.png)

##### 3.4打包压缩命令

```
作用: 对文件进行打包、解包、压缩、解压
语法: tar  [-zcxvf]  fileName  [files]
    包文件后缀为.tar表示只是完成了打包，并没有压缩
    包文件后缀为.tar.gz表示打包的同时还进行了压缩

说明:
    -z: z代表的是gzip，通过gzip命令处理文件，gzip可以对文件压缩或者解压
    -c: c代表的是create，即创建新的包文件
    -x: x代表的是extract，实现从包文件中还原文件
    -v: v代表的是verbose，显示命令的执行过程
    -f: f代表的是file，用于指定包文件的名称

举例：
    打包
        tar -cvf hello.tar ./*		  		将当前目录下所有文件打包，打包后的文件名为hello.tar
        tar -zcvf hello.tar.gz ./*		  	将当前目录下所有文件打包并压缩，打包后的文件名为hello.tar.gz
		
    解包
        tar -xvf hello.tar		  			将hello.tar文件进行解包，并将解包后的文件放在当前目录
        tar -zxvf hello.tar.gz		  		将hello.tar.gz文件进行解压，并将解压后的文件放在当前目录
        tar -zxvf hello.tar.gz -C /usr/local     将hello.tar.gz文件进行解压，并将解压后的文件放在/usr/local目录
```

示例：

（1）打包![image-20230627202246013](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627202246013.png)

（2）打包

![image-20230627202412488](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627202412488.png)

（3）解包

![image-20230627202601087](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627202601087.png)

（4）解包

![image-20230627202717064](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627202717064.png)

（5）解包到指定目录

![image-20230627202842653](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627202842653.png)

##### 3.5文本编辑工具

​		什么是Linux中的文本编辑工具？类似于Windows中的记事本，vi 和 vim 是Linux提供的两种文本编辑工具。vi的语法是: vi fileName。

​		vim是基于vi发展得来的功能更加强大的文本编辑工具。想使用vim命令，需要先安装vim，输入  yum install vim  来安装。

**vim使用**

​		（1）vim在编辑文件时，打开文件的命令是 vim [fileName]  。如果文件存在的话则会直接打开，不存在的话则会新建文件。

​		（2）vim在进行文本编辑时共分为三种模式，分别是 命令模式（Command mode），插入模式（Insert mode）和底行模式（Last line mode）。这三种模式之间可以相互切换。我们在使用vim时一定要注意我们当前所处的是哪种模式。

**注意：当使用 vim [fileName]进入文件时，默认进入命令模式，进入其他两种模式都需要先进入命令模式。处于命令模式的时候，可以通过方向键、gg、G查看文件。**

+ 命令模式

```
  | 命令模式指令 | 含义                              |
  | ------------ | --------------------------------- |
  | gg           | 定位到文本内容的第一行            |
  | G            | 定位到文本内容的最后一行          |
  | dd           | 删除光标所在行的数据              |
  | ndd          | 删除当前光标所在行及之后的n行数据 |
  | u            | 撤销操作                          |
  | shift+zz     | 保存并退出                        |
  | i 或 a 或 o  | 进入插入模式                      |
```

+ 插入模式

```
	| 插入模式下可以对文件内容进行编辑
	| 在命令模式下按下[i,a,o]任意一个，可以进入插入模式。进入插入模式后，下方会出现【insert】字样
	| 在插入模式下按下ESC键，回到命令模式
```

+ 底行模式

```
	| 底行模式下可以通过命令对文件内容进行查找、显示行号、退出等操作
	| 在命令模式下按下[:,/]任意一个，可以进入底行模式
	| 通过/方式进入底行模式后，可以对文件内容进行查找
	| 通过:方式进入底行模式后，可以输入wq（保存并退出）、q!（不保存退出）、set nu（显示行号）
```

![image-20230627211628583](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230627211628583.png)

##### 3.6查找命令

```
作用: 从指定文件中查找指定的文本内容
语法: grep word fileName
举例: 
    grep Hello HelloWorld.java	查找HelloWorld.java文件中出现的Hello字符串的位置
    grep hello *.java			查找当前目录中所有.java结尾的文件中包含hello字符串的位置
```

##### 3.7查询进程命令

+ ps -ef|grep 

```
	ps命令是linux下非常强大的进程查看命令，通过ps -ef可以查看当前运行的所有进程的详细信息。
	"|" 在Linux中称为管道符，可以将前一个命令的结果输出给后一个命令作为输入。
	使用ps命令查看进程时，经常配合管道符和查找命令 grep 一起使用，来查看特定进程
	systemctl是管理Linux中服务的命令，可以对服务进行启动、停止、重启、查看状态等操作。
	可以通过 kill -9 来结束进程。
```

##### 3.8查询防火墙命令

| 操作                         | 指令                                                         | 备注                 |
| ---------------------------- | ------------------------------------------------------------ | -------------------- |
| 查看防火墙状态               | systemctl status firewalld / firewall-cmd --state            |                      |
| 暂时关闭防火墙               | systemctl stop firewalld                                     |                      |
| 永久关闭防火墙(禁用开机自启) | systemctl disable firewalld                                  | ==下次启动,才生效==  |
| 暂时开启防火墙               | systemctl start firewalld                                    |                      |
| 永久开启防火墙(启用开机自启) | systemctl enable firewalld                                   | ==下次启动,才生效==  |
| 开放指定端口                 | firewall-cmd --zone=public --add-port=8080/tcp --permanent   | ==需要重新加载生效== |
| 关闭指定端口                 | firewall-cmd --zone=public --remove-port=8080/tcp --permanent | ==需要重新加载生效== |
| 立即生效(重新加载)           | firewall-cmd --reload                                        |                      |
| 查看开放端口                 | firewall-cmd --zone=public --list-ports                      |                      |

### 4.Linux项目布署(重点)

##### 4.1软件安装方式

​		在Linux系统中，安装软件的方式主要有四种，这四种安装方式的特点如下：

| 安装方式         | 特点                                                         |
| ---------------- | ------------------------------------------------------------ |
| 二进制发布包安装 | 软件已经针对具体平台编译打包发布，只要解压，修改配置即可     |
| rpm安装          | 软件已经按照redhat的包管理规范进行打包，使用rpm命令进行安装，==不能自行解决库依赖问题== |
| yum安装          | 一种在线软件安装方式，本质上还是rpm安装，自动下载安装包并安装，安装过程中自动解决库依赖问题(安装过程需要联网) |
| 源码编译安装     | 软件以源码工程的形式发布，需要自己编译打包                   |

##### 4.2安装项目软件

**安装好资料中的JDK并配置好环境：**

使用vim命令修改/etc/profile文件，在文件末尾加入以下配置：

```
JAVA_HOME=/usr/local/jdk1.8.0_171
PATH=$JAVA_HOME/bin:$PATH
```

添加配置后输入‘ source  /etc/profile ’ 重新加载profile文件。

**同样用二进制发布包的形式安装Tomcat：**

​		将上传的安装包解压后进入Tomcat的bin目录，并执行命令启动Tomcat：

```
cd /usr/local/apache-tomcat-7.0.57/

cd bin

sh startup.sh或者./startup.sh
```

​		想访问倒Tomcat有两种方法：

（1）关闭防火墙

```
systemctl stop firewalld
```

(2)开放8080端口

```
开放8080端口号
firewall-cmd --zone=public --add-port=8080/tcp --permanent

重新加载防火墙
firewall-cmd --reload
```

**安装MYSQL**

​		因为CentOS7会自带mariadb数据库，并且与MYSQL数据库相互冲突，所以要先卸载掉它。

```
rpm -qa | grep mariadb			查询当前系统中安装的名称带mariadb的软件
```

应该如何登录MYSQL呢？第一次启动时，会自动生成root用户的访问密码并且输出在mysql的日志文件 /var/log/mysqld.log中，查询该文件可以获取到访问密码。

```
cat /var/log/mysqld.log | grep password

通过上述指令，查询日志文件内容中包含password的行信息。
```

**修改MYSQL密码**

```
1. 登录mysql（复制日志中的临时密码登录）
	mysql -uroot -p								

2. 修改密码
    set global validate_password_length=4;			设置密码长度最低位数
    set global validate_password_policy=LOW;		设置密码安全等级低，便于密码可以修改成root
    set password = password('root');				设置密码为root
    
3. 开启访问权限
    grant all on *.* to 'root'@'%' identified by 'root';
    flush privileges;
```

##### 4.3手动部署项目

**1.在idea中给SpringBoot打成jar包**

​		先在本地中启动工程，若能正常访问就执行package指令将项目打成jar包。

![image-20230628120543304](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628120543304.png)

**2. 将jar包上传到Linux服务器**

将打好的jar包上传至Linux服务器的 /usr/local/app 目录下。 先执行指令创建app目录。

```
A. 在/usr/local下创建目录app
mkdir /usr/local/app

B. 切换到app目录下
cd /usr/local/app

C. 执行指令,进行jar包上传
rz
```

**3.启动SpringBoot程序**

​		执行  java -jar helloworld-1.0-SNAPSHOT.jar  命令启动程序。

注意：springboot项目没有配置端口号，默认是8080端口，和Tomcat的端口号冲突了，所以启动springboot项目需要将Tomcat停止。

![image-20230628143254137](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628143254137.png)

​		此时项目存在一个问题，就是程序会采用控制台霸屏的形式运行程序，当前项目启动的这个窗口被霸屏占用了，如果我们把这个窗口关闭掉(或ctrl+c)，当前服务也就访问不到了。

​		想要后台运行项目需要用到一个命令：no hang up（不挂起），此时启动项目的命令为：nohup java -jar helloworld-1.0-SNAPSHOT.jar &> hello.log &

### 5.Linux权限

​		什么是Linux权限？当要运行一个Shell脚本时，就需要对它设置权限，由于linux系统是一个多用户的操作系统，并且针对每一个用户，Linux会严格的控制操作权限。

```
1. ==chmod==（英文全拼：change mode）命令是控制用户对文件的权限的命令

2. Linux中的权限分为三种 ：读(r)、写(w)、执行(x)

3. Linux文件权限分为三级 : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）

4. 只有文件的所有者和超级用户可以修改文件或目录的权限

5. 要执行Shell脚本需要有对此脚本文件的执行权限(x)，如果没有则不能执行
```

Linux系统中权限描述如下: 

![image-20230628152832881](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628152832881.png)

![image-20230628152840460](http://typora-img-zhangle.superaiclub.com/javaStudyImg/image-20230628152840460.png)

chmod命令可以使用八进制数来指定权限(0 - 代表无 , 1 - 执行x , 2 - 写w , 4 - 读r):

|  值  |      权限      | rwx  |
| :--: | :------------: | :--: |
|  7   | 读 + 写 + 执行 | rwx  |
|  6   |    读 + 写     | rw-  |
|  5   |   读 + 执行    | r-x  |
|  4   |      只读      | r--  |
|  3   |   写 + 执行    | -wx  |
|  2   |      只写      | -w-  |
|  1   |     只执行     | --x  |
|  0   |       无       | ---  |

示例：

```
chmod 777 bootStart.sh   为所有用户授予读、写、执行权限
chmod 755 bootStart.sh   为文件拥有者授予读、写、执行权限，同组用户和其他用户授予读、执行权限
chmod 210 bootStart.sh   为文件拥有者授予写权限，同组用户授予执行权限，其他用户没有任何权限
```

