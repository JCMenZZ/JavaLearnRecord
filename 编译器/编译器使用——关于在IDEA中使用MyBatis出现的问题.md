## 编译器使用——关于在IDEA中使用MyBatis出现的问题

### MyBatis插件冲突问题

在使用MyBatis开发项目的时候，IDEA无法给我们像Spring一样的舒适度，也就是无法进行java代码与配置文件的相互跳转和自动生成，所以就下载了MyBatis插件，我个人下载了两个插件都是以前下的。（Free MyBatis Plugins和MyBatisX）但是在后面使用的时候，出现了如下问题。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201217191306.png)

究其原因是两个插件起了冲突，我们只需要关闭其中的一个就行。关于这两个插件到底使用哪个？我们在使用MyBatis-Plus进行开发时可以使用MyBatisX插件，如果用不到MP可以使用Free MyBatis Plugins。MyBatisX的跳转标志是一只小鸟，Free MyBatis Plugins的跳转标志是箭头。

### 关于MyBatis的配置文件

MyBatis框架中有两个重要的配置文件（映射文件和核心配置文件），这两个配置文件在编写时IDEA不会为我们创建模板，所以每次我们在编写配置文件时相应的固定配置要手动去写，有时还会去查看一下是否编写正确，所以为了节省时间提高效率，我们可以在IDEA中编写配置文件模板，这样在使用MyBatis进行开发时，我们就不需要去写相应的固定配置。具体做法如下：

首先，打开File->Settings找到File and Code Templates。然后，点击加号创建一个代码模板，这里的Name可以根据个人风格去创建，在Extension中填写文件类型。最后，将我们对应的代码模板粘贴到Name下面的区域，点击Apply点击ok即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202020-12-17%20202501.png)

两个的创建方式一样，不同的地方在于模板不同，模板如下：

映射文件模板

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
    
</mapper>
```

核心配置模板

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

配置完成后，点击File->New，这时就可以在新建菜单中找到对应的配置文件模板了。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201217203256.png)

因为MyBatis和Spring不同，在IDEA中只要在Maven中添加了相关的Spring坐标，就可以生成我们需要的Spring配置文件模板，而MyBatis不会，所以可以采用上述的方式进行配置，方便我们日常的开发学习。当然，最好还是能熟练的写出全部配置为好，只依赖工具的程序员不是一个好的程序员。