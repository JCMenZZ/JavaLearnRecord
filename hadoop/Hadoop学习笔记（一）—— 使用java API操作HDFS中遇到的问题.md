## Hadoop学习笔记（一）——（使用java API操作HDFS中遇到的问题）

### 1、文件树中缺少文件

（1）关于创建**Maven project**后文件树下没有**Maven Dependencies**文件

**主要原因**：pom.xml文件中的开头报错

**错误提示**：Missing artifact jdk.tools:jdk.tools:jar:1.8

如图：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question1.PNG)

**分析**：tools.jar包是JDK自带的，pom.xml中以来的包隐式依赖tools.jar包，而tools.jar并未在库中，

只需要将tools.jar包添加到jdk库中即可。

**解决方案**：在pom文件中添加如下代码即可。

```xml
    <dependency>
            <groupId>jdk.tools</groupId>
            <artifactId>jdk.tools</artifactId>
            <version>1.8</version>
            <scope>system</scope>
            <systemPath>${JAVA_HOME}/lib/tools.jar</systemPath>
    </dependency>
```

解决完成后的效果如下图所示：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question2.PNG)

### 2、pom.xml文件报错

**错误提示**：Missing artifact jdk.tools:jdk.tools:jar:1.8

如图：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question1.PNG)

**分析**：tools.jar包是JDK自带的，pom.xml中以来的包隐式依赖tools.jar包，而tools.jar并未在库中，

只需要将tools.jar包添加到jdk库中即可。

**解决方案**：在pom文件中添加如下代码即可。

```xml
    <dependency>
            <groupId>jdk.tools</groupId>
            <artifactId>jdk.tools</artifactId>
            <version>1.8</version>
            <scope>system</scope>
            <systemPath>${JAVA_HOME}/lib/tools.jar</systemPath>
    </dependency>
```

解决完成后的效果如下图所示：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question2.PNG)

**注意**：1、2两个问题的错误原因是一致的，只要解决了tools.jar包的问题，所有出错的问题都会被解决。

### 3、从HDFS下载文件到本地时报错且所下载的文件内容为空

**错误提示**：java.io.IOException: (null) entry in command string: null chmod 0644 F:\testFile

如图：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question3.PNG)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question4.PNG)

源代码如下图所示：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question5.PNG)

**分析**：因为我们的Hadoop集群搭建在Linux环境下，而开发环境是在Windows环境下，此时如果不设置Hadoop开发环境，就会报以上错误。

**解决方案**：

+ **第一种方案**：安装配置Windows平台下的Hadoop。**（注意：配置完成后必须重启电脑）**
+ **第二种方案**：将Linux上进行Hadoop安装的Hadoop压缩包下载并解压，然后在解压包**bin**目录下额外添						Windows**相关依赖文件（winutils.exe、winutils.pdb、hadoop.dll）**，然后进行Hadoop						环境变量配置。**（注意：配置完成后必须重启电脑）**
+ **第三种方案**：使用**FileSystem类**自带的方法，即便不对Hadoop的环境变量配置也可正常实现代码功能，				       缺点是本地路径下没有附带一个**xxx.crc**的校验文件。

这里推荐使用**第三种方案**，可以避免在配置Windows下的Hadoop环境时出现其他的错误。

**采用第三种方案解决问题**：

这里只需要将上图所示中的这条代码

```java
fs.copyToLocalFile(new Path("/testFile"), new Path("F:/"));
```

改为下面这种方式即可

```java
fs.copyToLocalFile(true, new Path("/testFile"), new Path("F:/"),true);
```

解决完成后的效果如下图所示：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/question6.PNG)

***

## 拓展学习

### 1、关于eclipse中的单元测试@Test的相关概念

**单元测试定义**：

>  **单元测试**是指对软件中的最小可测试单元进行检查和验证。对于单元测试中单元的含义，一般来说，要根据实际情况去判定其具体含义（例如：C语言中单元指一个函数，Java里单元指一个类，图形化的软件中可以指一个窗口或一个菜单等）。单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试。通俗来讲就是只针对一个函数或类等进行单独的运行调试。

**相关知识链接：[单元测试](https://baike.baidu.com/item/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/1917084?fr=aladdin#9)**

这里我们在Java中用到的单元测试方法，采用了java中的JUnit单元测试工具。JUnit 是 Java 社区中知名度最高的单元测试工具。主要用于白盒测试和回归测试。

**注意**：并不是所有的方法都适合用JUnit进行单元测试，JUnit对所测试的方法有一定的要求。在使用JUnit进行测试时，有下面这几条**注意点**。

+ **@Test 不能加在静态方法上**
+ **所测试的方法不能有返回值**
+ **方法不能有参数。**
+ **不选择方法而去执行JUnit , 相当于所有的@Test注解下的方法都会运行一遍。**

### 2、关于偏移量的相关概念

关于偏移量的概念早在学习C语言、汇编语言、计算机组成原理的时候就有接触，但这个概念相对于其他具象的概念而言要相对难以理解，因为偏移量本身便是一个抽象的概念。最近在研究深度学习、大数据的时候，又接触到了偏移量这个概念，这里再将其拿出来作以具象化，深度理解偏移量这个问题。

> 在计算机汇编语言中，把偏移量的定义为：存储单元的实际地址与其所在段的段地址之间的距离称为段内偏移，也称为“有效地址或偏移量”。

**其实通俗来讲，计算机只要知道了首地址和偏移量，就相当于知道了这个数据的存储位置。**

不同的语言中对于偏移量的定义略有不同，只要理解了偏移量最本质的含义，无论何种语言它所代表的意思基本相同。

这里提供三个链接，需要的小伙伴课可自行阅读。

> [关于Java IO流中偏移量](https://blog.csdn.net/qq_28082757/article/details/96437398)
>
> [关于偏移量的理解](https://blog.csdn.net/u011641620/article/details/15963851)
>
> [什么是偏移量](https://baike.baidu.com/item/%E5%81%8F%E7%A7%BB%E9%87%8F/9180391?fr=aladdin)





