## Git的常用方式

### （1）使用Git Bash将本地的项目上传至远程仓库

**具体步骤**如下：

1. 在项目下的文件夹中使用`git init`命令，初始化本地仓库。
2. 使用`git status -s`命令，查看本地还未提交的文件。
3. 使用`git add .`命令，将要提交的文件提交到缓存中。
4. 使用`git commit -m "firstSubmit"`命令，来简要标识提交内容。
5. 使用`git remote add origin "远程仓库的地址"`，来和远程仓库获取连接。
6. 使用`git pull --rebase origin master`命令，拉取远程库与本地同步合并。（远程仓库不为空时，必须要执行此步，否则可能会提交失败）
7. 使用`git push -u origin master`命令，将本地仓库的内容上传至远程仓库。

**注意**：如果出现无法上传或产生冲突的问题，最简单的一个办法就是将本地仓库下的.git文件夹删除，之后按照上面的步骤重新执行一遍即可成功提交。 上述比较关键的步骤为3、4、6、7。如果已经对仓库初始化且关联了远程仓库，此时可省去1、5步。 一般情况下可以不用执行第六步，若提交失败可执行此步骤。

**补充**：在**Git Bash**上查看自己的**public key**可以在Git控制台上输入`cat ~/.ssh/id_rsa.pub`即可查看自己的公钥。对远程仓库的连接测试`ssh -T git@github.com`，如果测试gitee只需将github改为gitee即可。

### （2）如何删除远程仓库中的某个文件？

删除远程仓库中的文件有两种方式，①在远程仓库中直接删除，之后再将远程仓库中的内容拉取到本地。②可在Git Bash界面使用命令对远程仓库中的某个文件进行删除。

第一种不在记录，这里记录一下第二种方式，操作步骤如下：

**首先**，在本地仓库下打开Git Bash界面。

**然后**，执行命令`git rm [文件名]`，执行完成后删除命令会自动将其提交到暂存区不需要手动提交。这时本地仓库下的该文件就被删除了

**再然后**，执行命令`git status -s`查看文件状态，之后执行`git commit -m "提交日志标记"`。

**最后**，执行命令`git push`，将本地的操作推送到远程仓库中。这时刷新远程仓库可以发现该文件已经删除了。

### （3）如何在IDEA中使用Git？

**首先**，对IDEA进行配置，此时应该下载好Git客户端，并将其安装。（已经有Git的忽略）

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112102519.png)

这里IDEA会自动将需要的Git的路径获取，如果没有自动获取就进行手动配置。完成后点击Test测试是否配置成功。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112102810.png)

**其次**，对GitHub进行配置，点击Add account，将自己的GitHub账户添加到IDEA。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112103108.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112103229.png)

**再次**，登录GitHub，在GitHub上创建一个远程的仓库，此时在本地需要提交的文件夹下执行上传步骤，使该文件夹成为本地仓库，并将一些文件提交到远程仓库中。这时我们打开这个已经上传了的项目。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112111155.png)

有时我们这样的操作并不会使IDEA同步Git，所以此时我们需要在IDEA中创建一个仓库即可。操作步骤如下。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112111407.png)

这时，我们在项目中修改代码或完善代码后，只需要右键点击项目名，将改动提交到缓存中。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112111656.png)

此时如果确定要提交，只需要右键点击项目选择Git-->Commit Directory或者在工具栏点击Git后面的绿色的对勾或使用快捷键（Ctrl+K）即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201112111155.png)

此时填入Commit Message，然后点击Commit And Push即可。如果不是在Git Bash中执行的仓库初始化，此时会要求填写上传至何处的地址，只需要将GitHub下面的URL填入即可。这时，刷新GitHub界面即可查看刚刚提交的内容。

**注意**：如果我们对本地项目的一些文件不想上传至远程仓库，此时需要在本地仓库下创建一个`.gitignore`文件，并在其中拷贝github帮我们生成的`.gitignore`中的内容。

在IDEA中我们还可以对代码进行比对、创建分支、切换分支、合并分支等一系列的Git操作，这里不再记录。