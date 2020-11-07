## 关于此篇

  之前，我们对hadoop高性能集群的运行平台做了部署，接下来针对hadoop集群配置作以部署。关于运行平台的部署和相关软件的资料，可在相关资料一栏下，自行提取使用。

## Linux系统搭建hadoop高性能集群（二）

相关软件版本

Hadoop版本：hadoop-2.7.4

  ### 第一步 Hadoop集群配置
   Hadoop集群部署模式有三种分别为独立模式、伪分布模式和完全分布模式，这里我们采用完全分布模式搭建Hadoop集群。

之前我们将hadoop运行时所需要的java运行环境做了配置，接下来我们需要将hadoop-2.7.4的安装包上传至Linux系统，同样使用``rz``命令上传安装包，上传完成后使用如下命令解压缩安装包。

```shell
$ tar -zxvf hadoop-2.7.4.tar.gz -C /export/software/
```

**注意**：-C后跟所需要的解压路径。

解压完成后，同样使用``vi /etc/profile``对hadoop的环境变量做如下配置。

```ini
export HADOOP_HOME=/export/software/hadoop-2.7.4
export PATH=:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

**注意**：这里的“HADOOP_HOME=”后跟hadoop安装包的解压路径，作用是指定hadoop的文件夹。

完成后保存退出即可，之后执行``source /etc/profile``指令使配置文件生效。

配置完成后，在Linux命令行界面上输入``hadoop version``查看hadoop是否安装成功。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg11.PNG)

这里还需要对其他的主机进行如上配置。

  ### 第二步  配置Hadoop集群的主节点

首先，先进入到Hadoop解压包下的"etc/hadoop/"目录，在此目录下可使用ls命令查看各种配置文件。

#### 修改hadoop-env.sh文件

使用``vi hadoop-env.sh``进入配置文件，找到JAVA_HOME参数位置，进行如下修改。

```sh
export JAVA_HOME=/export/software/jdk
```

这里的JAVA_HOME就是jdk的安装路径。修改完成后保存退出即可。

#### 修改core-site.xml文件

使用``vi core-site.xml``进入配置文件，在最末行找到"configuration"，删去"configuration"，写入如下代码。

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name> 
        <value>hdfs://hadoop01:9000</value>  <!--指定namenode地址在hadoop01上-->
    </property>
    <property>
        <!--指定hadoop的临时目录-->
        <name>hadoop.tmp.dir</name>
        <value>/export/servers/hadoop-2.7.4/tmp</value>
    </property>
</configuration>
```

修改完成后保存退出即可。

#### 修改hdfs-site.xml文件

使用``vi hdfs-site.xml``进入配置文件，在最末行找到"configuration"，删去"configuration"，写入如下代码。

```xml
<configuration>
    <property>
        <!--指定HDFS的副本数量，依个人配置而定-->
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop02:50090</value>
    </property>
</configuration>
```

修改完成后保存退出即可。

#### 修改mapred-site.xml文件

该文件在etc/hadoop/目录中没有该文件，需要先通过cp命令拷贝mapred-site.xml.template。命令如下：

```shell
$ cp mapred-site.xml.template mapred-site.xml
```

拷贝完成后，使用``vi mapred-site.xml``进入配置文件，在最末行找到"configuration"，删去"configuration"，写入如下代码。

```xml
<configuration>
    <!-- 指定MapReduce运行时框架，指定在Yarn上，默认是local -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

修改完成后保存退出即可。

#### 修改yarn-site.xml文件

使用``vi yarn-site.xml``进入配置文件，在最末行找到"configuration"，删去"configuration"，写入如下代码。

```xml
<configuration>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>hadoop01</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>
```

修改完成后保存退出即可。

#### 修改slaves文件

使用``vi slaves``进入配置文件，删去localhost，并添加如下代码。

```
hadoop01
hadoop02
hadoop03
```

### 第三步 将集群主节点的配置文件分发到其他子节点

这里只对hadoop的主节点进行了配置，由于我们配置了ssh服务，所以可以直接分发配置文件来达到配置的目的。具体命令如下：

```shell
$ scp /etc/profile hadoop02:/etc/profile
$ scp /etc/profile hadoop03:/etc/profile
$ scp -r /export/ hadoop02:/
$ scp -r /export/ hadoop03:/
```

分发完成后，对三台主机分别执行``source /etc/profile``使配置文件生效。

至此，hadoop集群搭建完毕。

### 问题

在启动集群时，发现在主节点上缺少NodeManger进程，主要原因是因为系统资源分配不足，解决方法在yarn-site.xml文件中添加如下配置。

```xml
<property>
 	<name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>8</value>
</property>
<property>
 	<name>yarn.nodemanager.resource.memory-mb</name>
    <value>8192</value>
</property>
<property>
 	<name>yarn.scheduler.minimum-allocation-mb</name>
    <value>2048</value>
</property>
```

添加完成后即可成功运行。

UI效果验证如下

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg12.PNG)

## 相关资料

hadoop-2.7.4.tar.gz：[https://pan.baidu.com/s/11y4tfuw-fTbMG6iiEyAiJA ](https://pan.baidu.com/s/11y4tfuw-fTbMG6iiEyAiJA)  提取码：fzmg

