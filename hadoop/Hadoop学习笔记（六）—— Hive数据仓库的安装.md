## Hadoop学习笔记（六）—— Hive数据仓库的安装

### 前言

在安装Hive之前，我们需要了解Hive的安装模式，Hive的安装模式有三种，分别是嵌入模式、本地模式和远程模式，三种模式的安装方式不同，且各有优劣，这里我们采用本地模式安装。

如果在Linux系统上无法在线下载apache旗下的产品，可采用windows下下载jar包，上传至Linux系统上，下载网址如下：

> [http://archive.apache.org/dist/](http://archive.apache.org/dist/)

### 本地模式下安装Hive

#### 第一步 安装并启动MySql服务

这里采用在线安装方式，下载安装mysql服务，使用的命令如下：

```shell
$ yum install mysql mysql-server mysql-devel
```

安装完成后，使用如下命令启动mysql服务

```shell
$ /etc/init.d/mysqld start
```

#### 第二步 连接MySQL并登录MySQL服务

启动MySQL服务成功后，在命令行界面输入``mysql``，登录mysql。（效果如下）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg21.PNG)

进入mysql客户端后，对登录mysql的用户名及密码进行修改，并设置允许远程登录权限。（使用的命令如下）

```mysql
//修改登录mysql的用户名及密码
//首先进入到存放用户名及密码的数据库中
mysql> USE mysql;
//修改用户名及密码
mysql> update user set Password=PASSWORD('111') where user='root';
//设置允许远程登录
mysql> grant all privileges on *.* to 'root'@'% ' identified by '111' with grant option;
//刷新权限
mysql> flush privileges;
```

#### 第三步 将Hive的jar包上传至Linux下并解压

这里的上传命令依然使用``rz``命令上传，上传完成后使用如下命令解压至目标文件夹。

```shell
$ tar -zxvf apache-hive-1.2.1-bin.tar.gz -C /export/servers/
```

#### 第四步 Hive配置

+ 修改**hive-env.sh**配置文件

  该配置文件在hive下的conf文件夹中，但是该文件夹只有其的模板文件，所以这里使用``cp``命令，拷贝一份并重命名为hive-env.sh，拷贝完成后使用vi编辑器打开文件，在文件中添加如下内容。

  ```sh
  # 设置Hadoop环境变量
  export HADOOP_HOME=/export/servers/hadoop-2.7.4
  ```

  完成后保存退出即可。

+ 添加**hive-site.xml**配置文件

  该配置文件在hive中并没有提供，这里我们需要使用vi编辑器创建hive-site.xml文件，创建完成后，在该文件中添加如下内容。

  ```xml
  <configuration>
          <property>
                  <name>javax.jdo.option.ConnectionURL</name>
                  <value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true</value>
                  <description>Mysql连接协议</description>
          </property>
          <property>
                  <name>javax.jdo.option.ConnectionDriverName</name>
                  <value>com.mysql.jdbc.Driver</value>
                  <description>JDBC连接驱动</description>
          </property>
          <property>
                  <name>javax.jdo.option.ConnectionUserName</name>
                  <value>root</value>
                  <description>用户名</description>
          </property>
          <property>
                  <name>javax.jdo.option.ConnectionPassword</name>
                  <value>填入自己的mysql密码</value>
                  <description>密码</description>
          </property>
  </configuration>
  ```

  完成后保存退出即可。

#### 第五步 上传Mysql的连接驱动jar包

使用``rz``命令将Mysql的连接驱动jar上传至Hive安装包的lib文件夹下，这里使用的版本是mysql-connector-java-5.1.32.jar。（效果如下）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg22.PNG)

至此，hive数据仓库的本地模式安装结束。

### Hive的启动

#### 使用CLI方式交互Hive

在Hive的安装目录下，输入``/bin/hive``即可启动hive，退出时在hive命令行界面输入quit或exit即可退出。

#### 通过远程服务交互Hive

将Hive程序的安装目录分发到指定服务器上后，在第一台服务器的hive的安装目录下输入``bin/hiveserver2``这时该服务器会成为hive的服务器，利用SecureCRT软件克隆该服务器后，选择操作hive的目标服务器（该服务器最好含有namenode进程），在该服务器上输入如下命令即可进行hive操作

```shell
//输入远程连接命令
$ bin/beeline
//输入远程连接协议，连接到指定的hive服务器
beeline>!connect jdbc:hive2://node-01:10000
//输入连接hive服务器的用户名及密码
Enter username for jdbc:hive2://node-01:10000:root
Enter password for jdbc:hive2://node-01:10000:******
```

完成后即可进入hive命令行界面。

**注意**：1、通过``jps``命令查看进程时，hive的进程是RunJar

​			2、最后如果要退出远程操作，这里尝试过close、exit、quit都不行后，我们使用简单粗暴的方法退出，直接按住键盘上的Ctrl+C键即可强制退出，退出后如果有进程残存，需要kill进程即可。

### 资源

**apache旗下的各种产品的jar包资源下载地址**

> [http://archive.apache.org/dist/](http://archive.apache.org/dist/)

**Mysql的mysql-connector-java各种版本下载地址**

> [https://mvnrepository.com/artifact/mysql/mysql-connector-java](https://mvnrepository.com/artifact/mysql/mysql-connector-java)