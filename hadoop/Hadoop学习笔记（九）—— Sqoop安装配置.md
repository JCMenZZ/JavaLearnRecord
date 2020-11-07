## Hadoop学习笔记（九）—— Sqoop安装配置

### 前言

Sqoop是一个数据迁移工具，通过使用Sqoop可以将数据从关系数据库导入到Hadoop文件系统中，反之亦可。Sqoop主要分为Sqoop1和Sqoop2两个版本，其中Sqoop1部署方便且结构简单，适合简单的数据迁移工作。这里只是用于学习需要，所以只展示部署Sqoop1的步骤。

### 第一步 安装Sqoop

这里还是使用`rz`命令上传下载好的`sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz`文件至虚拟机的存放压缩包的目录下。

上传完成后，使用`tar -zxvf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz -C /export/servers/`解压缩文件至存放安装包的目录下。

解压完成后即可。

### 第二步 修改必要的配置文件

关于sqoop的配置文件在"sqoop/conf/"下

#### 修改sqoop-env.sh配置文件

在conf目录下并没有sqoop-env.sh文件，只有它的模板文件，这里需要使用`cp`命令拷贝并重命名该模板文件。完成后使用vi编辑器打开该文件，并对该文件的部分内容做以下修改。

```sh
export HADOOP_COMMON_HOME=/export/servers/hadoop-2.7.4
export HADOOP_MAPRED_HOME=/export/servers/hadoop-2.7.4
export HIVE_HOME=/export/servers/apache-hive-1.2.1-bin
```

修改完成后，保存退出即可。这里的配置只针对hadoop2.7.4及sqoop1.4.6版本，其他版本的配置略有不同。

#### 配置sqoop系统环境变量

使用vi编辑器打开/etc/profile文件，在文件内容末尾添加如下配置即可。

```sh
export SQOOP_HOME=/export/servers/sqoop
export PATH=$PATH:$SQOOP_HOME/bin
```

完成后保存退出，并使用`source /etc/profile`刷新文件配置即可。

这里还需要对以后使用到的关系型数据库的驱动包上传至sqoop/lib文件中，上传完成后即可使用sqoop。（例如：使用mysql数据库就将mysql-connector-java-5.1.32.jar上传至lib下）

至此关于sqoop的配置结束。

### 效果展示

在使用`/esqoop list-databases -connect jdbc:mysql://localhost:3306/ --username root --password 个人的mysql密码`开启sqoop前，需要使用`/etc/init.d/mysqld start`指令开启MySQL服务，否则会拒绝连接。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg30.PNG)



