## Zookeeper分布式集群部署

### 前言

之前，我们在CentOS上部署了hadoop集群，在后期的使用中，为了减轻构建健壮的分布式系统服务，我们这里还要部署Zookeeper分布式协调服务。

Zookeeper下有Leader、Follower、Observer三种状态，这里我们采用Leader+Follower模式部署Zookeeper集群，省略Observer。

### 第一步 上传Zookeeper的安装包

同样使用rz命令，将安装包上传至指定目录下。

### 第二步 解压安装包

进入Zookeeper的安装包目录下，使用如下命令解压安装包。

```shell
$ tar -zxvf zookeeper-3.4.10.tar.gz -C /export/software
```

-C后跟的解压后文件的存放路径

### 第三步 修改Zookeeper的配置文件

这里需要将zookeeper自带模板配置文件复制为zoo.cfg，命令如下

```shell
$ cp zoo_sample.cfg zoo.cfg
```

**注意**：这里的zoo_sample.cfg在zookeeper下的conf文件夹下。

使用``vi zoo.cfg``进入配置文件，在配置文件中添加如下内容

```shell
# 设置数据文件目录+数据持久化路径
dataDir=/export/data/zookeeper/zookdata

# 配置Zookeeper集群的服务器编号以及对应的主机名、选举端口号和通信端口号（心跳端口号）
server.1=hadoop01:2888:3888
server.2=hadoop02:2888:3888
server.3=hadoop03:2888:3888
```

添加修改完成后，保存退出即可。

### 第四步 创建myid文件

根据配置文件中的dataDir的目录，创建zookdata文件夹。

在zookdata文件夹下创建myid文件，命令如下。

```shell
$ cd /export/data/zookeeper/zookdata
$ echo 1>myid
```

### 第五步 配置环境变量

使用``vi /etc/profile``添加zookeeper的环境变量如下。

```ini
export ZK_HOME=/export/software/zookeeper-3.4.10
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZK_HOME/bin
```

配置完成后保存退出即可

### 第六步 分发zookeeper的相关文件至其他服务器

命令如下

```shell
$ scp -r /export/software/zookeeper-3.4.10/ hadoop02:/export/software/
$ scp -r /export/software/zookeeper-3.4.10/ hadoop03:/export/software/
$ scp -r /export/data/zookeeper/ hadoop02:/export/data/
$ scp -r /export/data/zookeeper/ hadoop03:/export/data/
$ scp /etc/profile hadoop02:/etc/profile
$ scp /etc/profile hadoop03:/etc/profile
```

分发完成后，需要进到其他两台主机的/export/data/zookeeper/zookdata下修改myid分别为2和3。

### 第七步 使环境变量生效

在三台主机上同时输入``source /etc/profile``使改好的配置文件生效。

配置完成后使用如下命令即可操作zookeeper。

启动zookeeper服务

```shell
$ zkServer.sh start
```

查看zookeeper当前节点的角色

```shell
$ zkServer.sh status
```

关闭zookeeper服务

```shell
$ zkServer.sh stop
```

### 效果验证

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg13.PNG)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg14.PNG)

### 相关软件

Zookeeper-3.4.10：[https://pan.baidu.com/s/1esDLmuB4StJ-Rszox08xmg](https://pan.baidu.com/s/1esDLmuB4StJ-Rszox08xmg )    提取码：k061