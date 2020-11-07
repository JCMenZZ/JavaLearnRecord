## Hadoop学习笔记（七）—— Flume的安装配置

### 前期准备

+ JDK版本：1.8以上

+ JDK版本与Flume版本要对应

  这里采用JDK版本为1.8.0，Flume的版本1.8.0

### Flume安装步骤

#### 第一步 将下载好的Flume版本上传至Linux上

下载地址：[http://flume.apache.org/download.html](http://flume.apache.org/download.html)

下载完成后，使用`rz`命令上传文件，上传完成后解压到指定文件夹即可。

#### 第二步 flume-env.sh环境变量配置

flume的配置文件在flume安装包下的conf目录下，在conf目录中只有flume-env.sh的模板文件，这里还需要使用`cp`命令将flume-env.sh的模板文件拷贝为flume-env.sh，拷贝完成后使用vi编辑器进入文件后，找到JAVA_HOME在其后写入如下配置。

```sh
export JAVA_HOME=/export/servers/jdk
```

这里写入的是jdk的安装路径，修改完成后保存退出即可。

#### 第三步 在profile文件中添加flume的系统环境变量

使用命令`vi /etc/profile`打开profile文件，在文件最后添加如下内容。

```sh
export FLUME_HOME=/export/servers/flume
export PATH=$PATH:$FLUME_HOME/bin:
```

配置完成后，保存退出并使用`source /etc/profile`刷新文件即可。

至此flume的安装完成。

### 实验验证

这里写好了一份flume的采集方案，通过如下命令启动flume

```shell
$ flume-ng agent --conf conf/ --conf-file conf/netcat-logger.conf \ --name a1 
-Dflume.root.logger=INFO,console
```

**注意**：这里我是在flume文件夹下启动的，所以这里的--conf后跟conf/配置文件路径，若要在其他文件夹下使用，需要将其改为绝对路径。--conf-file后跟flume采集方案的绝对路径。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg23.PNG)

如图可以看出，flume已经开始监听端口。

之后需要在虚拟机上使用`yum -y install telnet`安装telnet工具，使用指令`telnet localhost 44444`模拟进行数据采集。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/hadoopimg24.PNG)

如图可以看出flume服务器端监听到了输入的数据，并接收到服务器端显示。

至此，flume运行成功。