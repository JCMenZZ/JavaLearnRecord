## IDEA中使用Maven构建简单的SSM项目

我们在进行开发时，除了搭建好项目的基本架子之外，还可以根据项目计划书对项目的基本结构进行扩充，一般搭建一个SSM项目有很多方法，但是网上的一些答案总体来说都有点复杂了，这里记录一个简单的快速构建SSM项目的步骤。

百度的一个结果如下：[https://www.jianshu.com/p/f4f84dcfebd4](https://www.jianshu.com/p/f4f84dcfebd4)。这个还是比较详细的，但有点复杂，不适合新手。

### 前期条件

工具要求：IDEA编译器、Maven、JDK版本1.8以上、Tomcat和MySql的版本都可以。

前期准备：在任意的位置下创建一个文件夹，并使用IDEA打开该文件夹。

**注意**：Tomcat的版本不用太高，否则会造成jsp中无法使用EL表达式的问题。IDEA中有时会自带版本为1.5的JDK，这时需要手动修改IDEA默认的JDK版本，否则会导致有些API在低版本的JDK中无法通过编译。

### 新建项目

在IDEA中右键点击该文件夹，选择New-->Module，在左侧选择Maven，右边不选择任何，点击Next，在其中填入相关信息，最后点击finish即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226120142.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226120355.png)

此时idea会自动给我们创建好对应的目录下的java文件夹和resource文件夹，并会生成一个测试文件夹，由于我们是WEB项目，所以还需要引入webapp文件夹。做法如下：

选中项目文件夹点击file-->project structure-->Modules，在Modules界面点击加号点击Web，此时需要修改web文件夹的名称和路径，点击界面中右侧的笔图标，修改路径及文件夹名称为：`src\main\webapp\`，其余部分保持不变。最后点击apply点击ok即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226121022.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226121143.png)

此时可以在对应的java目录下建立自己需要的包结构，最后我们的目录结构如下：

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226121833.png)

**注意**：有时编译器不会帮你生成index.jsp文件，如果缺少可以手动添加一个，这里的index.jsp只是作为测试使用，如果不需要也可以不用添加。

### 完善项目

#### 完善pom.xml文件

在pom.xml下添加必要的依赖坐标，具体如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jclight</groupId>
    <artifactId>mavenBuild_SSMTemplate</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <!--  声明版本号  -->
    <properties>
        <springframework.version>5.0.5.RELEASE</springframework.version>
    </properties>
    <!--  引入依赖  -->
    <dependencies>
        <!--数据库-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.32</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--json-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.10.3</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.75</version>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
        <!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>
        <!--log日志-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.7</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.7</version>
        </dependency>
        <!--文件上传-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
        <!--Spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <excludes>
                    <exclude>**/*.java</exclude>
                </excludes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                    <encoding>UTF-8</encoding>
                    <fork>false</fork>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

**注意**：这里只是目前能应用上的一些依赖，后期如果缺少什么可以补充什么。这里还需要手动去刷新一下写好的pom.xml文件，如果配置了自动刷新可以不需要这一步。

#### 完善web.xml文件

在完善之前还需要创建Spring的核心配置文件和SpringMVC的配置文件，具体如下：

在resource文件夹下，创建核心配置文件`applicationContext.xml`，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

在这里可以引入其他的配置文件，例如：引入shiro、redis集群、cache-redis缓存、自定义组件等配置，此时就不需要改动web.xml了。

在resource文件夹下，创建核心配置文件`spring-mvc.xml`，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
">
    <!--  开启SpringMVC注解驱动  -->
    <mvc:annotation-driven/>
    <!--配置Spring使用CGLIB动态代理技术进行织入增强-->
    <aop:aspectj-autoproxy proxy-target-class="true"/>
    <!--开启自动扫描-->
    <context:component-scan base-package="com.jclight.controller"/>
    <!--配置避免IE执行AJAX时返回的JSON出现下载文件-->
    <bean id="mappingJackson2HttpMessageConverter"
          class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>text/html;charset=UTF-8</value>
            </list>
        </property>
    </bean>
    <!--对模型和视图进行解析-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--错误处理-->
    <bean id="exceptionResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="defaultStatusCode" value="500"/>
        <property name="warnLogCategory"
                  value="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver"/>
    </bean>
    <!--开启静态资源访问权限-->
    <mvc:default-servlet-handler/>
</beans>
```

完善web.xml文件，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--配置全局初始化参数-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!--配置编码过滤器-->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <async-supported>true</async-supported>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--配置Spring监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--配置防止Spring内存溢出监听器-->
    <listener>
        <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
    </listener>
    <!--配置SpringMvc的前端控制器-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

在这其中还可以配置spring容器中其他配置（MyBatis、log4j、项目访问默认欢迎页，自定义过滤器等）。这里的配置文件的完善就结束了，在开发中如果还需要配置，可以再继续增加。

#### 配置Tomcat服务器

点击绿色锤子右边的框，在其中找到Edit Configurations，打开该界面点击加号选中Tomcat Server点击local，检查server界面的配置，无误后点击Deployment界面，点击加号选择Artifact，添加后缀带有war exploded的项，修改访问路径为”/“，最后点击apply点击ok即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226220818.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226221432.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201226221532.png)

最后运行服务器，观察浏览器界面显示，此时我们的SSM项目的一个基本结构就搭建完成了，之后根据项目的具体要求进行适当的修改和完善即可。