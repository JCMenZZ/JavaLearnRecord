## 编译器使用——关于IDEA部分插件使用问题

### 前言

工欲善其事必先利其器，在学习Java的过程中，有一个好的工具无疑能增加学习效率，在这段时间的学习中，将使用IDEA的一些问题做以备份，便于在忘记时快速找到解决办法。

### IDEA关于搭建Maven工程时的换源问题？

有时我们在使用Maven构建环境时，一个小小的依赖包就有可能下载很长时间，为此针对这个问题，可以将自己的IDEA中的Maven的下载源换成国内源，具体操作步骤如下。

首先，打开IDEA在左上角的File中找到setting，在setting中找到Maven的配置。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201115222103.png)

这里是IDEA的原始配置，我们需要把它改成我们自己的Maven。在Maven home directory选项中选择IDEA中自带的Maven，如果没有自动显示出其他的Maven就点击旁边的三个小点，自己找到路径并填写，一般这个路径就在IDEA所在的文件夹下，在该选项下的User settings file选项中选中旁边的Override，然后点击旁边的文件夹图标找到Maven的配置文件（settings.xml），该配置文件在IDEA安装路径下的插件文件夹下的Maven文件夹中，根据版本来选择使用哪个配置文件。上图中最下面的选项是本地的Maven仓库如果没有就不用去配置。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201115223017.png)

之后点击Apply点击ok即可完成IDEA中的Maven配置。根据配置文件路径打开配置文件，并找到`mirrors`标签，在该标签下填入如下代码块即可成功换源。

```xml
<mirror>
      <id>aliyunmaven</id>
      <mirrorOf>central</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

之后我们在Maven目录下的bin目录下打开cmd窗口进行验证。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201115223624.png)

通过验证我们可以发现，成功将下载源地址换成了阿里源。但是有时可能会存在一个问题，阿里源下的库中没有我们需要的依赖，所以此时我们需要将源地址再换回来等下载好该依赖后再换成国内源即可。

### IDEA关于translation插件的配置问题？

当我们在IDEA中安装了translation插件，此时会发现无法正常翻译，这时我们需要对IDEA进行配置，具体方法如下：

首先在IDEA中点击File->settings，找到如下图所示的选项，

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106104214.png)

选择Auto-detect proxy settings，在URL中填入：`http://172.0.0.1:1080`，点击Apply后点击OK即可。此时重启IDEA即可正常使用谷歌翻译。有时这个URL会失效，这时我们在settings中将translation插件的默认翻译源换成有道翻译。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106105318.png)

然后，在浏览器中打开[有道智云](https://ai.youdao.com/)，注册一个账号，在控制台界面选择应用管理，点击创建一个应用。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106105835.png)

应用名称随意，接入方式选择API，点击下一步，点击创建应用。之后在自然语言翻译下点击翻译实例，选择创建实例。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106110437.png)

点击下一步，我们的实例就创建完成了。此时显示的界面右边有一个绑定应用，点击绑定应用，选择我们之前创建的应用即可。此时我们点击我的应用，点击绑定了服务的应用，这里我们可以看见我们的应用ID和应用密钥，将他们填入Translation插件中对应的文本框中即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106110939.png)

此时，我们重启IDEA，打开翻译工具，测试一下，成功查询出我们想要的翻译，且查询速度要比谷歌翻译快速。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201106111307.png)

**注意**：如果后期有道智云因为费用问题无法使用了，可以选择使用百度翻译，具体配置方法类似。

### 如何搭建一个属于自己的图床？

最近在整理项目的时候，发现以前所写的一些`.md`文件因为存储路径的改变，导致图片无法正常显示，而且在使用Typora写`.md`文件时，会将一些不是绝对路径的图片保存在C盘下的某个目录中，当我们不小心清理了这些文件，就会导致一部分的图片无法显示，上传到博客网站备份又怕网站关闭导致失去这些文件，正巧看见一个搭建免费图床的办法，在这里作以记录。

#### 第一步 环境准备

+ 安装**Node.js**，注意nodejs的版本必须大于8，否则PicGo会无法正常使用。如果安装过nodejs，可以在`cmd`下键入`node -v`查看自己nodejs的版本号。
+ 安装**PicGo**，这里给出==2.3.0-beta.3==版本的**下载地址**：[https://github.com/Molunerfinn/PicGo/releases/download/v2.3.0-beta.3/PicGo-Setup-2.3.0-beta.3.exe](https://github.com/Molunerfinn/PicGo/releases/download/v2.3.0-beta.3/PicGo-Setup-2.3.0-beta.3.exe)或者在此[https://github.com/Molunerfinn/PicGo/releases](https://github.com/Molunerfinn/PicGo/releases)地址下找自己想要的版本。
+ 注册一个**Gitee**账号，这里可以使用GitHub账号，考虑到GitHub是国外的服务器，所以采用Gitee。

**注意**：这里安装了`PicGo`后还需要给他配置环境变量，否则将无法打开软件。这里的环境变量配置只需要将`%APPDATA%\picgo\data.json`粘贴在系统变量里的Path目录下，之后重启计算机即可正常打开软件。

#### 第二步 Gitee和PicGo的配置

在Gitee中的设置界面中，找到私人令牌，点击私人令牌并生成一个私人令牌。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/imagebed-01.png)

如此我们的环境就准备完毕了。环境准备完毕后，在Gitee上创建一个新的仓库，创建完成后在PicGo中进行配置插件。首先，打开PicGo点击插件设置，搜索Gitee。注意：该仓库一定是公开的，否则无法正常使用链接。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/imagebed-02.png)

安装第二个插件，等待安装完成后，点击插件右下角设置图标，选择配置uploader-gitee。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/imagebed-03.png)

**注意**：这里的**repo**中填入自己Gitee下创建的用户名/仓库的路径。例如：用户名为gitee，路径为image，则repo为gitee/image。**token**中填入上一步在gitee中生成好的私人令牌。点击确定即可配置插件完成。

#### 第三步 测试

上述配置完成后，进入PicGo的设置界面，滑动到最下面，只选择gitee，最后回到上传区，点击图片上传旁边的倒三角选择gitee即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/imagebed-04.png)

完成后，测试上传一张图片试试，将需要上传的图片拖到提示的位置，等待提示上传成功后点击相册，复制图片链接，并将此链接粘贴到markdown文件编辑器中即可。此时，刷新gitee下图床的仓库，即可看见刚刚上传的图片。

