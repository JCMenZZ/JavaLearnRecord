## 框架学习——Spring（三）

### Spring集成Web环境

在真实的项目中，后端代码要结合前端环境进行使用交互，当后端使用Spring整合时就需要集成Web环境使得Web层的各种`API`能正常使用。具体方法如下：

`pom.xml`中需要添加如下坐标：

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
    <version>2.2.1</version>
    <scope>provided</scope>
</dependency>
```

#### 测试

Dao层代码如下：

UserDao.java

```java
public interface UserDao {
    void save();
}
```

UserDaoImpl.java

```java
public class UserDaoImpl implements UserDao {
    public void save() {
        System.out.println("save UserDaoImpl...");
    }
}
```

service层代码如下：

UserService.java

```java
public interface UserService {
    void save();
}
```

UserServiceImpl.java

```java
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save() {
        userDao.save();
    }
}
```

web层代码如下：

UserServlet.java

```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = app.getBean(UserService.class);
        userService.save();
    }
}
```

Spring配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
">
    <bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
    <bean id="userService" class="com.jclight.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"/>
    </bean>
</beans>
```

web.xml如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>UserServlet</servlet-name>
        <servlet-class>com.jclight.web.UserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>UserServlet</servlet-name>
        <url-pattern>/userServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

此时配置好需要的Tomcat服务器即可测试上述的Spring集成Web环境是否成功搭建。

#### 对ApplicationContext应用上下文获取方式的优化

通过上述的测试我们可以分析出，当web层有很多方法时，应用上下文会获取很多次同时也会new很多对象，此时不仅影响开发效率还降低了整个系统的性能，所以此时我们需要对应用上下文的获取进行优化。在Web项目中，可以使用`ServletContextListener`监听Web应用的启动，我们可以在Web应用的启动时，就加载Spring的配置文件，创建应用上下文对象ApplicationContext，在将其存储到最大的域servletContext域中，这样就可以在任意位置从域中获得应用上下文ApplicationContext对象了。具体做法如下：

首先，创建一个listener的包并在该包下创建一个类，代码如下：

```java
public class ContextLoaderListener implements ServletContextListener {
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        ServletContext servletContext = servletContextEvent.getServletContext();
        servletContext.setAttribute("app", app);
        System.out.println("this is ContextLoaderListener");
    }

    public void contextDestroyed(ServletContextEvent servletContextEvent) {

    }
}
```

然后，在web.xml文件中配置监听器，配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--  配置监听器  -->
    <listener>
        <listener-class>com.jclight.listener.ContextLoaderListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>UserServlet</servlet-name>
        <servlet-class>com.jclight.web.UserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>UserServlet</servlet-name>
        <url-pattern>/userServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

最后修改Web层的方法，代码如下：

```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        ApplicationContext app = (ApplicationContext) servletContext.getAttribute("app");
        UserService userService = app.getBean(UserService.class);
        userService.save();
    }
}
```

**注意**：这里的`ServletContext`对象还可以使用req来获取。

上述的代码中还存在一些小问题，在实际开发中我们需要将配置文件提取出来且获取应用上下文对象应该提取成工具类，减少自定义的字符串的使用，简化开发增加开发效率，具体做法如下：

提取配置文件并在web.xml中进行配置，代码如下：

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--  配置监听器  -->
    <listener>
        <listener-class>com.jclight.listener.ContextLoaderListener</listener-class>
    </listener>
    <!--  全局初始化参数  -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <servlet>
        <servlet-name>UserServlet</servlet-name>
        <servlet-class>com.jclight.web.UserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>UserServlet</servlet-name>
        <url-pattern>/userServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

ContextLoaderListener.java

```java
public class ContextLoaderListener implements ServletContextListener {
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        ServletContext servletContext = servletContextEvent.getServletContext();
        //读取web.xml中的全局参数
        String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
        ApplicationContext app = new ClassPathXmlApplicationContext(contextConfigLocation);
        //将Spring应用上下文对象存储到最大的域（ServletContext）中
        servletContext.setAttribute("app", app);
        System.out.println("this is ContextLoaderListener");
    }

    public void contextDestroyed(ServletContextEvent servletContextEvent) {

    }
}
```

UserServlet.java

```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        UserService userService = app.getBean(UserService.class);
        userService.save();
    }
}
```

util包下的WebApplicationContextUtils.java

```java
public class WebApplicationContextUtils {
    public static ApplicationContext getWebApplicationContext(ServletContext servletContext) {
        return (ApplicationContext) servletContext.getAttribute("app");
    }
}
```

如此，开发者在进行开发时不需要针对配置文件的修改而去修改整片的代码，且不需要去查找域中的字符串代表的实际意义，大大的简化了开发的复杂度。注意：这些监听器在Spring中都被具体封装了，在Spring整合web项目时，直接使用Spring进行配置即可。在Spring内部提供了一个监听器`ContextLoaderListener`，该监听器就是对上述代码的封装，在其内部加载Spring配置文件，创建应用上下文对象，并存储到ServletContext域中，提供了一个客户端工具WebApplicationContextUtils可供使用者获取应用上下文对象。使用方法如下：①在web.xml中配置ContextLoaderListener监听器（导入spring-web的坐标）②使用WebApplicationContextUtils获得应用上下文对象ApplicationContext。

导入相关依赖如下：

```xml
 <!--    spring-web    -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
```

配置web.xml如下：

```xml
<!--  配置监听器  -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--  全局初始化参数  -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

UserServlet.java

```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        assert app != null;
        UserService userService = app.getBean(UserService.class);
        userService.save();
    }
}
```

### 报错处理

在我们配置好Tomcat服务器有时会出现`代理抛出异常错误: java.rmi.server.ExportException: Port already in use: 1099; nested exception is: java.net.BindException: Address already in use: JVM_Bind`错误，通过错误提示分析可得端口1099被占用了，所以此时我们需要打开cmd并输入`netstat -aon|findstr 1099`，找到占用1099端口的进程，找到它对应的进程ID。这时可以通过任务管理器关闭该进程，或通过cmd命令关闭进程，命令为`taskkill -f -pid 264`。我这里遇到的进程正好是idea64.exe，所以这里只需要重启IDEA即可运行Tomcat服务器。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201125144847.png)