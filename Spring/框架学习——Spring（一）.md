## 框架学习——Spring（一）

### 构建Spring工程

在IDEA中构建Spring工程的方法有很多，比如利用Maven直接进行构建或者在重构Servlet项目时修改并添加一些配置文件也可以达成构建Spring的目的，这里记录一个与上述不一样的构建方式。

#### 具体步骤

首先，创建一个用来保存多个Spring项目的文件夹，接着打开IDEA并选择打开项目，找到这个刚刚创建好的文件夹，鼠标右击文件夹名称，选择New model。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201114153934.png)

其次，点击Maven（不要点击Maven显示的任何东西），点击Next。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201114154120.png)

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201114154212.png)

在GroupId中填入组织名称（例如：com.bytedance），在ArtifactId中填入项目名称（例如：spring_aop），点击finsh即可。这时IDEA会帮助我们生成需要的项目文件夹及文件，但是缺少web文件夹，所以此时我们点击file选择project Structure查看JDK是否配置正确，之后点击Facets点击加号选择web后修改web生成的路径，使其在src/main下，并修改名称为webapp即可。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201114154938.png)

### Spring基础

#### 定义

Spring是分层的Java EE/SE应用全栈（全面的解决方案）轻量级开源框架，以IOC（反转控制）和AOP（面向切面编程）为内核。提供了展现层（SpringMVC）和持久层（Spring JDBCTemplate）以及业务层事务管理等众多的企业级应用技术，整合了开源世界中众多著名的第三方框架和类库，逐渐成为了使用最多的Java EE企业级应用开源框架。

#### 优势

+ 方便解耦，简化开发。

  通过Spring提供的IOC容器，可以将对象间的依赖关系交由Spring进行控制，避免硬编码所造成的过度耦合。其次，用户也不必再为单例模式、属性文件解析等这些底层的需求写代码，可以更专注于上层的应用。

+ AOP编程的支持

  通过Spring的AOP功能，方便进行面向切面编程。许多不容易用传统OOP实现的功能可以通过AOP轻松实现。

+ 声明式事务的支持

  可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活的进行事务管理，提高开发效率和质量。

+ 方便程序的测试

  可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手可做的事情。

+ 方便集成各种优秀的框架

  Spring对各种优秀框架（Struts、Hibemate、Hessian、Quartz等）的支持。

+ 降低Java EE API的使用难度

  Spring对JavaEE API（如JDBC、JavaMail、远程调用等）进行了薄薄的封装层，使这些API的使用难度大大降低。

+ 适合进行源码的学习

  Spring的实现是非常巧妙的，所以可以通过阅读Spring的源码来学习Java的源码。

#### 开发步骤

1. 导入Spring开发的基本包坐标（Maven中），非Maven工程需要导包。
2. 编写Dao接口和实现类
3. 创建Spring核心配置文件
4. 在Spring配置文件中配置实现类
5. 使用Spring的API获得Bean实例

代码实现如下：

**导入Spring开发的基本包坐标**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.7.RELEASE</version>
    </dependency>
</dependencies>
```

**编写Dao接口和实现类**

Dao接口

```java
public interface UserDao {    
    void save();
}
```

实现类

```java
public class UserDaoImpl implements UserDao {
    public void save() {
        System.out.println("UserDao save...");
    }
}
```

**创建Spring核心配置文件**

在src/main下的resources文件夹中右键单击文件夹，选择new-->XML ...-->Spring Config，并给它起一个名字**applicationContext**，如下图所示。

![](https://gitee.com/JCLightZZ/image-bed/raw/master/20201114172259.png)

**注意**：如果在配置好`pom.xml`后没有点击出现的蓝色刷新符号将不会显示这个Spring Config文件。

**在Spring配置文件中配置UserDaoImpl**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--配置UserDaoImpl-->
    <bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
</beans>
```

**使用Spring的API获得Bean实例**

重新创建一个包，在该包下创建一个类并写一个main方法，运行测试，若测试成功则表示Spring配置成功。代码如下：

```java
public class UserDaoDemo {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new 	ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.save();
    }
}
```

#### Spring配置文件

##### Bean标签

**作用**：用于配置对象交由Spring来创建，默认情况下它调用的是类里的无参构造，若无无参构造则不能创建成功。

**基本属性**

|     属性名     |                             描述                             |     作用     |
| :------------: | :----------------------------------------------------------: | :----------: |
|       id       |                 Bean实例在Spring中的唯一标识                 |   基本配置   |
|     class      |         Bean的全限定名称（某一个Bean所在的具体位置）         |   基本配置   |
|     scope      | 指定对象的作用范围，可取值singleton（默认取值，单例）、prototype（多例）、request（将对象存入request域中，用于Web项目）、session（将对象存入session域中，用于web项目）、global session（应用在Portlet环境，若没有portlet环境那么globalSession相当于session） |   范围配置   |
|  init-method   |                  指定类中的初始化方法的名称                  | 生命周期配置 |
| destroy-method |                   指定类中的销毁方法的名称                   | 生命周期配置 |

**注意**：当scope的取值为singleton时，Bean的实例化个数是一个，Bean的创建时机在配置文件加载时就创建完成了，Bean的生命周期分为三种（对象创建、对象运行、对象销毁）。在singleton中，当应用加载创建容器时，对象就被创建了，创建成功后只有容器在，对象就一直存活着，当应用卸载销毁容器时，对象就被销毁了。当scope的取值为prototype时，Bean的实例化个数是多个，Bean的创建时机在调用`getBean()`方法时就创建完成了，此时的生命周期和上述一样，当使用对象时会创建一个对象实例，只要对象在使用中，就一直存活着，当对象长时间不用时就会被Java的垃圾回收机制回收了。

##### Bean实例化的三种方式

+ 无参构造方法实例化。当我们不进行任何配置时，当前默认的实例化方式就是该方式。

+ 工厂静态方法实例化。当我们对Bean标签配置该模式时，实例化方式就成为了该方式。配置方法如下：

  ```xml
  <bean id="userDao" class="com.jclight.factory.StaticFactory" factory-method="getUserDao"/>
  ```

  `factory-method`就是指定实例化时所对应的方法。

+ 工厂实例方法实例化。当我们对Bean标签配置该模式时，实例化方式就成为了该方式。配置方法如下：

  ```xml
  <bean id="factory" class="com.jclight.factory.DynamicFactory"/>
  <bean id="userDao" factory-bean="factory" factory-method="getUserDao"/>
  ```

  这里就是通过配置的factory-bean的值来调用id为factory的bean标签，使得指定的方法为工厂实例方法。

##### Bean的依赖注入

###### 分析——为什么要用依赖注入？

当我们在配置文件中配置了Dao和Service的Bean时，这时我们在Service的实现中获取Dao的Bean后在Web层中的Controller中获取Service的Bean，并调用相关的方法，这时就出现了一个问题。我们获取Service的Bean时同时获取了Dao的Bean，这两者都是在容器外部获取的，而实际只用到了Service的Bean来调用方法，所以此时应该将Dao的Bean的获取放在容器中进行获取。

修改前的配置文件如下：

```xml
<bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
<bean id="userService" class="com.jclight.service.impl.UserServiceImpl"/>
```

修改后：

```xml
<bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
	<bean id="userService" class="com.jclight.service.impl.UserServiceImpl">
    	<!--    set方法实现依赖注入    -->
        <property name="userDao" ref="userDao"/>
</bean>
```

修改完成后，这时在我们的代码中就只有Service的Bean，大大降低了两者之间的依赖关系，使得代码更加容易理解和维护，这就是Spring中的依赖注入。所谓依赖注入，它是Spring框架的核心IOC的具体实现。通过IOC，把对象的创建交由容器去完成，在代码中不可能出现没有依赖的状况，通过IOC解耦只是降低了他们的依赖关系，但不会消除。简单的来说就是等框架把持久层对象传入业务层，而不用我们去手写获取方法。

###### 依赖注入的方式

+ 通过构造方法进行注入

  通过有参构造进行注入时，所注入的类必须要有有参构造，注入代码如下：

  ```xml
  <bean id="userService" class="com.jclight.service.impl.UserServiceImpl">
  	<constructor-arg name="userDao" ref="userDao"/>
  </bean>
  ```

+ 通过set方法进行注入

  通过set方法进行注入时需要添加一个子标签`property`，除此之外还可以使用P命名空间注入，这种注入方式要比前一种更为方便。具体如下：

  首先在配置文件中需要先引入P命名空间，然后修改注入方式即可，代码如下：

  ```xml
  <!--在beans下面加入下面这一段-->
  <!--xmlns:p="http://www.springframework.org/schema/p"-->
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:p="http://www.springframework.org/schema/p"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
  >
      <bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
      <!--    set方法实现依赖注入    -->
      <!--    <bean id="userService" class="com.jclight.service.impl.UserServiceImpl">-->
      <!--        <property name="userDao" ref="userDao"/>-->
      <!--    </bean>-->
      <!--  set方法之p命名空间注入  -->
      <bean id="userService" class="com.jclight.service.impl.UserServiceImpl" p:userDao-ref="userDao"/>
  </beans>
  ```

  注意：依赖注入时这里的`property`中的name属性代表的userDao和ref代表的userDao是不一样的，name属性代表的是set方法的userDao，而ref属性代表的是配置文件中的userDao。

###### 依赖注入的数据类型

依赖注入的数据类型主要有三种，①普通数据类型，②引用数据类型，③集合数据类型

普通数据类型注入，注入的方式不变可以使用set方法注入，也可以使用构造函数注入。前提要通过set方法设置这些属性，配置文件的书写如下：

```xml
<bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl">
    <!--    通过set方式进行普通数据类型的依赖注入    -->
    <!--<property name="username" value="张三"/>-->
    <!--<property name="age" value="22"/>-->
    <!--    通过构造函数进行普通数据类型注入    -->
    <constructor-arg name="username" value="张三"/>
    <constructor-arg name="age" value="22"/>
</bean>
```

引用数据类型注入和上面依赖注入方式中是一样的。

集合数据类型注入，注入方式和上述一样，具体配置如下：

```xml
<bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl">
    <!--    通过set方式进行集合数据类型的依赖注入    -->
    <property name="strList">
        <list>
            <value>张三</value>
            <value>李四</value>
            <value>王五</value>
        </list>
    </property>
    <!--    Map带有引用类型的注入配置    -->
    <property name="userMap">
        <map>
            <entry key="user01" value-ref="user01"/>
            <entry key="user02" value-ref="user02"/>
        </map>
    </property>
    <!--    Properties的注入配置    -->
    <property name="properties">
        <props>
            <prop key="prop01">propValue01</prop>
            <prop key="prop02">propValue02</prop>
            <prop key="prop03">propValue03</prop>
        </props>
    </property> 
    <!--    通过构造函数进行集合数据类型的依赖注入    -->
    <!--<constructor-arg name="strList">
        <list>
            <value>张三</value>
            <value>李四</value>
            <value>王五</value>
        </list>
    </constructor-arg>
    <constructor-arg name="userMap">
        <map>
            <entry key="user01" value-ref="user01"/>
            <entry key="user02" value-ref="user02"/>
        </map>
    </constructor-arg>
    <constructor-arg>
        <props>
            <prop key="pp1">pv1</prop>
            <prop key="pp2">pv2</prop>
            <prop key="pp3">pv3</prop>
        </props>
    </constructor-arg>-->
</bean>
<bean id="user01" class="com.jclight.domain.User">
    <property name="name" value="Tom"/>
    <property name="addr" value="上海"/>
</bean>
<bean id="user02" class="com.jclight.domain.User">
    <property name="name" value="Jerry"/>
    <property name="addr" value="北京"/>
</bean>
```

##### Spring的分模块开发

简而言之就是在一个Spring的配置文件中引入其他的配置文件，以此来使得配置文件清晰简洁，便于后期的维护和开发。

具体方法就是在Spring的主配置文件中使用import标签来进行对其他配置文件的加载。在实际的开发应用中，我们可以将一个很庞大的主配置文件进行拆解（拆解的方式有很多，可以根据需求模块进行拆解，也可以根据不同层来进行拆解等），拆解完成后，在主配置文件中引入即可。代码示例如下：

```xml
 <import resource="applicationContext-user.xml"/>
```

##### Spring相关的API

**ApplicationContext**：接口类型，代表应用上下文，可以通过其实例获取Spring容器中的Bean对象。它的实现类有三个，具体如下。

|               实现类               |                             描述                             |
| :--------------------------------: | :----------------------------------------------------------: |
|   ClassPathXmlApplicationContext   |                  从类的根目录下加载配置文件                  |
|  FileSystemXmlApplicationContext   |  从磁盘路径上加载配置文件，配置文件可以在磁盘的任意一个位置  |
| AnnotationConfigApplicationContext | 使用注解配置容器对象时，需要使用此类来创建Spring容器。主要用来读取注解 |

##### Spring配置数据源

###### 数据源（连接池）的作用

①提高程序的性能；②使用数据源时需要先实例化数据源，初始化部分连接资源；③使用连接资源时从数据源获取；④使用完毕后将连接资源归还给数据源；

常见的数据源（连接池）有DBCP、C3P0、BoneCP、Druid等

手动方式创建c3p0数据源的代码示例如下：

```java
ComboPooledDataSource dataSource = new ComboPooledDataSource();
//设置基本的连接参数
dataSource.setDriverClass("com.mysql.jdbc.Driver");
dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
dataSource.setUser("root");
dataSource.setPassword("root");
Connection connection = dataSource.getConnection();
```

手动方式创建druid数据源的代码示例如下：

```java
DruidDataSource dataSource = new DruidDataSource();
//设置基本的连接参数
dataSource.setDriverClassName("com.mysql.jdbc.Driver");
dataSource.setUrl("jdbc:mysql://localhost:3306/test");
dataSource.setUsername("root");
dataSource.setPassword("root");
DruidPooledConnection connection = dataSource.getConnection();
```

通过properties资源文件进行创建c3p0数据源的代码示例如下：

```java
//读取配置文件
ResourceBundle resourceBundle = ResourceBundle.getBundle("jdbc");
String driver = resourceBundle.getString("jdbc.driver");
String url = resourceBundle.getString("jdbc.url");
String username = resourceBundle.getString("jdbc.username");
String password = resourceBundle.getString("jdbc.password");
//创建数据源对象
ComboPooledDataSource dataSource = new ComboPooledDataSource();
//设置连接参数
dataSource.setDriverClass(driver);
dataSource.setJdbcUrl(url);
dataSource.setUser(username);
dataSource.setPassword(password);
//获取资源
Connection connection = dataSource.getConnection();
```

该方式创建druid数据源也是一样的，只不过在创建数据源时略有所不同。使用数据源来操作JDBC时，必须先在pom,xml中导入依赖坐标。
```xml
<!--    数据源（连接池c3p0）    -->
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<!--    数据源（连接池druid）    -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

###### 利用Spring容器来创建数据源对象

通过手动的方式我们可以看出这些数据源的连接参数的配置是可以通过set方式进行依赖注入的，所以我们可以在Spring容器中使用set方式进行依赖注入以此来达到让Spring来创建数据源对象。配置示例如下：

```xml
<!--  配置数据源（c3p0连接池）  -->
<!--<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
    <property name="user" value="root"/>
    <property name="password" value="root"/>
</bean>-->
<!--  配置数据源（druid连接池）  -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
```

获取对象实例代码如下：

```java
ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
DataSource dataSource = app.getBean(DataSource.class);
Connection connection = dataSource.getConnection();
```

此外，我们还可以让Spring帮我们去读取配置文件（properties）中的信息，具体代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
                           http://www.springframework.org/schema/beans                            
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context  
                           http://www.springframework.org/schema/context/spring-context.xsd
                           ">
    <!--  加载外部的properties配置文件  -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```

**注意**：要让Spring读取配置文件必须引入context的命名空间和约束路径，然后再进行配置即可。引入的命名空间为` xmlns:context="http://www.springframework.org/schema/context"`，约束路径为` xsi:schemaLocation="http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"`，此时把外部的配置文件加载到spring配置文件中即可。

properties配置文件的编写非常简单，示例如下：

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
```

#### Spring注解

Spring的注解代替了xml配置文件，可以简化配置提高开发效率。

##### 原始注解

Spring的原始注解主要替代了Bean的配置，原始注解如下：

|      注解      |                        描述                        |
| :------------: | :------------------------------------------------: |
|   @Component   |              用在类上，用来实例化Bean              |
|  @Controller   |          用在Web层的类上，用来实例化Bean           |
|    @Service    |        用在Service层的类上。用来实例化Bean         |
|  @Repository   |          用在Dao层的类上，用来实例化Bean           |
|  @Autowirted   |          用在字段上，用来根据类型依赖注入          |
|   @Qualifier   | 结合@Autowirted一起使用，用来根据名称进行依赖注入  |
|   @Resource    | 相当于@Autowirted+@Qualifier，用来按照名称进行注入 |
|     @Value     |                    注入普通属性                    |
|     @Scope     |               标注一个Bean的作用范围               |
| @PostConstruct |   使用在方法上，用来标注该方法是Bean的初始化方法   |
|  @PreDestory   |    使用在方法上，用来标注该方法是Bean的销毁方法    |

上面这些注解前四个是用来实例化一个Bean的对象的，再向后的三个是用来注入引用类型的依赖，@Value是注入普通类型的依赖。

**注意**：当使用注解替代spring配置文件时，必须在配置文件中配置注解的组件扫描，这时这些注解才会起作用。组件扫描的作用是指定哪个包及子包下的Bean需要进行扫描以便识别使用注解配置的类、字段和方法。组件扫描的配置如下：

```xml
<!--  配置组件扫描  -->
<context:component-scan base-package="com.jclight"/>
```

替代示例如下：

```java
//<bean id="userDao" class="com.jclight.dao.impl.UserDaoImpl"/>
@Component("userDao")
public class UserDaoImpl implements UserDao {
    public void save() {
        System.out.println("UserDao-save-UserDaoImpl...");
    }
}
```

```java
//<bean id="userService" class="com.jclight.service.impl.UserServiceImpl"/>
@Component("userService")
public class UserServiceImpl implements UserService {

    //<property name="userDao" ref="userDao"/>
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;

    public void save() {
        userDao.save();
    }
}
```

@Autowired是配置自动注入，下面的@Qualifier("")是配置引用依赖注入。注意：如果使用的是Spring配置文件进行注入必须要有set方法或构造函数，若使用注解则可以省略。当直接使用@Autowired进行依赖注入时，它是按照数据类型从spring容器中进行匹配，此时可以不用写@Qualifier。但这种注入方式仅仅限于spring容器中只有一个该数据类型的bean，若有超过一个以上的该类型的bean这种会出现异常，此时使用@Autowired+@Qualifier注入即可。@Qualifier是按照id值从spring容器中进行匹配的。除此之外，我们还可以将注入改为`@Resource(name = "userDao")`此时这段代码代码相当于@Autowired+@Qualifier。

##### 新注解

使用原始注解不能完全替代xml配置文件，例如：非自定义的Bean的配置（如数据库连接池等）、加载properties文件的配置、组件扫描的配置、引入其他文件的配置。这时使用新注解就可以解决这些问题。具体如下：

|     注解名      |                             描述                             |
| :-------------: | :----------------------------------------------------------: |
| @Configuration  | 用来指定当前类是一个Spring配置类，当创建容器时会从该类上加载注解 |
| @ComponentScan  | 用来指定Spring在初始化容器时需要扫描的包，作用和Spring的xml配置文件中的扫描器配置一样 |
|      @Bean      |     使用在方法上，标注将该方法的返回值存储在Spring容器中     |
| @PropertySource |                  用来加载properties配置文件                  |
|     @Import     |                      用来导入其他配置类                      |

这些注解的使用方式如下：

```java
@Configuration
@ComponentScan("com.jclight")
public class SpringConfiguration {
}
```

使用该注解时，所创建的配置类应该在config包下，它的作用是标志该类是Spring的核心配置类。此外因为我们使用了注解所以此时必须配置组件扫描，这时这个`@ComponentScan("com.jclight")`就可以配置组件扫描。这时对配置类的配置就基本完成了，但是在Spring配置文件中还存在数据库连接池的配置，所以此时我们在配置类中创建一个方法，并对该方法进行配置即可。代码如下：

```java
@Configuration
@ComponentScan("com.jclight")
public class SpringConfiguration {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("jdbc.password")
    private String password;
    
    @Bean("dataSource")
    public DataSource getDataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);   
        dataSource.setUser(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```

这里通过注解`@Bean("……")`Spring会将当前方法的返回值以指定名称存储到Spring容器中。注解`@Value`可以使用SpEL表达式来进行传值。而注解`@Import`的使用更为简单，通常我们会有一个主配置文件很多个其他的配置文件，我们在主配置文件中导入其他的配置文件，而其他的配置文件主要用来配置其他的参数。例如主配置文件中导入数据源的配置文件，数据源的配置文件用来配置数据源，那么此时我们的主配置文件只需要在类的上面加上`@Import(DataSourceConfiguration.class)`，如果配置文件很多导入方式可以为`@Import({DataSourceConfiguration.class,XXX.class})`。

当我们对配置文件中的所有的配置都放在了类中进行配置时，关于Spring的配置文件就可以不要了，此时在使用Spring提供的API时，就需要修改一下，具体修改如下：

```java
//ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
UserService userService = app.getBean(UserService.class);
userService.save();
```

通过这种方式就可以不再需要配置文件，而直接使用Spring的注解进行全注解开发。

#### Spring集成Junit

在Spring中如果要测试一段代码时，首先必须获取应用上下文对象（配置文件、配置类等），获取完成后要利用该上下文对象获取Spring的Bean，此时才能测试我们想要测试的内容，当我们需要获取多个Bean时，这种方式就显得非常麻烦，所以Spring中提供了集成Junit的方案，首先需要让SpringJunit负责创建Spring容器，但是需要将配置文件的名称传递过去，其次将需要进行测试的Bean直接在测试类中进行注入。步骤如下：

+ 导入Spring集成Junit的坐标
+ 使用@Runwith替换原来的运行期（Junit被Spring---->Junit替换）
+ 使用@ContextConfiguration指定配置文件或配置类
+ 使用@Autowired注入需要测试的对象
+ 创建测试方法进行测试

代码实现如下：

pom.xml导入坐标

```xml
<!--    集成测试    -->
<dependency>     
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
```

创建一个测试类进行配置

```java
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {
    @Autowired
    private UserService userService;
    @Autowired
    private DataSource dataSource;

    @Test
    public void test() {
        userService.save();
        System.out.println(dataSource);
    }
}
```

注意：这里导入配置文件有两种方式，一种是导入配置文件，另一种是导入配置类。