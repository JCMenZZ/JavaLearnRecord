## 关于此篇（题外话）

   目前，作者也还是一名默默无闻的大学生，之所以写这篇文章，主要是最近接到了不少朋友的求助，他们在搭建hadoop集群时，出现了不少的问题，以至于无法成功搭建。这里统一写一份部署教程，帮助一些读者朋友解决一些微不足道的问题，也将hadoop集群的搭建流程及错误处理作以留存。好了，废话不多说了，进入正题。

**关于hadoop集群部署和相关软件的资料，可在相关资料一栏下，自行提取使用。**

## Linux系统搭建hadoop高性能集群（一）
相关软件版本

虚拟机软件：VMware Workstation15.5

Linux系统：CentOS-6.10

  ### 第一步  虚拟机的安装
   这里不再详述，不会的小伙伴可以选择百度或CSDN下查找相应的安装方法。

  ### 第二步  创建一个虚拟机

1、打开VMware软件，点击**新建虚拟机**，这里有两种方式。

**第一种**：在主界面上点击创建新的虚拟机
  ![创建虚拟机](https://img-blog.csdnimg.cn/20200410094952927.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzYyNDAzMQ==,size_16,color_FFFFFF,t_70#pic_center)**第二种**：点击文件按钮在下拉菜单中选择新建虚拟机
 ![创建虚拟机](https://img-blog.csdnimg.cn/20200410095148106.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzYyNDAzMQ==,size_16,color_FFFFFF,t_70#pic_center)

进入安装向导后，使用默认安装方式，连续点击下一步直到进入“选择客户机操作系统”，选择Linux，版本选择CentOS 64位；点击下一步后，命名虚拟机并选择安装位置；点击下一步，配置虚拟机的处理器，这里需要根据主机的性能和虚拟机数量配置核数，通常为双核一个处理器；在虚拟机的内存配置中选择默认（推荐）；磁盘容量可选择默认也可自行更改。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg01.PNG)

**注意**：在创建过程中的ISO安装镜像，选择稍后安装，不要使用自动（简易）安装。

### 第三步 虚拟机启动初始化

选中刚刚创建好的虚拟机，在CD/DVD(IDE)选项中，选中使用ISO镜像文件，将本机上的CentOS的ISO镜像文件路径填入文本框中，设置完成后，开启此虚拟机。进入系统安装界面后，选择第一条选项（Install or upgrade an existing system），引导驱动加载完成后，进入Disc Found界面，在此界面中选择skip后，选择系统使用的语言，选择完成后在存储设备警告界面选择yes...，设置主机名，设置网络配置，选择自动连接，选择时区，设置密码，在磁盘格式化界面单击将修改写入磁盘选项，单击Reboot，等待重启完成后即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg02.PNG)

**注意**：这里只安装命令行界面，桌面可视化不需要安装。

### 第四步 虚拟机克隆

将安装好系统的虚拟机关闭，右键单击虚拟机，选择克隆，因为我们的Hadoop集群需要分别设置namenode和datanode，所以这里需要根据个人主机的配置克隆虚拟机数量，这里我们克隆两台虚拟机，VMware提供了两种克隆方式，分别是完整克隆和链接克隆，这里我们使用完整克隆，在克隆虚拟机向导中为虚拟机命名，完成后等待片刻即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg03.PNG)



### 第五步 Linux系统网络配置

#### IP地址配置

在虚拟机的编辑下点击虚拟网络编辑器，在VMnet8下获取管理员权限，更改VMnet8的DHCP设置，根据子网IP和子网掩码，设置起始IP地址和结束IP地址，点击确定后即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg04.PNG)

进入本机以太网下，右键属性更改VMnet8的DNS配置和IP配置，IP地址设置必须和虚拟机网络的网关一致，DNS可选用```8.8.8.8```或```114.114.114.114```。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg05.PNG)

#### 配置主机名和IP映射

修改完成后进入克隆好的第一个虚拟机hadoop02，在命令行界面输入如下命令，配置主机名。

```shell
$ vi /etc/sysconfig/network
```

在HOSTNAME一栏进行主机名配置（依据个人配置），这里将三台主机的主机名依次配置为hadoop01、hadoop02、hadoop03。

主机名配置完成后，配置IP映射，输入如下命令，配置IP映射。

```shell
$ vi /etc/hosts
```

**注意**：主机的IP地址要和主机名对应。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg07.PNG)

#### 网络参数配置

输入如下命令，修改虚拟机网卡配置文件（配置该设备的MAC地址）

```shell
 $ vi /etc/udev/rules.d/70-persistent-net.rules 
```

根据每台虚拟机的网络适配器高级设置中的MAC地址，配置该主机的MAC地址，该配置文件下的ATTR{address}就是该主机的MAC地址，由于克隆了两台虚拟机，所以在所克隆的虚拟机的网卡配置文件中会有eth0和eth1两块网卡，这里我们删除eth0，保留eth1，并将eth1的NAME="eth1"改为NAME="eth0"，完成后保存退出即可。

输入如下命令，修改IP地址文件，设置静态IP。

```shell
$ vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

这里将“BOOTPROTO”后的参数改为static，“HWADDR”后的参数要符合本机的MAC地址，修改完成后在文件最末行添加如下所示（根据个人配置修改）。

```ini
IPADDR=该虚拟机的IP地址
NETMASK=255.255.255.0
GATEWAY=虚拟机的网关IP
DNS1=域名解析器对应PC端电脑的DNS地址
```

配置完成后，即可进行效果验证，即输入命令ifconfig查看IP配置是否生效，接下来可使用ping命令，查看是否可以链接外部网站。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoop-img08.PNG)

#### 注意

上述的操作，在所有的虚拟机下都要进行配置，否则在后续的配置过程中，会出现错误，导致hadoop集群无法正常使用。

### 第六步 SSH服务配置

首先，输入如下命令查看当前虚拟机是否安装了ssh服务。

```shell
$ rpm -qa | grep ssh
```

同时使用``ps -e | grep sshd``命令查看ssh服务是否启动，通常为启动状态。如果未安装SSH服务，可执行``yum install openssh-server``命令进行安装。

其次使用终端仿真程序（SecureCRT/Xshell），连接虚拟机，这里本人采用的是SecureCRT，输入需要连接的IP地址和用户名、密码后，等待连接成功。

连接成功后，输入如下命令开始配置ssh免密登录。

```shell
$ ssh-keygen -t rsa
```

连续按四次回车键即可，之后我们需要将hadoop01主机的公钥复制到需要关联的服务器上（包括主机），执行``ssh-copy-id hadoop02``即可将公钥复制到hadoop02的主机上，其他主机只需要改主机名即可。配置完成后，输入``ssh hadoop02``验证是否可以远程连接。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg09.PNG)

### 第七步 JDK安装

由于hadoop是由java语言开发的，所以hadoop集群的使用要依赖java环境，因此我们还需对虚拟机的JDK进行安装。我们在安装CentOS时，系统会给我们安装好JDK的1.7下的版本，我们这里不使用该版本的JDK，所以在进行安装之前还需要删除本机自带的JDK，并安装好lrzsz，该软件是上传文件的可视化界面，便于我们操作。

删除本机自带的JDK命令：``yum remove java* -y``

安装lrzsz：``yum install lrzsz -y``

将下载好的8u161版本的JDK安装包使用rz命令，上传至Linux系统。

使用如下命令解压缩安装包

```shell
$ tar -zxvf jdk-8u161-linux-x64.tar.gz -C /export/software
```

解压完成后使用``vi /etc/profile``指令配置JDK的环境变量，将下面的内容添加到文件底部。

```ini
export JAVA_HOME=/export/software/jdk
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

完成后保存退出即可，之后执行``source /etc/profile``指令使配置文件生效。

**注意**：上面的“JAVA_HOME=”后跟jdk的安装路径，这里我将jdk的原有名称使用了mv命令重命名为jdk，可根据个人配置，填写配置文件。

上述配置完成后，输入``java -version``验证Java环境是否安装成功。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg10.PNG)

至此，我们的hadoop基础运行环境配置结束。

**注意**：这里的配置需要在每一台主机上进行配置。

## 相关资料

CentOS6.10：[https://pan.baidu.com/s/14TcpFdpphm92THQ-Vks29g](https://pan.baidu.com/s/14TcpFdpphm92THQ-Vks29g)  提取码：966u

jdk8u-161：[https://pan.baidu.com/s/1Zu7evMq7HKkAobHRI8AAeA](https://pan.baidu.com/s/1Zu7evMq7HKkAobHRI8AAeA)   提取码：euyc 


