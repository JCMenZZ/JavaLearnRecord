## Git的安装与使用

#### 前言

平时我们在开发或学习中，基本都会用到**Github**来进行学习或存储资料、上传代码等操作。所以我们的目的就是将**github**上的仓库拷贝到本地或从本地主机提交代码到**github**上。

#### 第一步 下载安装Git

> 下载地址：[http://gitforwindows.org/](http://gitforwindows.org/)

下载完成后，根据个人需求选择合适的选项进行安装，安装成功后，打开DOS界面验证是否安装成功。（可以输入`git`或`git --version`进行验证）

![image-20201019103941917](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019103941917.png)

#### 第二步 配置Git

当我们安装好Git后，可以通过如下的三个命令来进行配置Git，命令如下：

```shell
git config --global user.name "填写昵称"
git config --global user.email "填写邮箱"
git config --global push.default simple
```

**注意**：邮箱和姓名只是用于git的日志标识。

配置完成后，可以使用`git config --global --list`来查看我们的配置。

#### 第三步 进入Git的Bash界面

在确认配置无误后，在桌面右键弹出的菜单中选择Git Bash Here，如图

![image-20201019105805306](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019105805306.png)

点击后进入Bash界面

![image-20201019150229398](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019150229398.png)

#### 第四步 配置SSH

在Bash界面输入下面这段代码，按下三次回车键，生成ssh密钥。

```bash
ssh-keygen -t rsa -C "你的邮箱"
```

![image-20201019111910123](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019111910123.png)

根据生成的ssh密钥的路径，找到密钥文件。

![image-20201019112121904](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019112121904.png)

打开`id_rsa.pub`文件，复制里面的内容。

#### 第五步 进入Github网站进行SSH配置

打开自己的github账号，如果没有就去注册一个。在github中进入设置界面。

![image-20201019112741323](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019112741323.png)

点击New SSH key将我们复制的内容粘贴进去。

![image-20201019113052957](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019113052957.png)

此时，我们在Github上的配置就完成了。测试一下，是否连接成功。输入`ssh -T git@github.com`等待片刻，输入yes后，观察界面输出。

![image-20201019113723077](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019113723077.png)

此时，就表示连接成功了。

#### 第六步 设置本地的git仓库

配置完成后，我们可以自己创建一个文件夹作为本地的git仓库，也可以将项目文件夹作为一个git仓库。只需要进入相对应的文件夹下，点击鼠标右键，选择Git Bash Here，在Bash界面下输入`git init`，等待片刻后该文件夹下会自动生成一个`.git`文件，此时这个目录就变成了本地的git仓库。

![image-20201019150435143](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019150435143.png)

![image-20201019150500209](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019150500209.png)

**注意**：`.git`目录是一个隐藏的目录。

#### 第七步 在Github上创建一个仓库（Repository）并在本地操作

当我们完成了前面的几步操作后，就可以在Github上创建一个项目的仓库。在Github上找到**create a New Repository**按钮，点击后填写项目名称、项目说明后点击create就可以成功创建了。创建完成后，会出现一个提示界面，该界面上有两个按钮`HTTPS`和`SSH`，点击SSH就可以查看当前仓库的地址了。（例如：`git@github.com:……/…….git`）将这个地址记下来，在我们本地的这个项目的仓库中打开Git Bash。

![image-20201019155400935](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019155400935.png)

此时，我们将需要上传的文件放在此仓库中，并在Bash界面中输入如下命令，

```bash
git add .
```

这个命令是提交当前目录下的所有变化，`.`表示文件夹下的所有文件。此外，还有`git add -A`提交项目的所有变化，但不局限于当前目录。以及`git add -u`提交被修改和被删除的文件，不包括新文件。

完成后，输入命令`git commit -m "提交说明"`。等待执行完成后，输入命令`git remote add origin git@github.com:……/…….git`将远程仓库的地址添加到本地。之后执行上传命令`git push -u origin master`，观察界面输出看是否上传成功。

![image-20201019185146062](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201019185146062.png)

有时会出现上传失败的错误提示，这时有可能是在github中的远程仓库存在了文件而本地仓库却没有该文件所造成的，这时就可以执行这条命令`git pull origin master `，来进行处理。有时也会出现例如`fatal:remote origin already exists`的错误，而导致无法上传，这时只需要执行如下的命令就可以了。

```bash
git remote rm origin
```

移除远程的origin，移除成功后重新添加即可。也就是再次输一遍下面这条命令。

```bash
git remote add origin git@github.com:……/…….git
```

至此，关于Git的安装以及配合Github的使用就结束了。