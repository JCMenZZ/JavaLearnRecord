## 项目管理——Maven学习（三）

### Maven私服

Maven私服是Maven仓库中的远程仓库，Maven仓库分为本地仓库和远程仓库，远程仓库又分为Maven中央仓库、其他远程仓库和私服。其中，中央仓库是由maven官方提供的，maven私服需要手动去搭建。通常，maven私服就是局域网内的maven远程仓库，我们通过电脑上安装maven软件并连接maven私服，就可以从私服下载上传过的所有的JAR包。且可以上传自己打好包的项目，以供其他人进行下载。除此之外，私服可以充当代理服务器，当私服上没有JAR包时会从maven中央仓库中自动下载。

#### 使用Nexus搭建Maven私服

Nexus是一个Maven仓库管理器，Nexus除了可以充当Maven私服，还可以提供强大的仓库管理、构建搜索等功能。使用Nexus搭建Maven私服的步骤具体如下：

首先，下载相关Nexus版本[https://help.sonatype.com/repomanager2/download/download-archives---repository-manager-oss](https://help.sonatype.com/repomanager2/download/download-archives---repository-manager-oss)。这里采用的是windows下搭建maven私服，linux下和此类似。

官网上下载速度有些坑，这里提供两个版本的nexus如下：

> 链接：https://pan.baidu.com/s/1QcPPs-NMeX333BiPjohaXA 
> 提取码：essu 

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201231175110.png)

然后，将下载好的压缩包进行解压缩，之后进入nexus目录下的bin目录下，在此目录下打开cmd命令窗口，执行nexus.bat install命令安装服务。**注意**：必须要以管理员的权限执行命令，否则会安装失败。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201231181636.png)

安装成功后，我们可以就可以启动nexus服务了，windows下启动nexus服务的方式有两种，可以通过Windows系统服务启动nexus，还可以通过命令行启动nexus服务。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201231185459.png)

启动后打开浏览器输入`http://localhost:8081/nexus`，即可访问我们的nexus，此时点击网页右上角的Log in按钮进行登录，默认的账号和登录密码为admin与admin123，登录成功后点击左侧菜单Repositories可以看到nexus内置的仓库列表。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201231194929.png)

此时我们maven私服的搭建就完成了。

**注意**：如果端口冲突，可以将正在运行的8081端口的程序关闭，或者在Nexus的配置文件中修改端口号。

#### Nexus的仓库类型

从Nexus的仓库列表可知，nexus默认内置了很多仓库，这些仓库有四种类型，每种类型的仓库用于存放特定的JAR包，如下：

| 仓库类型 |                             说明                             |
| :------: | :----------------------------------------------------------: |
|  hosted  | 宿主仓库，部署自己的JAR包到这个类型的仓库，包括Releases和Snapshots两部分，Releases为内部发布版本仓库、 Snapshots为内部测试版本仓库。 |
|  proxy   | 代理仓库，用于代理远程的公共仓库，如maven中央仓库。用户连接私服，私服会自动去中央仓库下载JAR包或者插件。 |
|  group   | 仓库组，用来合并多个hosted与proxy仓库，通常配置自己的maven来连接仓库组。 |
| virtual  |          虚拟仓库，兼容Maven1版本的JAR包或者插件。           |

hosted类型的仓库中Releases其实表示的是存放经过测试比较稳定的jar包的仓库，而Snapshots为存放正在测试或还未测试，尚不稳定的jar包的仓库。也就是说我们上传JAR包到仓库的时候，如果JAR包经过测试且不存在BUG的问题，我们就可以发布在Releases仓库中，如果会存在BUG问题，我们就发布在Snapshots仓库中。

**注意**：我们在nexus页面上看到的仓库实际对应的目录是在我们磁盘中解压缩nexus的目录下的`sonatype-work\nexus\storage`路径下存放的，我们实际上传到仓库的jar包在对应目录下也会有存档。

#### 将JAR包上传至私服

上传JAR包到私服的操作步骤如下：首先，配置maven的settings.xml文件。然后，配置项目的pom.xml文件。最后，执行`mvn deploy`命令，将JAR包上传至私服。

配置maven的settings.xml文件，具体如下：

将如下代码粘贴到settings.xml文件中的services标签下，代码如下：

```xml
<server>
    <id>releases</id>
    <username>admin</username>
    <password>admin123</password>
</server>
<server>
    <id>snapshots</id>
    <username>admin</username>
    <password>admin123</password>
</server>
```

配置需要上传的项目的pom.xml文件，具体如下：

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <url>http://localhost:8081/nexus/content/repositories/releases/</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

将上述的代码粘贴到pom.xml文件中，完成后执行命令mvn deploy，此时观察私服可以发现我们的项目已经被打好包并上传成功了。

**注意**：settings.xml的server标签中的id必须和私服的仓库名称对应，用户名和密码必须要填写正确。在pom.xml文件中的repository标签下的id，必须和settings.xml文件中配置的进行对应，url必须对应私服下该id对应的仓库的url。在pom文件中，指定的版本号会决定具体上传的位置，例如这里的版本号为`1.0-SNAPSHOT`，最终将会上传至snapshots仓库下，若为RELEASE，则会上传至releases仓库下。

#### 从私服下载JAR包

从私服下载JAR包的具体步骤如下：首先，在maven的settings.xml文件中配置下载模板。然后，在settings文件中配置私服镜像。最后，在该文件中配置激活下载模板。

settings.xml文件中配置下载模板，具体如下：

将下面的代码粘贴到settings文件中的profiles标签中即可，代码如下：

```xml
<profile>  
    <id>dev</id>  
    <repositories>  
        <repository> 
            <id>nexus</id>  
            <!--仓库组的地址-->
            <url>http://localhost:8081/nexus/content/groups/public/</url>  
            <!--是否下载releases构件-->
            <releases>  
                <enabled>true</enabled>  
            </releases>  
            <!--是否下载snapshots构件-->
            <snapshots>  
                <enabled>true</enabled>  
            </snapshots>  
        </repository>  
    </repositories> 
    <pluginRepositories> 
        <pluginRepository> 
            <id>public</id> 
            <name>Public Repositories</name> 
            <url>http://localhost:8081/nexus/content/groups/public/</url> 
        </pluginRepository> 
    </pluginRepositories>
</profile> 
```

**注意**：这里的id并不是固定的，但是这里配置的id的名称必须在下载时进行对应，否则会下载失败。下面的url配置的是nexus私服下的仓库组的url。

配置私服下载镜像，具体如下：

```xml
<mirror>
    <!--构建均从私有仓库中下载,*代表所有-->
    <id>nexus</id>
    <mirrorOf>*</mirrorOf>
    <url>http://localhost:8081/nexus/content/groups/public/</url>
</mirror>
```

配置激活下载模板如下：

将下面的代码粘贴到settings文件的settings下即可，代码如下：

```xml
<activeProfiles>
    <activeProfile>dev</activeProfile>
</activeProfiles>
```

此时我们在pom.xml文件中引入依赖，它会先从本地仓库中寻找该依赖，若找不到则会去私服找对应的依赖，这时私服会从中央仓库下载对应的依赖。等待下载完成后，我们可以发现，私服下和本地仓库下都存在了该依赖。

#### 将第三方JAR包安装到本地仓库和Maven私服

有时依赖Maven并不能将我们想要的JAR包都下载下来，这时如果强行使用Maven去导入坐标，编译器或命令行界面会报错。因为有的JAR包并不在Maven的中央仓库中，所以我们必须去提供该JAR包的网站手动去下载，下载完成后将该JAR包安装在本地仓库或maven私服中，这时就可以正常使用该JAR包下的API了。例如：Oracle的驱动，我们在pom.xml文件中引入该驱动包的坐标，但是却无法下载成功，所以这时我们就无法正常使用该驱动包了。

将这种第三方的JAR安装到**本地仓库**的做法如下：

首先下载好第三方的JAR包，然后将其放在任意目录下，之后使用管理员权限打开cmd窗口，在cmd窗口下进入第三方JAR包所在的路径下，然后执行命令`mvn install:install-file -Dfile=ojdbc14-10.2.0.4.0.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar`等待安装成功。**注意**：这里的`mvn install:install-file`是固定的命令格式，`-Dfile=`后跟第三方JAR包的全名，`-DgroupId=`后跟JAR所在的组织名也就是JAR包的目录结构，`-DartifactId=`后跟该JAR包的唯一标识，`-Dversion`后跟该JAR包的版本号，`-Dpackaging=`后跟该JAR包的打包方式。此时我们就可以在pom文件中导入相关的依赖坐标了。

除此之外，我们还可以将第三方的JAR包安装在私服中，具体做法如下：

首先下载好第三方的JAR包，然后在maven的settings文件中配置第三方仓库的server信息，最后执行`mvn deploy:deploy-file -Dfile=ojdbc14-10.2.0.4.0.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty`命令进行安装。

settings文件配置如下：

```xml
<server>
    <id>thirdparty</id>
    <username>admin</username>
    <password>admin123</password>
</server>
```

**注意**：上述的代码段应放在servers标签内部，标签id的值与命令中`-DrepositoryId=`的值要对应。命令中的Durl为私服中第三方仓库的url，其余的参数值与安装到本地仓库的命令一样。

此时等待命令执行成功，之后在私服页面上刷新第三方仓库，这时就可以看见我们上传的第三方JAR包了。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20210102002241.png)