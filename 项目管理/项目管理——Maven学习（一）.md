## 项目管理——Maven学习（一）

### Maven介绍

Maven是一个项目管理工具，主要作用是在项目的开发阶段对Java项目进行依赖管理和项目构建。依赖管理就是对JAR包的管理，通过导入Maven坐标，来实现不用手动将仓库的JAR包导入的当前项目。项目构建是通过Maven的命令完成项目从清理、编译、测试、报告、打包、部署六个过程。

### Maven的仓库类型

Maven主要有两种仓库类型，①本地仓库；②远程仓库。在远程仓库中又有Maven中央仓库，Maven私服（公司局域网内的仓库，需要搭建），其他远程公共仓库（如apache提供的远程仓库和阿里提供的远程仓库等）

### Maven常用命令

|  命令   |                  说明                  |
| :-----: | :------------------------------------: |
|  clean  |                  清理                  |
| compile |                  编译                  |
|  test   |    测试（自动帮助我们进行单元测试）    |
| package |                  打包                  |
| install | 安装（将当前项目打包后安装到本地仓库） |

### Maven的依赖范围

在`pom.xml`文件中使用标签`scope`来完成依赖范围的配置，具体的依赖范围如下：

| 依赖范围 | 对于编译classpath有效 | 对于测试classpath有效 | 对于运行时classpath有效 |            例子             |
| :------: | :-------------------: | :-------------------: | :---------------------: | :-------------------------: |
| compile  |          是           |          是           |           是            |         spring-core         |
|   test   |          否           |          是           |           否            |            Junit            |
| provided |          是           |          是           |           否            |         Servlet-api         |
| runtime  |          否           |          是           |           是            |          JDBC驱动           |
|  system  |          是           |          是           |           否            | 本地的、Maven仓库之外的类库 |

### Maven的依赖传递

Maven中依赖是可以传递的，在多个项目中存在依赖关系时，我们可以推出项目的直接依赖和间接依赖。当我们在pom.xml中导入某个依赖坐标时，我们可以看出该JAR包下还存在着其他的JAR包，所以所谓的依赖传递其实就是JAR包的传递。这种依赖传递可以帮助我们减少工作量，但是它还存在着一些问题，如依赖冲突。

#### 依赖冲突

由于存在依赖传递这种现象的存在，所以当我们导入某些依赖时，由于这种依赖传递存在，可能会给我们导入不同版本的相同JAR包，但我们只需要某一个版本的JAR包，此时就造成了依赖冲突。由于存在依赖冲突，这时就导致我们需要的某个版本和实际使用的版本不一致，最后就会离我们的想法背道而驰了。当然，它也有解决的方案，如使用maven提供的依赖调解原则，该原则有存在第一声明者优先原则、路径近者优先原则。排除依赖，锁定版本。

#### 解决依赖冲突

##### 1.依赖调解原则——第一声明者优先原则

该解决方案就是在pom.xml文件中定义依赖，以先声明的依赖为准。也就是根据坐标导入的顺序来确定最终使用哪个传递过来的依赖。但是这种方式解决依赖冲突非常不好，如果导入的依赖坐标很多，此时就会出现刚解决的依赖冲突问题，又出现一个新的依赖冲突。

##### 2.依赖调解原则——路径近者优先原则

该解决方案是在pom.xml文件中定义依赖，以路径近者为准。当出现依赖冲突时，我们只需要将产生冲突的jar包的坐标写入pom.xml文件中，此时maven就会根据在pom文件中的该坐标的版本为准。

##### 3.排除依赖

当存在依赖冲突时，我们可以使用标签`exclusions`标签将传递过来的依赖排除出去，以此来解决依赖冲突问题，做法如下：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.7.RELEASE</version>
    <!--指定不需要传递的依赖-->
    <exclusions>
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

##### 4.版本锁定

该解决方案是目前使用最多的一种，它是直接采用锁定版本的方法确定依赖JAR包的版本，版本锁定后则不考虑依赖的声明顺序或依赖的路径，以锁定的版本为准添加到工程中。首先，在pom.xml文件中的`dependencyManagement`标签中锁定依赖的版本。然后在`dependencies`标签中声明需要导入的maven坐标。示例如下：

```xml
<!--  版本锁定  -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>
<!--  导入坐标  -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
</dependencies>
```

