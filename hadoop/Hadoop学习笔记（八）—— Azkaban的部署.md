## Hadoop学习笔记（八）—— Azkaban的部署

### 前期准备

Azkaban是Apache旗下的一款工作流管理器，由于它的使用和配置相对简单，且易于二次开发，所以受到了很多公司和研究者的青睐，同时它还是hadoop生态圈的一份子，这里所用到的Azkaban版本是3.50.0，也就是3.x系列的版本。在3.x系列中，Azkaban有三种部署模式，solo server mode（独立服务器模式）、two server mode（双服务器模式）、distributed multiple-executor mode（分布式多执行器模式）。这里使用双服务器模式部署Azkaban。

**注意**：

1、使用3.x版本时，JDK的版本需要1.8及以上的版本，否则可能运行失败。

2、使用双服务器模式部署，还需在虚拟机上安装MySql。

### Azkaban的部署

#### 第一步 上传下载好的文件

通过网站下载需要的Azkaban版本后，使用`rz`命令将文件上传至虚拟机。

#### 第二步 Azkaban源文件编译

将上传后的文件使用如下命令进行解压

```shell
$ tar -zxvf azkaban-3.50.0.tar.gz
```

**注**：若想解压到目标文件夹中，只需在上述命令后跟-C 加指定文件夹路径即可。

解压完成后，使用如下命令安装Git

```shell
$ yum -y install git
```

安装完成后，进入Azkaban的安装目录下，使用如下命令对源文件进行编译

```shell
$ ./gradlew build -x test
```

等待片刻后出现BUILD SUCCESSFUL信息后，说明Azkaban源文件编译成功。

**注意**：在编译时需要全程联网，Azkaban源文件会通过git工具下载相关文件，期间会出现由于网络原因或其他原因的编译失败，这时多次使用上述命令重新编译即可，重复多次后等待返回的成功提示。

#### 第三步 MySQL安装配置

这里除了第一种部署模式，其他的部署模式均要安装配置MySQL，在之前的实验中已经安装好了mysql服务，这里直接进行创建Azkaban数据库及用户。

在安装了Azkaban的主机上使用如下命令创建Azkaban数据库及用户。

```shell
$ mysql -u root -p
mysql> create database azkaban;
```

创建完成后，需要对/etc/my.cnf文件进行配置，设置MySql的限制接收数据包大小的值，这里利用CRT克隆一个命令行界面，在该界面对/etc/my.cnf文件进行配置，具体添加内容如下。（下面的内容添加在文件中的[mysqld]下）

```cnf
max_allowed_packet=1024M
```

添加完成后保存退出，使用``sudo /sbin/service mysqld restart``重启mysql服务。

重启完成后，使用前面编译成功的db脚本初始化azkaban数据库表。首先先对`azkaban-db-0.1.0-SNAPSHOT.tar.gz`文件进行解压（该文件在`azkaban-3.50.0/azkaban-db/build/distributions/`下）,解压完成后进入解压后的文件夹中，在该文件夹中有一个`create-all-sql-0.1.0-SNAPSHOT.sql`的脚本执行文件，通过该脚本可对所有的sql脚本文件进行azkaban数据库初始化。

之后，进入之前的mysql命令行界面，使用如下命令导入`create-all-sql-0.1.0-SNAPSHOT.sql`。

```mysql
mysql> source /export/servers/azkaban-3.50.0/azkaban-db/build/distributions/azkaban-db-0.1.0-SNAPSHOT/create-all-sql-0.1.0-SNAPSHOT.sql
```

**注意**：

1、这里的source后跟的是该文件的绝对路径

2、在使用上述命令前，务必要先进入到azkaban数据库中，使用`use azkaban;`即可

#### 第四步 Azkaban Web服务安装配置

首先，创建SSL密钥库，在配置了上述步骤的主机node-01上的某个目录下，使用如下指令生成SSL密钥库。

```shell
$ keytool -keystore keystore -alias jetty -genkey -keyalg RSA
```

之后会提示输入密钥库口令，这里输入自己想要设置的密码，按照提示的内容依次填写或忽略，直至提示is correct（是否正确），输入Y后会提示设置jetty的口令，设置完成后即可。

设置完成后，对Azkaban web 服务器进行安装配置，首先将`azkaban-web-server-0.1.0-SNAPSHOT.tar.gz`解压到指定的文件夹下（该文件在`azkaban-3.50.0/azkaban-web-server/build/distributions/`下），使用的命令如下

```shell
//创建一个存放web服务解压后的文件的存放目录
$ mkdir -p /export/servers/azkaban
//解压缩文件
$ tar -zxvf azkaban-web-server-0.1.0-SNAPSHOT.tar.gz -C /export/servers/azkaban
```

完成后进入解压后的目录下查看，发现该目录下只有bin、lib和web三个文件，还缺少conf、extlib和plugins三个文件，其中conf和plugins文件可以在azkaban-solo-server文件夹下找到.tar.gz的压缩包解压后复制即可，而extlib是一个空文件夹，自己创建即可。（使用的命令如下）

```shell
$ cp -R conf/ /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/
$ cp -R plugins/ /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/
$ mkdir -p /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/extlib
```

##### 配置azkaban.properties文件

复制创建完成后，打开conf下的azkaban.properties配置文件，对其中的时区、mysql、jetty进行修改。（修改后的文件如下）

```properties
# 设置时区为亚洲/上海
default.timezone.id=Asia/Shanghai
# Azkaban UserManager class
user.manager.class=azkaban.user.XmlUserManager
user.manager.xml.file=conf/azkaban-users.xml
# Loader for projects
executor.global.properties=conf/global.properties
azkaban.project.dir=projects
# 对数据库类型和配置进行修改编辑
database.type=mysql
mysql.port=3306
mysql.host=localhost
mysql.database=azkaban
mysql.user=root
mysql.password=数据库密码
mysql.numconnections=100
# Velocity dev mode
velocity.dev.mode=false
# Azkaban Jetty server properties.
# 对jerry服务配置进行修改编辑
jetty.use.ssl=true
jetty.maxThreads=25
jetty.port=8081
jetty.ssl.port=8443
jetty.keystore=keystore
jetty.password=ssl密码
jetty.keypassword=jetty密码
jetty.truststore=keystore
jetty.truestpassword=keystore密码
# Azkaban Executor settings
executor.port=12321
# ...
```

其他设置不变，只对中文注释下的内容进行更改即可。修改完成后保存退出即可，之后需要将之前生成的keystore目录使用`cp keystore /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/`移动到AzkabanWeb服务根目录下。（根据个人配置修改命令参数即可）

##### 配置azkaban-users.xml文件

在该文件下为Azkaban web服务器添加或修改管理用户。使用vi编辑器打开文件，在文件中添加如下内容即可。

```xml
<azkaban-users>
  <user groups="azkaban" password="azkaban" roles="admin" username="azkaban"/>
  <user password="metrics" roles="metrics" username="metrics"/>
  <user password="admin" roles="metrics,admin" username="admin"/>

  <role name="admin" permissions="ADMIN"/>
  <role name="metrics" permissions="METRICS"/>
</azkaban-users>
```

其中第四行为新添加的内容，添加完成后保存退出即可。

##### 配置log4j.properties文件

在conf文件夹下并没有该文件，这里自己创建一个文件，并在该文件下写入如下代码

```properties
log4j.rootLogger=INFO,Console
log4j.logger.azkaban=INFO,server
log4j.appender.server=org.apache.log4j.RollingFileAppender
log4j.appender.server.layout=org.apache.log4j.PatternLayout
log4j.appender.server.File=logs/azkaban-server.log
log4j.appender.server.layout.ConversionPattern=%d{yyyy/MM/dd HH:mm:ss.SSS Z} %p [%c{1}] [Azkaban] %m%n
log4j.appender.server.MaxFileSize=102400MB
log4j.appender.server.MaxBackupIndex=2
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%d{yyyy/MM/dd HH:mm:ss.SSS Z} %p [%c{1}] [Azkaban] %m%n
```

完成后保存退出即可。

### 第五步 Azkaban Executor服务安装配置

首先将`azkaban-exec-server-0.1.0-SNAPSHOT.tar.gz`解压到指定的文件夹下（该文件在`azkaban-3.50.0/azkaban-exec-server/build/distributions/`下），使用的命令如下

```shell
$ tar -zxvf azkaban-exec-server-0.1.0-SNAPSHOT.tar.gz -C /export/servers/azkaban
```

完成后进入解压后的目录下查看，发现该目录下只有bin和lib两个文件，还缺少conf、extlib和plugins三个文件，其中conf和plugins文件可以直接复制之前配置好的文件即可，而extlib是一个空文件夹，自己创建即可。（使用的命令如下）

```shell
$ cp -R /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/conf/ /export/servers/azkaban/azkaban-exec-server-0.1.0-SNAPSHOT/
$ cp -R /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/plugins/ /export/servers/azkaban/azkaban-exec-server-0.1.0-SNAPSHOT/
$ cp -R /export/servers/azkaban/azkaban-web-server-0.1.0-SNAPSHOT/extlib/ /export/servers/azkaban/azkaban-exec-server-0.1.0-SNAPSHOT/
```

##### 配置azkaban.properties文件

在之前配置的基础上，删去jerry服务的配置，并在其下添加修改如下内容即可。

```properties
# Azkaban Jetty server properties.
# Azkaban Executor settings
# 设置最大线程数
executor.maxThreads=50
# 设置Executor端口
executor.port=12321
# 设置流动线程数
executor.flow.threads=30
```

配置完成后保存退出即可。

### 效果验证

**启动Azkaban Executor服务**

在Azkaban Executor服务所在的解压包目录下，执行指令`bin/start-exec.sh`启动Azkaban Executor服务，此时使用`ll`命令查看，发现文件夹中多了两个关于日志信息的文件，其中logs文件夹中的文件保存了此次启动服务的日志信息，使用vi编辑器查看该文件，日志显示启动成功。（如果不成功，则根据日志文件提示的内容修改即可）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg25.PNG)

**启动Azkaban Web服务**

在Azkaban Web服务所在的解压包目录下，执行指令`bin/start-web.sh`启动Azkaban Web服务（必须先启动成功Azkaban Executor服务，才能启动Azkaban Web服务），此时使用`ll`命令查看，发现文件夹中多了两个关于日志信息的文件，其中logs文件夹中的文件保存了此次启动服务的日志信息，使用vi编辑器查看该文件，日志显示启动成功。（如果不成功，则根据日志文件提示的内容修改即可）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg26.PNG)

也可通过jps查看是否启动成功。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg27.PNG)

**查看Azkaban UI界面**

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg28.PNG)

这里的用户名和密码为azkaban-users.xml配置文件中所设置的用户名和密码

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg29.PNG)

至此，Azkaban的部署配置完成。

### Azkaban运行时的坑

利用刚刚搭建好的Azkaban，运行一个job任务时，发现我的任务就只有个输出（echo）但运行起来非常耗时，查看了下日志文件，发现报错

**错误提示**：Cannot request memory (Xms 0 kb, Xmx 0 kb) from system for job wordcount_mr

**原因**：azkaban默认情况下在开始运行job时会检测系统的内存，其最低要求的内存是3G，若系统内存不足3G，便会出现运行的job一直卡在那不动。（在之前搭建虚拟机的时候设置过系统内存为1G，明显不足要求）

**解决方法**：

**第一种**：增加系统内存，使其达到azkaban的要求。

**第二种**：关闭azkaban检测内存的选项。

**第二种的具体实现**：在azkaban/azkaban-exec-server/plugins/jobtypes/目录下的commonprivate.properties文件和azkaban-web-server/plugins/jobtypes/目录下的commonprivate.properties文件中添加以下内容

```properties
memCheck.enabled=false
```

添加完成后，重启服务即可运行成功。