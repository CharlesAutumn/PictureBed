## Docker

### 基础篇

### Docker简介

![image-20240406204016436](D:\Typora\picture\image-20240406204016436.png)

但是这里就是当做一条鲸鱼

镜像文件也叫映像文件

就是说开发的人员直接打包，运维的兄弟就方便操作了

系统平滑移植，容器虚拟化技术

软件自带了环境的安装

搬家的过程叫做镜像

![image-20240116190454813](D:\Typora\picture\image-20240116190454813.png)

一句话：一次镜像，处处运行

解决了运行环境和配置问题的软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术

![image-20240116191318665](D:\Typora\picture\image-20240116191318665.png)

容器与虚拟机比较

![image-20240116192720886](D:\Typora\picture\image-20240116192720886.png)

Docker的出现引出了一个新的职位的变化，就是开发/运维工程师（DevOps）新一代开发工程师

Docker是内核级虚拟化

![image-20240116193517353](D:\Typora\picture\image-20240116193517353.png)

### 重要的概念

"高可用"（High Availability）是指一个系统能够在大部分甚至全部时间内保持可用状态的能力。具有高可用性的系统通常能够快速、可靠地响应用户请求，即使在面临硬件故障、软件问题或其他意外情况时也能够继续正常运行。

镜像文件貌似一面镜子，将真品进行赋值和拷贝，docker中镜像文件是一个只读的文件，分层构建，每个镜像文件都有一个唯一的标识符，称为镜像ID。使用Docker的镜像层叠功能，可以将多个镜像文件组合在一起构建复杂的程序。

容器是镜像通过run命令得到的，镜像通过pull拉取

Docker镜像是一种轻量级、可执行（run命令执行）的独立的软件包，镜像包含了软件运行所需的所有内容，例如把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。docker的容器实例只有通过镜像image才可以创建出来。

Docker的镜像是一层一层叠加的，我的理解是，某个镜像会基于一个基底，然后通过每次添加新功能，一次新功能的叠加就是新加一层，最终得到我们需要的镜像，所以我们在pull镜像的时候，下载的时候可以看到很多download。下载过程中，每一个pull，就是一层。这个一层一层就是**联合文件系统（UnionFS）**。UnionFS文件系统是一种分层、轻量级且高性能的文件系统。联合文件系统是Docker镜像的基础，**镜像可以通过分层来进行继承，这个继承我的理解是，已经存在的，可以复用的意思**。

Docker镜像是由一层一层的文件系统组成，这种层级基础是联合文件系统UnionFS，联合文件系统UnionFS由两部分bootfs和rootfs两者组成。

bootfs(boot file system) 主要包含bootloader（boot加载器）和kernel（内核）, bpotloader 主要是引导加载kernel,加载镜像的时候，会通过bootloader加载kernal，Docker镜像最底层是bootfs，当boot加载完成后整个kernal内核都在内存中了，bootfs也就可以卸载，值得注意的是，bootfs是被所有镜像共用的，许多镜像images都是在base image(rootfs)基础上叠加的。**也就是说bootloader推动kernel加载，kernel加载到内存中，bootfs就可以卸载了。**
rootfs (root file system)，在bootfs之上。包含的就是典型Linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Centos等等。**rootfs 在bootfs之上，相当于各种操作系统的发行版**

![image-20240404201002837](D:\Typora\picture\image-20240404201002837.png)

最开始只有bootsfs和基础image，然后添加A，B，得到我们想要的镜像

docker三要素：镜像（image）、容器（container）、仓库（repository）三件套

镜像就是模板，使用模板run出来的就是容器，存放模板的地方叫仓库

![image-20240116195114438](D:\Typora\picture\image-20240116195114438.png)

![image-20240116195246205](D:\Typora\picture\image-20240116195246205.png)

所谓的简易版是指：最小的最赖以生存的Linux内核文件，不需要的不加载

![image-20240116195656312](D:\Typora\picture\image-20240116195656312.png)

由于魔法，我们一般访问国内阿里云

![image-20240116195829917](D:\Typora\picture\image-20240116195829917.png)

docker本身是一个管理引擎这句话很有利于理解

一个容器就相当于一种服务

**其实就是类和实例**

docker就像是一个鲸鱼，容器就是集装箱，鲸鱼的背上驮着一个一个集装箱（就像是一个真的打包好的项目，中间一定是有很多种服务的，mysql,go-zero，每一个服务就是一个箱子，所有的箱子组成一个项目）那要是只打包一个项目的话，就是鲸鱼的背上只有一个大的集装箱

### daemon守护进程

#### 1、定义

+ 守护进程是**运行在后台**的一种特殊进程，它独立于控制终端并且周期性地执行某种任务或循环等待处理某些事件的发生；它不需要用户输入就能运行而且提供某种服务，不是对整个系统就是对某个用户程序提供服务。Linux系统的大多数服务器就是通过守护进程实现的。

+ 守护进程一般在系统启动时开始运行，除非强行终止，否则直到系统关机才随之一起停止运行；

+ 守护进程一般都以root用户权限运行，因为要使用某些特殊的端口（1-1024）或者资源；

+ 守护进程的父进程一般都是init进程，因为它真正的父进程在fork出守护进程后就直接退出了(什么意思？？？？)，所以守护进程都是孤儿进程，由init接管；

+ 守护进程是非交互式程序，没有控制终端，所以任何输出，无论是向标准输出设备stdout还是标准出错设备stderr的输出都需要特殊处理。

+ 守护进程的名称通常以d结尾，比如sshd、xinetd、crond等

#### 2、作用

- 守护进程是一个生存周期较长的进程，通常独立于控制终端并且周期性的执行某种任务或者等待处理某些待发生的事件
- 大多数服务都是通过守护进程实现的
- 关闭终端，相应的进程都会被关闭，而守护进程却能够突破这种限制

常见的守护进程就是**数据库服务器mysqld**等。

https://blog.csdn.net/JMW1407/article/details/108412836 原文在此，有说法

### Docker架构图解

![image-20240116200332666](D:\Typora\picture\image-20240116200332666.png)

这里是先看本地有没有镜像

最细的虚线代表看看本地有没有这个redis镜像，有的话，run容器

没有的话，去仓库下载

### docker工作原理

![image-20240116200249203](D:\Typora\picture\image-20240116200249203.png)

docker的守护进程运行在主机上

### docker整体架构及底层通信原理简述

![image-20240404205455831](D:\Typora\picture\image-20240404205455831.png)

首先是cs结构，后端是一个松耦合的结构，众多模块各司其职

1--> 使用client端，就是使用命令行窗口和服务器建立通信，使用pull进行远程镜像文件的拉取

![image-20240404205736813](D:\Typora\picture\image-20240404205736813.png)

2--> 这里的daemon也就是后台守护进程，是主体架构，提供了docker server，通过docker server连上服务器端的守护进程

3--> 每一个工作都是以job的形式存在，引擎才是干活的

![image-20240404210405292](D:\Typora\picture\image-20240404210405292.png)

4--> 

![image-20240404210423435](D:\Typora\picture\image-20240404210423435.png)

5-->

![image-20240404210701126](D:\Typora\picture\image-20240404210701126.png)

6--> 

![image-20240404210734060](D:\Typora\picture\image-20240404210734060.png)

7-->





### Docker安装

https://www.docker.com/  docker官网

https://docs.docker.com/engine/install/centos/ 安装地址

![image-20240404211714988](D:\Typora\picture\image-20240404211714988.png)

就是说真的高手都是看官网直接文档学的，但是要一定的基础才能看懂嗯嗯

一个是官网一个是镜像文件的下载网站dockerHub

Docker并非是一个通用的工具，它依赖于已经存在并运行的Linux内核环境，也就是说，你的**docker必须部署在Linux内核系统上**。

![image-20240404211610793](D:\Typora\picture\image-20240404211610793.png)

#### 前提条件

![image-20240116194502374](D:\Typora\picture\image-20240116194502374.png)

```bash
cat /etc/redhat-release
```

![image-20240404202007188](D:\Typora\picture\image-20240404202007188.png)

#### 卸载旧版本

![image-20240404211747700](D:\Typora\picture\image-20240404211747700.png)

就在和安装一个界面中

```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

#### yum安装gcc相关

首先是魔法

```bash
yum -y install gcc
yum -y install gcc-c++
```

#### 安装需要的软件包

时代变迁，多多看看官方文档

```bash
sudo yum install -y yum-utils
```

#### 设置stable镜像仓库

官方的命令

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

emmmmm我有魔法，所以我感觉我直接使用官网的就行   1秒结束....笑死我了

```bash
//阿里云的镜像
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

#### 更新yum软件包索引

```bash
yum makecache fast
```

官网上没有，就相当于重建一下yum的下载索引，以后yum的安装稍微快一些，这属于linux的基础命令

#### 安装docker

```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

一路 y

#### 启动docker

```bash
sudo systemctl start docker
```

没有消息就是好消息

#### 测试docker

看看进程是否在

```bash
ps -ef|grep docker
```

看看版本信息

```bash
docker version
```

你好,docker

```bash
docker run hello-world
```

#### 卸载docker

```bash
systemctl stop docker
sudo yum remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

### 镜像加速器配置

这里注册了阿里云的账号，使用手机号进行注册的，绑定的支付宝

![image-20240405130751814](D:\Typora\picture\image-20240405130751814.png)

```bash
https://7pha4xsy.mirror.aliyuncs.com
```

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://7pha4xsy.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### HelloWorld解析

![image-20240405131105803](D:\Typora\picture\image-20240405131105803.png)

![image-20240405131226485](D:\Typora\picture\image-20240405131226485.png)

你的电脑上的docker就是那个鲸鱼

### docker快的原理解释

![image-20240405131617051](D:\Typora\picture\image-20240405131617051.png)

![image-20240405131853075](D:\Typora\picture\image-20240405131853075.png)

下面的两层是一样的

![image-20240405131924528](D:\Typora\picture\image-20240405131924528.png)

**理论实操小总结**

### docker常用命令

#### 帮助启动类命令

![image-20240405132510075](D:\Typora\picture\image-20240405132510075.png)

#### 镜像命令

![image-20240405132812066](D:\Typora\picture\image-20240405132812066.png)

镜像ID是唯一性的

![image-20240405133343338](D:\Typora\picture\image-20240405133343338.png)

![image-20240405162449485](D:\Typora\picture\image-20240405162449485.png)

可以-qa 一起用

这个search 是在远程仓库中搜索，不是在本地进行搜索，就是看看远程仓库有没有这个

![image-20240405133853288](D:\Typora\picture\image-20240405133853288.png)

点赞数  和  是否是官方认证 

![image-20240405160308998](D:\Typora\picture\image-20240405160308998.png)

![image-20240405160553423](D:\Typora\picture\image-20240405160553423.png)

![image-20240405160756723](D:\Typora\picture\image-20240405160756723.png)

![image-20240405161101304](D:\Typora\picture\image-20240405161101304.png)

![image-20240405161114616](D:\Typora\picture\image-20240405161114616.png)

rmi是删除命令，笑死我了，后面可以加名字也可以加ID

![image-20240405161959250](D:\Typora\picture\image-20240405161959250.png)

我也报这个错了，提示你有些容器在使用这个镜像，然后-f 强力删除就完了

![image-20240405162128255](D:\Typora\picture\image-20240405162128255.png)

这个才是全部的ID，上面的都是一小部分，但是也能够代表唯一的特点了

删除一个镜像的时候使用ID，两个多个使用名字

![image-20240405162245345](D:\Typora\picture\image-20240405162245345.png)

![image-20240405162631229](D:\Typora\picture\image-20240405162631229.png)

**面试题：谈谈docker虚悬镜像是什么？**

 ![image-20240405162842014](D:\Typora\picture\image-20240405162842014.png)

![image-20240405162823759](D:\Typora\picture\image-20240405162823759.png)

正常的镜像都是有名有性的，虚悬镜像产生的原因是因为docker在构建的时候，出现了一些问题，就会导致产生虚悬镜像，这种建议删除

#### 容器命令

![image-20240405163517940](D:\Typora\picture\image-20240405163517940.png)

这里就是说使用ubuntu进行演示，正常在虚拟机上进行ubuntu的安装很复杂，这次正好体验一下docker的方便之处

![image-20240405164049462](D:\Typora\picture\image-20240405164049462.png)

![image-20240405164217818](D:\Typora\picture\image-20240405164217818.png)

![image-20240405165033952](D:\Typora\picture\image-20240405165033952.png)

直接docker run ubuntu是没有任何反应的

使用-it bash  / -it /bin/bash的话，可以调出伪终端

![image-20240405165314752](D:\Typora\picture\image-20240405165314752.png)

![image-20240405165444311](D:\Typora\picture\image-20240405165444311.png)

![image-20240405165610813](D:\Typora\picture\image-20240405165610813.png)

可以发现这个容器ID就是上面的命令行ID号

Up 24 seconds表示已经运行了24秒钟

POSTS表示对外暴露端口，没有暴露，因为ubuntu不用暴露

最后一个参数就是系统随机分配的名字

哎呀woc我没有写 /bin/bash，但是上面图中下面的图中默认是这个

![image-20240405170139224](D:\Typora\picture\image-20240405170139224.png)

![image-20240405170332468](D:\Typora\picture\image-20240405170332468.png)

上面是docker ps 的选项

![image-20240405170426681](D:\Typora\picture\image-20240405170426681.png)

![image-20240405170513123](D:\Typora\picture\image-20240405170513123.png)

![image-20240405170810973](D:\Typora\picture\image-20240405170810973.png)

![image-20240405171321874](D:\Typora\picture\image-20240405171321874.png)

你不可以（正常）删除正在运行的容器，但是可以使用-f 强制删除

![image-20240405171503871](D:\Typora\picture\image-20240405171503871.png)

![image-20240405171531536](D:\Typora\picture\image-20240405171531536.png)

很危险，基本不使用

**重要知识点**

![image-20240405172247367](D:\Typora\picture\image-20240405172247367.png)

这里你-d之后，会返回一个流水号，表示确实有这个，但是由于docker的机制问题，会立即自杀，因为感觉没有需要我，机制就是必须要一个前台进程来保证后台能活着，就是-it方式

![image-20240405172423185](D:\Typora\picture\image-20240405172423185.png)

![image-20240405172737524](D:\Typora\picture\image-20240405172737524.png)

有的-d会自杀，但是有的不会自杀

![image-20240405173153470](D:\Typora\picture\image-20240405173153470.png)

看看

docker logs id编号    查看某个容器的日志

![image-20240405173444342](D:\Typora\picture\image-20240405173444342.png)

![image-20240405173515767](D:\Typora\picture\image-20240405173515767.png)

![image-20240405173850998](D:\Typora\picture\image-20240405173850998.png)

![image-20240405173948428](D:\Typora\picture\image-20240405173948428.png)

![image-20240405174034036](D:\Typora\picture\image-20240405174034036.png)

![image-20240405174300529](D:\Typora\picture\image-20240405174300529.png)

![image-20240405174320807](D:\Typora\picture\image-20240405174320807.png)

![image-20240405174439123](D:\Typora\picture\image-20240405174439123.png)

![image-20240405174549912](D:\Typora\picture\image-20240405174549912.png)

![image-20240405174729024](D:\Typora\picture\image-20240405174729024.png)

![image-20240405174829029](D:\Typora\picture\image-20240405174829029.png)

![image-20240405174930943](D:\Typora\picture\image-20240405174930943.png)

![image-20240405175016642](D:\Typora\picture\image-20240405175016642.png)

![image-20240405175128227](D:\Typora\picture\image-20240405175128227.png)

直接一锅端

![image-20240405175206505](D:\Typora\picture\image-20240405175206505.png)

![image-20240405175354639](D:\Typora\picture\image-20240405175354639.png)

也就是说后面的那几个名字随便写，然后生成的是一个镜像文件

ok这些命令对于开发够够的，好好学吧

### docker镜像分层

前面都只是简单的理解，这里炒回锅肉，不是老生常谈，是理解原理

我们之前一直是使用网络上的镜像，那么我们能不能自己做一个镜像呢？

接下来详细熟悉一下所有概念

![image-20240405184140433](D:\Typora\picture\image-20240405184140433.png)

镜像就像是蛋糕一样，一层一层的堆叠

![image-20240405184342393](D:\Typora\picture\image-20240405184342393.png)

![image-20240405184623842](D:\Typora\picture\image-20240405184623842.png)

基础镜像就是超类

![image-20240405185019849](D:\Typora\picture\image-20240405185019849.png)

![image-20240405185110722](D:\Typora\picture\image-20240405185110722.png)

注意是底层直接使用了host的内核

bootfs很多基本一样，但是rootfs一般是不一样的，就像是发行版本一样

**分层的意义在何？**就是为了复用，这么说不形象，比如说，一般ubuntu是没有vim命令的，这个时候我就可以利用分层的特点，单独下载一个vim功能，整合到ubuntu中。继承与ubuntu，而且镜像的每一层都可以被共享

![image-20240405185654967](D:\Typora\picture\image-20240405185654967.png)

![image-20240405185722325](D:\Typora\picture\image-20240405185722325.png)

![image-20240405185824155](D:\Typora\picture\image-20240405185824155.png)

### docker commit命令

![image-20240405190139840](D:\Typora\picture\image-20240405190139840.png)

![image-20240405190835861](D:\Typora\picture\image-20240405190835861.png)

其实思路就是下两个镜像，堆叠一下，变成一个新的分层镜像 ==不是这个思路啊，我猜的==

是这样的，在ubuntu中下载

```bash
apt-get update
apt-get -y install vim
```

![image-20240405191554236](D:\Typora\picture\image-20240405191554236.png)

![image-20240405191821403](D:\Typora\picture\image-20240405191821403.png)

![image-20240405192021176](D:\Typora\picture\image-20240405192021176.png)

### 本地镜像发布到阿里云

![image-20240405192218730](D:\Typora\picture\image-20240405192218730.png)

![image-20240405192249909](D:\Typora\picture\image-20240405192249909.png)

![image-20240405192305902](D:\Typora\picture\image-20240405192305902.png)

dockerfile是手工编写形成镜像文件

![image-20240405192626972](D:\Typora\picture\image-20240405192626972.png)

![image-20240405192722384](D:\Typora\picture\image-20240405192722384.png)

![image-20240405192950002](D:\Typora\picture\image-20240405192950002.png)

![image-20240405193125469](D:\Typora\picture\image-20240405193125469.png)

你要先有命名空间才能有仓库

![image-20240405193614078](D:\Typora\picture\image-20240405193614078.png)

感觉还是很有用的，好好设置了一下 Gzy20041029

然后生成自己的库之后，会生成一个文档，照着做就行了

![image-20240405194639365](D:\Typora\picture\image-20240405194639365.png)

![image-20240405194529986](D:\Typora\picture\image-20240405194529986.png)

md我有魔法，我就官方

![image-20240405194711211](D:\Typora\picture\image-20240405194711211.png)

![image-20240405194739278](D:\Typora\picture\image-20240405194739278.png)

 ![image-20240406140107578](D:\Typora\picture\image-20240406140107578.png)

-p 就是端口映射  小写的p就是指定端口映射

![image-20240406140312471](D:\Typora\picture\image-20240406140312471.png)

一定是外部先访问自己的宿主机嘛。访问宿主机8080的时候映射到docker的80端口

-v 是容器数据卷的知识点

![image-20240406140426715](D:\Typora\picture\image-20240406140426715.png)

![image-20240406140547723](D:\Typora\picture\image-20240406140547723.png)

![image-20240406140623288](D:\Typora\picture\image-20240406140623288.png)

![image-20240406140730148](D:\Typora\picture\image-20240406140730148.png)

![image-20240406140820623](D:\Typora\picture\image-20240406140820623.png)

![image-20240406140915076](D:\Typora\picture\image-20240406140915076.png)

![image-20240406141012665](D:\Typora\picture\image-20240406141012665.png)

私服库默认不支持http的因为安全问题嘛。然后要配置支持

![image-20240406141115507](D:\Typora\picture\image-20240406141115507.png)

![image-20240406141153316](D:\Typora\picture\image-20240406141153316.png)

![image-20240406141211926](D:\Typora\picture\image-20240406141211926.png)

![image-20240406141231237](D:\Typora\picture\image-20240406141231237.png)

看看这会有什么了

![image-20240406141259114](D:\Typora\picture\image-20240406141259114.png)

![image-20240406141336168](D:\Typora\picture\image-20240406141336168.png)

![image-20240406141354749](D:\Typora\picture\image-20240406141354749.png)

这个时候就有了

### docker容器数据卷

**阳哥说一定要动手！！！！少一点胡思乱想**

**心中无女人，编码自然神；忘掉心上人，抬手灭红尘；**

![image-20240406141703164](D:\Typora\picture\image-20240406141703164.png)

有一个参数建议加，不加没有关系的，但是建议加

![image-20240406141740553](D:\Typora\picture\image-20240406141740553.png)

![image-20240406141800187](D:\Typora\picture\image-20240406141800187.png)

这个挂载是插u盘的

注意每一个容器只是一个迷你版的Linux用户，不是真的具有root权限，你要开启这个root权限，就要使用这个命令

上节课中的-v 参数就是添加自定义容器卷的命令，: 左边的就是宿主机的路径，右边的容器内的路径 实现了宿主机和容器内两个路径的信息共享和互联

![ ](D:\Typora\picture\image-20240406142256077.png)

![image-20240406142537301](D:\Typora\picture\image-20240406142537301.png)

![image-20240406142608540](D:\Typora\picture\image-20240406142608540.png)

![image-20240406142721503](D:\Typora\picture\image-20240406142721503.png)

说的很有道理woc

 ![image-20240406142839902](D:\Typora\picture\image-20240406142839902.png)

有点感觉就是如果你要插一个u盘就是挂载一个u盘，然后你要将数据卷的位置定在u盘中，就是需要权限的设置

![image-20240406143032935](D:\Typora\picture\image-20240406143032935.png)

重用数据就像是，硬盘外再接一个硬盘![image-20240406143139464](D:\Typora\picture\image-20240406143139464.png)

这个直接实时生效是指，你在docker中整的数据，可以直接同步到数据卷中的，不用定时备份不用手动备份 

![image-20240406143352161](D:\Typora\picture\image-20240406143352161.png)

和这个有点像，但是比这个nb多了

**案例**

![image-20240406143443811](D:\Typora\picture\image-20240406143443811.png)

![image-20240406144850460](D:\Typora\picture\image-20240406144850460.png)

![image-20240406144906778](D:\Typora\picture\image-20240406144906778.png)

![image-20240406144924201](D:\Typora\picture\image-20240406144924201.png)

![image-20240406145217331](D:\Typora\picture\image-20240406145217331.png)

宿主机是没有这个目录的，但是docker会帮我们创建这个文件，记得给出文件的写权限

以上的操作会同步到宿主机中

![image-20240406145406556](D:\Typora\picture\image-20240406145406556.png)



对应的在docker容器中也是可以的，就是说**是双向的**

挂载，这里就是挂载到宿主机嘛，就是挂在哪里

![image-20240406145642614](D:\Typora\picture\image-20240406145642614.png)

这个命令就是以json串的形式进行展示这个容器的相关信息

![image-20240406145718945](D:\Typora\picture\image-20240406145718945.png)

这个命令可以查看docker容器的挂载信息

![image-20240406145802900](D:\Typora\picture\image-20240406145802900.png)

**如果docker容器停了,stop了，但是你在宿主机上建了一个文件，然后再启动这个容器，这个文件依旧还有**

![image-20240406150036748](D:\Typora\picture\image-20240406150036748.png)

![image-20240406150205772](D:\Typora\picture\image-20240406150205772.png)

![image-20240406150224602](D:\Typora\picture\image-20240406150224602.png)

![image-20240406150304635](D:\Typora\picture\image-20240406150304635.png)

![image-20240406150344165](D:\Typora\picture\image-20240406150344165.png)

ro readonly

![image-20240406150402013](D:\Typora\picture\image-20240406150402013.png)

只是限制docker容器的

![image-20240406151849565](D:\Typora\picture\image-20240406151849565.png)

![image-20240406151858916](D:\Typora\picture\image-20240406151858916.png)

![image-20240406151909072](D:\Typora\picture\image-20240406151909072.png)

![image-20240406152109914](D:\Typora\picture\image-20240406152109914.png)

![image-20240406152056988](D:\Typora\picture\image-20240406152056988.png)

开始继承

![image-20240406152155697](D:\Typora\picture\image-20240406152155697.png)

红色的就是 -v 的全称

name后面的=加不加随你

![image-20240406152323049](D:\Typora\picture\image-20240406152323049.png)

![image-20240406152353627](D:\Typora\picture\image-20240406152353627.png)

![image-20240406152438062](D:\Typora\picture\image-20240406152438062.png)

三个三个文件都有！！

我先将爹停止，然后再在主机上建一个文件，在儿子中查看文件，照样有

![image-20240406152601554](D:\Typora\picture\image-20240406152601554.png)

u2只是继承了挂载的路径规则，u1死了跟他没有什么关系

![image-20240406152704927](D:\Typora\picture\image-20240406152704927.png)

![image-20240406152717135](D:\Typora\picture\image-20240406152717135.png)

如果u1又回来的话会怎么样呢？

![image-20240406152807773](D:\Typora\picture\image-20240406152807773.png)

woc都在！

### docker安装常用软件说明

![image-20240406153230322](D:\Typora\picture\image-20240406153230322.png)

之前要安装东西要配置这配置那的，现在就是统一安装步骤，就是容器嘛

![image-20240406153338064](D:\Typora\picture\image-20240406153338064.png)

#### 安装tomcat

![image-20240406153521554](D:\Typora\picture\image-20240406153521554.png)

![image-20240406153609714](D:\Typora\picture\image-20240406153609714.png)

![image-20240406153801044](D:\Typora\picture\image-20240406153801044.png)

然后看看有没有，不写tomcat就是看全部的，写了就是看看这个有没有

然后run啦

![image-20240406153918099](D:\Typora\picture\image-20240406153918099.png)

-P呢就是。。。一般不要使用

![image-20240406154007838](D:\Typora\picture\image-20240406154007838.png)

前是外部，后是内部的

![image-20240406154152864](D:\Typora\picture\image-20240406154152864.png)

查看成功没

![image-20240406154235833](D:\Typora\picture\image-20240406154235833.png)

运行这只死猫

你要在这个linux中的火狐中访问8080端口，但是返回的是404

![image-20240406154528864](D:\Typora\picture\image-20240406154528864.png)

为什么？

因为tomcat最新版本对于首页的访问发生了一些新的改变，这样的情况是正常的

![image-20240406154635461](D:\Typora\picture\image-20240406154635461.png)

![image-20240406154913147](D:\Typora\picture\image-20240406154913147.png)

进去这个tomcat，这里还爆了一个小错误，因为我没有立即指定bash

![image-20240406155001714](D:\Typora\picture\image-20240406155001714.png)

我们要修改webapps文件，这里面啥都没有

![image-20240406155146103](D:\Typora\picture\image-20240406155146103.png)

先给他删了

![image-20240406155311107](D:\Typora\picture\image-20240406155311107.png)

因为是目录啊，-r一下

![image-20240406155355242](D:\Typora\picture\image-20240406155355242.png)

这个才是真真正正有货的东西，改名一下

![image-20240406155445598](D:\Typora\picture\image-20240406155445598.png)

重新访问一下就好使了

我累个烧刚啊，不好使。。。。。。。cao

删了使用免修改版

![image-20240406160341560](D:\Typora\picture\image-20240406160341560.png)

删了，md

![image-20240406160439239](D:\Typora\picture\image-20240406160439239.png)

你不用pull了，本地没有，自动给你pull，就是说直接run就行

![image-20240406160702417](D:\Typora\picture\image-20240406160702417.png)



![image-20240406160756556](D:\Typora\picture\image-20240406160756556.png)

woc出来了

#### 安装mysql

![image-20240406195238084](D:\Typora\picture\image-20240406195238084.png)

![image-20240406195315639](D:\Typora\picture\image-20240406195315639.png)

-e 表示环境的意思

![image-20240406195756179](D:\Typora\picture\image-20240406195756179.png)

注意一个事情 ，如果你的linux已经装过mysql的话，那么3306端口很有可能已经被占用了

![image-20240406195935537](D:\Typora\picture\image-20240406195935537.png)

通过这个命令查看一下mysql是不是在启动这

![image-20240406200324516](D:\Typora\picture\image-20240406200324516.png)

![image-20240406200419578](D:\Typora\picture\image-20240406200419578.png)

默认就这四个

使用config命令查看端口

![image-20240406200512510](D:\Typora\picture\image-20240406200512510.png)

![image-20240406200609221](D:\Typora\picture\image-20240406200609221.png)

![image-20240406200618305](D:\Typora\picture\image-20240406200618305.png)

工具不准，要以docker容器为准  

![image-20240406201023177](D:\Typora\picture\image-20240406201023177.png)

![image-20240406201145319](D:\Typora\picture\image-20240406201145319.png)

![image-20240406201245702](D:\Typora\picture\image-20240406201245702.png)

![image-20240406201313750](D:\Typora\picture\image-20240406201313750.png)

![image-20240406201346307](D:\Typora\picture\image-20240406201346307.png)

![image-20240406201434069](D:\Typora\picture\image-20240406201434069.png)

![image-20240406201537432](D:\Typora\picture\image-20240406201537432.png)

前面是挂容器卷了，只要你删了，在此运行dockermysql容器，内容不会被删除，不会丢失！！！！！！！！！！！

#### 安装redis

端口号 6379

![image-20240406202046782](D:\Typora\picture\image-20240406202046782.png)

![image-20240406202126738](D:\Typora\picture\image-20240406202126738.png)

![image-20240406202321178](D:\Typora\picture\image-20240406202321178.png)

这里的redis.conf实际你使用过程中的文件，在学redis的时候相信应该会遇到

![image-20240406202612998](D:\Typora\picture\image-20240406202612998.png)

![image-20240406202814136](D:\Typora\picture\image-20240406202814136.png)

![image-20240406202837589](D:\Typora\picture\image-20240406202837589.png)

![image-20240406202916904](D:\Typora\picture\image-20240406202916904.png)

![image-20240406203046870](D:\Typora\picture\image-20240406203046870.png)

这里-v之后，就是说你容器内部的这个目录之下的内容就是你宿主机上的内容了，然后后面的参数就是表示你服务端启动的时候使用我写的文件conf

这里他说错了一个地方，就是docker run的时候，和/bin/bash无关，/bin/bash与exec -it 有关

![image-20240406203357209](D:\Typora\picture\image-20240406203357209.png)

redis本身默认是16个库，怎么证明你修改了conf文件，他是否真的使用了？

那你就是修改这个conf文件，16  -->   10

然后select 12，看看报不报错

![image-20240406203632375](D:\Typora\picture\image-20240406203632375.png)

![image-20240406203659108](D:\Typora\picture\image-20240406203659108.png)

### 高级篇

### mysql主从复制

基本上所有的厂子，不管是小厂还是大厂，都要进行Mysql的主从配置，最基本的是一主一从，从太多也用不上，太多了，还容易跟不上主的数据，因为有数据不同步的事，还容易出错。大部分，一般来说，中小厂都是一主一从的。

![image-20240406204653509](D:\Typora\picture\image-20240406204653509.png)

![image-20240406204855750](D:\Typora\picture\image-20240406204855750.png)

这里mysql和redis的安装先跳过了

### dockerfile

![image-20240407171632601](D:\Typora\picture\image-20240407171632601.png)

![image-20240407171648166](D:\Typora\picture\image-20240407171648166.png)

![image-20240407171705145](D:\Typora\picture\image-20240407171705145.png)

![image-20240407171839486](D:\Typora\picture\image-20240407171839486.png)

![image-20240407171932057](D:\Typora\picture\image-20240407171932057.png)

![image-20240407171950827](D:\Typora\picture\image-20240407171950827.png)

像是开中药

![image-20240407172102540](D:\Typora\picture\image-20240407172102540.png)

安装药方开药

![image-20240407172412322](D:\Typora\picture\image-20240407172412322.png)

![image-20240407172431663](D:\Typora\picture\image-20240407172431663.png)

![image-20240407172506707](D:\Typora\picture\image-20240407172506707.png)

![image-20240407172543449](D:\Typora\picture\image-20240407172543449.png)

![image-20240407172616710](D:\Typora\picture\image-20240407172616710.png)

![image-20240407172644905](D:\Typora\picture\image-20240407172644905.png)

这个有点难受，那就不使用，知道就行





![image-20240407172737587](D:\Typora\picture\image-20240407172737587.png)

![image-20240407172754685](D:\Typora\picture\image-20240407172754685.png)



![image-20240407172902265](D:\Typora\picture\image-20240407172902265.png)

一般不指定，没有必要

![image-20240407172932356](D:\Typora\picture\image-20240407172932356.png)

![image-20240407172948743](D:\Typora\picture\image-20240407172948743.png)

k v键值对

前面定义，后面就可以使用

![image-20240407173058474](D:\Typora\picture\image-20240407173058474.png)

![image-20240407173132494](D:\Typora\picture\image-20240407173132494.png)

两个有点类似

![image-20240408111351572](D:\Typora\picture\image-20240408111351572.png)

copy功能要限制一些，但是性能好一点呗

![image-20240408111804865](D:\Typora\picture\image-20240408111804865.png)

唯二的区别，还可以进行压缩文件的解压  docker cp

![image-20240408112242324](D:\Typora\picture\image-20240408112242324.png)

dockerfile中覆盖  还有是linux中使用 /bin/bash指定参数，也会覆盖，这个时候再访问8080端口就没有那只猫了 覆盖不是不执行

![image-20240408153400618](D:\Typora\picture\image-20240408153400618.png)

![image-20240408143733536](D:\Typora\picture\image-20240408143733536.png)

![image-20240408151838565](D:\Typora\picture\image-20240408151838565.png)

就是一个定参，一个传参 

### 数据卷挂载和目录挂载区别

两个文件覆盖规则是一样的

没有文件时，会将容器内的文件挂载到目录中，有文件时，会将数目录中的文件覆盖容器内的文件

数据卷挂载

没有指定数据卷的时候，会自动创建数据卷

- 如果挂载一个空的数据卷到容器中的一个非空目录中，那么这个目录下的文件会被复制到数据卷中。
- 如果挂载一个非空的数据卷到容器中的一个目录中，那么容器中的目录中会显示数据卷中的数据。如果原来容器中的目录中有数据，那么这些原始数据会被隐藏掉。【覆盖】

目录挂载

没有指定的时候，默认还是自动创建数据卷的 前面使用的是绝对路径

![image-20240408110600151](D:\Typora\picture\image-20240408110600151.png)

![image-20240408110500341](D:\Typora\picture\image-20240408110500341.png)





































































































































