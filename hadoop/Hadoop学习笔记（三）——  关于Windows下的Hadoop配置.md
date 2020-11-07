##  Hadoop学习笔记（三）——  关于Windows下的Hadoop配置

### 前言

之前在使用Java API操作hadoop时，并未对Windows下的hadoop环境进行配置，直接使用了FileSystem类中自带的方法，跳过了配置hadoop运行环境这一步骤，主要在于方便，其次配置Windows下的hadoop运行环境要将部分文件放在C盘下，个人对于放在C盘下的一些东西有小小的抵触，所以便未进行配置。但是在之后使用MapReduce时，便会出现错误，且该错误出现后在下一次运行的进程中会有上一次的进程残存，会导致下一次的程序无法正常执行。（错误提示如下）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/WHadoopimg01.PNG)

MapReduce的运行模式有两种，一种是本地运行模式，另一种是集群运行模式，且MapReduce的jar包中默认使用的是本地运行模式，如果我们采用集群运行模式虽然很简单，但却失去了解决错误的乐趣，所以这里这里便对Windows下的Hadoop运行环境进行配置。

### 配置Windows下的Hadoop运行环境

**操作步骤**：

+ **第一步**：这里我们将hadoop-2.7.4拷贝到其他目录下。**（注意：该目录的名称必须没有中文和空格）**

+ **第二步**：在Windows上配置hadoop的环境变量，在系统变量目录下创建一个新的环境变量。

  |   变量名    |          变量值          |
  | :---------: | :----------------------: |
  | HADOOP_HOME | D:\SoftWare\hadoop-2.7.4 |

  这里的变量值就是hadoop-2.7.4所在的目录。添加完成后对系统变量下的Path中进行如下添加。

  ```
  %HADOOP_HOME%\bin
  ```

  目的在于指定hadoop-2.7.4的bin目录。

  效果图如下

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/WHadoopimg02.PNG)

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/WHadoopimg03.PNG)

+ **第三步**：将hadoop-2.7.4\bin文件夹下的hadoop.dll文件放到系统盘下的Windows\System32目录下。

  如图

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/WHadoopimg04.PNG)

+ **第四步**：重启Windows系统。

  重启完成后，打开相关的java程序并运行，没有报错。如图

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/WHadoopimg05.PNG)

### 资源链接

这里提供关于hadoop-2.7.4的windows下的相关配置文件，亲测有效。

> [点我下载](https://download.csdn.net/download/weixin_43624031/12358805)

