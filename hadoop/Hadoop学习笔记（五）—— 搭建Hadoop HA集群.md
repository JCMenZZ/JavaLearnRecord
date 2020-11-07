## Hadoop学习笔记（五）—— 搭建Hadoop HA集群

### 三台虚拟机的集群节点规划

| 服务器  | Name Node | Data Node | Resource Manager | Node Manager | Journal Node | Zookeeper | ZKFC |
| :-----: | :-------: | :-------: | :--------------: | :----------: | :----------: | :-------: | :--: |
| node-01 |     +     |     +     |        +         |      +       |      +       |     +     |  +   |
| node-02 |     +     |     +     |                  |      +       |      +       |     +     |  +   |
| node-03 |           |     +     |                  |      +       |      +       |     +     |      |

### 搭建环境准备

之前我们只是搭建了hadoop的高性能集群，该集群并不是这里的高可用集群，在HDFS高可用的架构中，部署了两个NameNode节点并且加入了ZKFC(故障恢复控制器)监控NameNode的健康状态，并通过心跳的方式与Zookeeper保持通信，解决了单点故障的问题。

这里的搭建环境只需要一个普通的Hadoop集群，安装好JDK和Zookeeper即可。

**注意**：这里只需要将Hadoop的安装包上传至虚拟机，并不需要安装。

### 搭建Hadoop HA集群

#### 配置core-site.xml文件

在该文件最末行，删去configuration，并添加如下代码

```xml
<configuration>
        <!--指定HDFS的nameservice为ns1-->
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://ns1</value>
        </property>
        <!--指定Hadoop的临时目录-->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/export/servers/hadoop-2.7.4/tmp</value>
        </property>
        <!--指定zookeeper地址-->
        <property>
                <name>ha.zookeeper.quorum</name>
                <value>node-01:2181,node-02:2181,node-03:2181</value>
        </property>
</configuration>
```

#### 配置hdfs-site.xml文件

在该文件最末行，删去configuration，并添加如下代码

```xml
<configuration>
        <!--设置副本个数-->
        <property>
                <name>dfs.replication</name>
                <value>2</value>
        </property>
        <!--设置namenode.name目录-->
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/export/data/hadoop/name</value>
        </property>
        <!--设置namenode.data目录-->
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/export/data/hadoop/data</value>
        </property>
        <!--开启WebHDFS-->
        <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
        <!--在NN和DN上开启WebHDFS(REST API)功能,可以不启用-->
        </property>
        <!--指定HDFS的nameservice为ns1，必须和core-site.xml一致-->
        <property>
                <name>dfs.nameservices</name>
                <value>ns1</value>
        </property>
        <!--ns1下面有两个namenode，分别是nn1和nn2-->
        <property>
                <name>dfs.ha.namenodes.ns1</name>
                <value>nn1,nn2</value>
        </property>
        <!--nn1的RPC通信地址-->
        <property>
                <name>dfs.namenode.rpc-address.ns1.nn1</name>
                <value>node-01:9000</value>
        </property>
        <!--nn1的http通信地址-->
        <property>
                <name>dfs.namenode.http-address.ns1.nn1</name>
                <value>node-01:50070</value>
        </property>
        <!--nn2的RPC通信地址-->
        <property>
                <name>dfs.namenode.rpc-address.ns1.nn2</name>
                <value>node-02:9000</value>
        </property>
        <!--nn2的http通信地址-->
        <property>
                <name>dfs.namenode.http-address.ns1.nn2</name>
                <value>node-02:50070</value>
        </property>
        <!--指定NameNode的元数据在JournalNode上的存放位置-->
        <property>
                <name>dfs.namenode.shared.edits.dir</name>
                <value>
qjournal://node-01:8485;node-02:8485;node-03:8485/ns1
                </value>
        </property>
        <!--指定JournalNode在本地磁盘存放数据的位置-->
        <property>
                <name>dfs.journalnode.edits.dir</name>
                <value>/export/data/hadoop/journaldata</value>
        </property>
        <!--开启NameNode失败自动切换-->
        <property>
                <name>dfs.ha.automatic-failover.enabled</name>
                <value>true</value>
        </property>
        <!--配置失败自动切换的实现方式-->
        <property>
                <name>dfs.client.failover.proxy.provider.ns1</name>
                <value>
org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider
                </value>
        </property>
        <!--配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行-->
        <property>
                <name>dfs.ha.fencing.methods</name>
                <value>
                        sshfence
                        shell(/bin/true)
                </value>
        </property>
        <!--使用sshfence隔离机制时需要免密登录-->
        <property>
                <name>dfs.ha.fencing.ssh.private-key-files</name>
                <value>/root/.ssh/id_rsa</value>
        </property>
        <!--配置sshfence隔离机制超时时间-->
        <property>
                <name>dfs.ha.fencing.ssh.connect-timeout</name>
                <value>30000</value>
        </property>
</configuration>
```

#### 配置mapred-site.xml文件

这个文件在".../hadoop-2.7.4/etc/hadoop/"目录下并不存在，只存在"mapred-site.xml.template"文件，这个是MapReduce的配置模板文件，这里只需要使用``cp mapred-site.xml.template mapred-site.xml``拷贝一份并重命名为mapred-site.xml即可。

之后使用vi编辑器打开mapred-site.xml，在该文件最末行，删去configuration，并添加如下代码

```xml
<configuration>
        <!--指定MapReduce框架为YARN方式-->
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
```

#### 配置yarn-site.xml文件

在该文件最末行，删去configuration，并添加如下代码

```xml
<configuration>
        <property>
                <name>yarn.nodemanager.resource.memory-mb</name>
                <value>2048</value>
        </property>
        <property>
                <name>yarn.scheduler.maximum-allocation-mb</name>
                <value>2048</value>
        </property>
        <property>
                <name>yarn.nodemanager.resource.cpu-vcores</name>
                <value>1</value>
        </property>
        <property>
        <!--开启ResourceManager高可用-->
                <name>yarn.resourcemanager.ha.enabled</name>
                <value>true</value>
        </property>
        <!--指定ResourceManager的cluster id-->
        <property>
                <name>yarn.resourcemanager.cluster-id</name>
                <value>yrc</value>
        </property>
        <!--指定ResourceManager的名字-->
        <property>
                <name>yarn.resourcemanager.ha.rm-ids</name>
                <value>rm1,rm2</value>
        </property>
        <!--分别指定ResourceManager的地址-->
        <property>
                <name>yarn.resourcemanager.hostname.rm1</name>
                <value>node-01</value>
        </property>
        <property>
                <name>yarn.resourcemanager.hostname.rm2</name>
                <value>node-02</value>
        </property>
        <!--指定Zookeeper集群地址-->
        <property>
                <name>yarn.resourcemanager.zk-address</name>
                <value>node-01:2181,node-02:2181,node-03:2181</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>
```

#### 配置slaves文件

删去该文件的全部内容，在文件中加入三台主机的名称，达到配置集群主机名称的目的。

例如：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg15.PNG)

配置完成后保存退出即可。

#### 配置hadoop-env.sh文件

在该文件中找到"export JAVA_HOME="，找到后删去其后的内容，并添加自己的jdk的安装路径。

例如：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg16.PNG)

配置完成后保存退出即可。

#### 分发目录

将配置好的Hadoop HA集群文件分发给其他主机，命令如下

```shell
$ scp -r hadoop-2.7.4/ node-02:/export/servers/
$ scp -r hadoop-2.7.4/ node-03:/export/servers/
```

分发完成后，配置profile文件，添加hadoop的环境变量，具体如下

```shell
export HADOOP_HOME=/export/servers/hadoop-2.7.4
export PATH=:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

配置完成后，使用如下命令将profile文件分发到其他主机

```shell
$ scp /etc/profile node-02:/etc/profile 
$ scp /etc/profile node-03:/etc/profile 
```

分发完成后，使用source命令使文件生效即可。

#### 初次启动Hadoop HA集群步骤

**第一步**：在三台主机上输入``zkServer.sh start``启动zookeeper服务。

+ **注意**：部分zookeeper安装包生成的启动脚本不同，有的需要进入zookeeper安装路径下的bin目录中，使用命令``./zkServer.sh start``才能启动，具体的启动方式需根据个人配置的实际情况而定。

**第二步**：在三台主机上输入``hadoop-daemon.sh start journalnode``启动JournalNode。

**第三步**：在node-01节点格式化NameNode，并将格式化后的目录分发给node-02中，具体命令如下。

```shell
$ hadoop namenode -format 
$ scp -r /export/data/hadoop node-02:/export/data/
```

**第四步**：在node-01节点输入命令``hdfs zkfc -formatZK``格式化ZKFC。

**第五步**：在node-01节点输入命令`` start-dfs.sh ``启动HDFS。

**第六步**：在node-01节点输入命令``start-yarn.sh ``启动YARN。

### 效果验证

输入jps命令查看进程

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg17.PNG)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg18.PNG)

这里只需要核对前面的集群节点规划，判断自己的Hadoop HA集群是否搭建成功。

### 问题

在通过webUI界面查看时，直接在浏览器中输入node-01:50070，浏览器会提示无法访问，原因是未对本地操作系统中的hosts文件中添加IP映射。(如果不想配置，也可输入节点的IP+端口号访问web界面)

**解决方案**：WIN10下hosts文件路径:C:\Windows\System32\drivers\etc

在hosts文件中添加如下配置

```
192.168.114.140 node-01
192.168.114.141 node-02
192.168.114.142 node-03
```

添加完成后，保存退出即可。（效果如下）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg19.PNG)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg20.PNG)