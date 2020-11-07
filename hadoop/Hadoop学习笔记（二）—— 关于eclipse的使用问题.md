## Hadoop学习笔记（二）—— 关于eclipse的使用问题

### 前言

近期在学习Hadoop时，总会在编译器的应用方面出错。因为hadoop本身就是用Java语言编写的，掌握使用Java API操作Hadoop是重中之重，所以这里我们要用到关于java的编译器——eclipse。为什么不用IDEA呢？因为手中的这本教材和视频都是使用的eclipse，这里转而使用IDEA来创建Maven工程包括之后会出现的一些问题都需要自行研究解决，为了不耽误看书看视频的进度，所以就勉为其难的使用了eclipse编译器。（个人观点）这里并不是说eclipse不好用，只是相对于IDEA，eclipse就有点美中不足了。接下来对于个人目前遇到的一些问题，作以笔记留存，如果能帮助到大家那最好不过了。

### 问题一  ——  编译器汉化

之前在使用eclipse时，心想反正我还有IDEA，不如把eclipse搞成汉化的用用，这样也能方便个人的一些操作，加快编码速度，结果搞成汉化的后，发现很多的功能都出现了一些问题，比如使用Ctrl+Shift+H查看源码时，有些类需要导入jar/rar文件才能查看，但如果换成汉化的后，这些就无法在页面上进行操作了。第二点，比如我在编码的时候对于部分快捷键不满，使用起来不是很顺手，这里我想改键，但是使用汉化版后，进入keys面板会进不去，且出现的错误还不能查看错误原因。所以我又将eclipse改了回来。总之呢，汉化只能方便个人，对于个人的提高是没有意义的。接下来先说第一个问题，将eclipse换为中文版。

#### 将eclipse换为中文版

- 打开浏览器，输入网址http://www.eclipse.org/babel/downloads.php，进入页面后向下翻，找到如下图所示的片段。

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg01.PNG)

  **这里的语言包随时都有可能更新，到底使用哪个请自行选择。**

- 这里选择最新版本的**Babel Language Pack Update Site for 2019-12**

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg02.PNG)

  语言包链接：https://download.eclipse.org/technology/babel/update-site/R0.17.1/2019-12/

+ 复制成功后打开eclipse，依次选择菜单栏中的 Help -> Install New Software…，在新打开的窗口中点击Add按钮，之后在location文本框中粘贴刚刚复制的网址，点击OK按钮后会弹出正在加载的窗口，并出现Pending……，等待片刻后，选择需要的中文包，这里选择简体中文Chinese Simplified，一路next后，勾选同意协议（I accept……），等待后重启eclipse即可。（界面展示如下）

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg03.PNG)

#### 将eclipse换为原版

+ 首先找到eclipse的安装路径，进入所在位置的文件夹中找到eclipse的配置文件。如图所示

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg04.PNG)

  ​	这里的eclipse.ini便是eclipse的配置文件。

+ 使用文本编辑器打开配置文件

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg05.PNG)

  这里使用红线圈起来的，便是eclipse语言项的配置，如果没有这一条语句，直接在配置文件最末行之后添加如下语句。

  ```ini
  -Duser.language=en_US
  ```

  添加完成后保存文档，重启eclipse即可。

  在后续的使用中若想换回中文，则可将这里的en_US改为zh或zh_cn，代码如下

  ```ini
  -Duser.language=zh
  ```

  完成后界面展示如下

  ![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg06.PNG)

  

  



### 问题二  ——  更改快捷键时报错

之前，我更换了eclipse的语言包，在继续使用的过程中发现，有些快捷键并不符合自己的编程习惯，于是进行改键操作，结果出现了**Invalid thread access**的错误，大致意思是请求的线程无效，如图所示。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg07.PNG)

我寻思着是不是跟语言包有关系，于是更换语言包继续改键操作，结果还是相同的问题，如图。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg08.PNG)

经过各种实验和百度后，终于发现了问题所在，原因是eclipse对插件的缓存不完整，解决方案如下。

首先，右键属性打开eclipse快捷方式的属性面板，打开后在目标一栏的输入框后添加“-clean”，如图。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg09.PNG)

添加完成后点击确定，重新启动eclipse，启动成功后进行改键就能打开keys界面了。如图

![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg10.PNG)

换回中文版也可运行，如图。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/Heclipseimg11.PNG)

使用这种方法会导致我们查看类的层次结构（Type Hierarchy）报错，解决这个问题，我们只需要将Type Hierarchy关闭，再重新打开即可。

**注意：这里的-clean命令再首次运行eclipse时会用到，之后便可以去掉这条命令。**

### 结语

目前所遇到的问题只有这些，以后的一些关于eclipse使用时遇到的麻烦会记录到此中。总之，目前虽然IDEA很火，但是掌握一门语言所用到的各种编译器也是能够提高自己的一种方法，希望能够借此来熟练掌握eclipse的使用方法，也希望能够帮助到和我一样遇到这些问题的小伙伴。



***

与君共勉

>真正的人生，只有在经过艰难卓绝的斗争之后才能实现。
>
>​																																	——塞涅卡 

