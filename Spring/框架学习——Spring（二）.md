## 框架学习——Spring（二）

### Spring AOP

#### 简介

AOP是面向切面编程，它是通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。通过Spring我们不再需要手写动态代理的代码，一样可以使得程序功能的增强。AOP是OOP的延续，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性和开发效率。它的作用是在程序运行期间，在不修改源码的情况下对方法进行功能的增强。优点在于减少了重复的代码，提高开发效率，便于维护。所以Spring AOP的底层实现就是使用的动态代理的技术实现的，在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，再去调用目标对象的方法，从而完成功能的增强。

#### AOP的动态代理技术

+ JDK代理：基于接口的动态代理技术，缺点：目标对象必须要有接口。

  流程：目标对象首先去找该目标对象的目标接口，在运行期间目标接口会动态的生成一个代理对象，此时代理对象是基于目标接口的。那么代理对象所有的方法和目标对象所有的方法是相同的，所以在动态代理的过程中，返回值类型必须是接口，也就是它的父类。

  手写动态代理代码如下：

  **目标接口**

  ```java
  public interface TargetInterface {
      void save();
  }
  ```

  **目标对象**

  ```java
  public class Target implements TargetInterface{
      public void save() {
          System.out.println("save running...");
      }
  }
  ```

  **目标增强对象**

  ```java
  public class Advice {
      public void beforeReturning() {
          System.out.println("前置增强...");
      }
  
      public void afterReturning() {
          System.out.println("后置增强...");
      }
  }
  ```

  **动态代理实现**

  ```java
  public class ProxyTest {
      public static void main(String[] args) {
          //目标对象
          final Target target = new Target();
          //增强对象
          final Advice advice = new Advice();
          TargetInterface proxy = (TargetInterface)             Proxy.newProxyInstance(target.getClass().getClassLoader(),                                    target.getClass().getInterfaces(), new InvocationHandler() {
              public Object invoke(Object proxy, Method method, Object[]                                 args) throws Throwable {
                  //前置增强
                  advice.beforeReturning();
                  //执行目标方法
                  Object invoke = method.invoke(target, args);
                  //后置增强
                  advice.afterReturning();
                  return invoke;
              }
          }
                                                                                      );
          //调用代理对象的方法
          proxy.save();
      }
  }
  ```

  

+ cglib代理：基于父类的动态代理技术（它是第三方库不是JDK中自带的动态代理）

  流程：目标对象在运行期间会直接动态的生成一个代理对象，省去了通过目标接口生成代理对象的步骤。

  手写动态代理代码如下：

  **目标对象**

  ```java
  public class Target {
      public void save() {
          System.out.println("save running...");
      }
  }
  ```

  **目标增强对象**

  ```java
  public class Advice {
      public void beforeReturning() {
          System.out.println("前置增强...");
      }
  
      public void afterReturning() {
          System.out.println("后置增强...");
      }
  }
  ```

  **动态代理实现**

  ```java
  public class ProxyTest {
      public static void main(String[] args) {
          //目标对象
          final Target target = new Target();
          //增强对象
          final Advice advice = new Advice();
          Enhancer enhancer = new Enhancer();
          enhancer.setSuperclass(Target.class);
          enhancer.setCallback(new MethodInterceptor() {
              public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                  //执行前置
                  advice.beforeReturning();
                  //执行目标
                  Object invoke = method.invoke(target, args);
                  //执行后置
                  advice.afterReturning();
                  return invoke;
              }
          });
          Target proxy = (Target) enhancer.create();
          proxy.save();    
      }
  }
  ```

以上就是比较常见的动态代理，在Spring中通过AOP就可以实现手写动态代理的代码，只需要通过配置就可以完成上述繁琐的过程。

#### Spring AOP 术语解析

Spring AOP的实现底层就是对动态代理的代码进行了封装，封装之后我们只需要对需要关注的部分进行代码编写，并通过配置的方式完成指定目标的方法的增强。

在Spring AOP中有一些常用的术语如下：

|   术语    |                             解释                             |   翻译    |
| :-------: | :----------------------------------------------------------: | :-------: |
|  Target   |                        代理的目标对象                        | 目标对象  |
|   Proxy   |          一个类被AOP增强后，就会产生一个结果代理类           |   代理    |
| Joinpoint | 被拦截到的点，在Spring中这些点指的是方法，因为Spring只支持方法类型的连接点（简而言之可以被增强的方法就是连接点） |  连接点   |
| Pointcut  | 对哪些连接点进行拦截的定义（简而言之切入点就是进行了配置的可以被增强的方法） |  切入点   |
|  Advice   |                 拦截到连接点后要进行做的事情                 | 通知/增强 |
|  Aspect   |       切入点和通知的结合（或者目标方法+增强就是切面）        |   切面    |
|  Weaving  | 把增强应用到目标对象来创建新的代理对象的过程。Spring采用动态代理织入，而AspectJ采用编译期织入和类装载织入。 |   织入    |

#### AOP开发注意事项

1. **要编写的内容**

   + 编写核心业务代码（目标类的目标方法（被增强的方法/切入点））
   + 编写切面类，切面类中含有通知（增强功能方法）
   + 在配置文件中，配置织入关系（将哪些通知与哪些连接点进行结合）

2. **AOP技术实现的内容**

   Spring框架监控切入点方法的执行。一旦监控到切入点方法被运行，则使用代理机制，动态创建目标对象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

3. **AOP底层的代理方式**

   在Spring中框架会根据目标类别是否实现了接口来决定采用哪种代理方式，这个代理方式主要有两种，一种是基于JDK的代理，一种是基于cglib的代理。

#### 基于XML的AOP开发

##### 基本步骤

1. 导入AOP的相关坐标

   ```xml
   <!--    Spring的基本包坐标    -->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.0.5.RELEASE</version>
   </dependency>
   <!--    SpringAOP的坐标    -->
   <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjweaver</artifactId>     
       <version>1.8.4</version>
   </dependency>
   ```

2. 创建目标接口和目标类（内部含有切入点）

   ```java
   public class Target implements TargetInterface {
       public void save() {
           System.out.println("save running...");
       }
   }
   ```

3. 创建切面类（内部含有增强方法）

   ```java
   public class MyAspect {
       public void beforeEnhance(){
           System.out.println("前置增强...");
       }
   }
   ```

4. 将目标类和切面类的对象创建权交由Spring

   ```xml
   <!--  目标对象  -->
   <bean id="target" class="com.jclight.aop.Target"/>
   <!--  切面对象  -->
   <bean id="myAspect" class="com.jclight.aop.MyAspect"/>
   ```

5. 在`applicationContext.xml`中配置织入关系

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"
   >
   	<!--  目标对象  -->
   	<bean id="target" class="com.jclight.aop.Target"/>
   	<!--  切面对象  -->
   	<bean id="myAspect" class="com.jclight.aop.MyAspect"/>
       <!--  配置织入  -->
       <aop:config>
           <!--   声明切面     -->
           <aop:aspect ref="myAspect">            
               <aop:before method="before" pointcut="execution(public void com.jclight.aop.Target.save())"/>
           </aop:aspect>
       </aop:config>
   </beans>
   ```

6. 测试

   这里的测试采用Spring提供的集成测试，测试类代码如下：

   ```java
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration("classpath:applicationContext.xml")
   public class AopTest {
       @Autowired
       private TargetInterface target;
   
       @Test
       public void test1() {
           target.save();
       }
   }
   ```

##### 切点表达式

切点表达式例如`<aop:before method="before" pointcut="execution(public void com.jclight.aop.Target.save())"/>`这里的`pointcut="execution(public void com.jclight.aop.Target.save())"`就是切点表达式。通过一个切点表达式我们可以指定多个方法。

切点表达式的语法格式`execution([修饰符] 返回值类型 包名.类名.方法名(参数))`，这里的访问修饰符可以省略；返回值类型、包名、类名、方法名可以用星号`*`替代，表示任意，所以这时就实现了一个表达式指定多个方法；包名与类名之间的一个点`.`代表当前包下的类，两个点`..`代表当前包及其子包下的类；参数列表可以使用两个点`..`表示任意个数，任意类型的参数列表。

##### 通知类型

通知的语法格式`<aop:通知类型 method="切面类中的方法名" pointcut="切点表达式"></aop:通知类型>`，通知类型如下：

|             通知类型             |                        描述                        |
| :------------------------------: | :------------------------------------------------: |
|     前置通知 `<aop:before>`      |    配置前置通知，指定增强方法在切点方法之前执行    |
| 后置通知 `<aop:after-returning>` |    配置后置通知，指定增强方法在切点方法之后执行    |
|     环绕通知 `<aop:around>`      | 配置环绕通知，指定增强方法在切点方法之前之后都执行 |
|  异常抛出通知 `<aop:throwing>`   |   配置异常抛出通知，指定增强方法在出现异常时执行   |
|      最终通知 `<aop:after>`      |  配置最终通知，无论增强方式执行是否有异常都会执行  |

##### 切点表达式的抽取

若在配置中多个切点表达式相同时，可以将切点表达式进行抽取，在增强中使用`pointcut-ref`属性代替`pointcut`属性来引用抽取后的切点表达式。例如：

```xml
<aop:pointcut id="myPointcut" expression="execution(* com.jclight.aop.*.*(..))"/>
<aop:around method="around" pointcut-ref="myPointcut"/>
<aop:after method="after" pointcut-ref="myPointcut"/>
```

#### 基于注解的AOP开发

##### 基本步骤

1. 创建目标接口目标类（内部含有切入点）

   **目标接口**

   ```java
   public interface TargetInterface {
       void save();
   }
   ```

   **目标类**

   ```java
   public class Target implements TargetInterface {
       public void save() {
           System.out.println("save running...");
       }
   }
   ```

2. 创建切面类（内部含有增强方法）

   ```java
   public class MyAspect {
       public void before() {
           System.out.println("前置增强...");
       }
   
       public void afterReturning() {
           System.out.println("后置增强...");
       }
       public Object around(ProceedingJoinPoint pjp) throws Throwable {
           System.out.println("环绕前增强...");
           //切点方法（目标方法）
           Object proceed = pjp.proceed();
           System.out.println("环绕后增强...");
           return proceed;
       }
   
       public void afterThrowing() {
           System.out.println("异常抛出增强...");
       }
   
       public void after() {
           System.out.println("最终增强...");
       }
   }
   ```

3. 将目标类和切面类的对象创建交由Spring完成

   **目标类**

   ```java
   @Component("target")
   public class Target implements TargetInterface {
       public void save() {
           System.out.println("save running...");
       }
   }
   ```

   **切面类**

   ```java
   @Component("myAspect")
   public class MyAspect {
       public void before() {
           System.out.println("前置增强...");
       }
   
       public void afterReturning() {
           System.out.println("后置增强...");
       }
   
       
       public Object around(ProceedingJoinPoint pjp) throws Throwable {
           System.out.println("环绕前增强...");
           //切点方法（目标方法）
           Object proceed = pjp.proceed();
           System.out.println("环绕后增强...");
           return proceed;
       }
   
       public void afterThrowing() {
           System.out.println("异常抛出增强...");
       }
   
       public void after() {
           System.out.println("最终增强...");
       }
   }
   ```

4. 在切面类中使用注解配置织入关系

   ```java
   @Component("myAspect")
   @Aspect
   public class MyAspect {
   
       @Before("execution(* com.jclight.anno.*.*(..))")
       public void before() {
           System.out.println("前置增强...");
       }
   
       public void afterReturning() {
           System.out.println("后置增强...");
       }
   
       public Object around(ProceedingJoinPoint pjp) throws Throwable {
           System.out.println("环绕前增强...");
           //切点方法（目标方法）
           Object proceed = pjp.proceed();
           System.out.println("环绕后增强...");
           return proceed;
       }
   
       public void afterThrowing() {
           System.out.println("异常抛出增强...");
       }
   
       public void after() {
           System.out.println("最终增强...");
       }
   }
   ```

5. 在配置文件中开启组件扫描和AOP自动代理

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
          http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd
   ">
       <!--  组件扫描  -->
       <context:component-scan base-package="com.jclight.anno"/>
       <!--  AOP自动代理  -->
       <aop:aspectj-autoproxy/>
   </beans>
   ```

6. 测试

##### 注解增强类型

|             注解              |                        描述                        |
| :---------------------------: | :------------------------------------------------: |
|      前置增强 `@Before `      |    配置前置增强，指定增强方法在切点方法之前执行    |
|  后置增强 `@AfterReturning`   |    配置后置通知，指定增强方法在切点方法之后执行    |
|      环绕增强 `@Around`       | 配置环绕通知，指定增强方法在切点方法之前之后都执行 |
| 异常抛出增强 `@AfterThrowing` |   配置异常抛出通知，指定增强方法在出现异常时执行   |
|       最终通知 `@After`       |  配置最终通知，无论增强方式执行是否有异常都会执行  |

##### 切点表达式的抽取

若在配置中多个切点表达式相同时，可以将切点表达式进行抽取，在切面中定义一个方法，在该方法上使用`@Pointcut`注解定义切点表达式，然后在增强注解中进行引用 。例如：

```java
@Component("myAspect")
@Aspect
public class MyAspect {
    @Around("pointCut()")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕前增强...");
        //切点方法（目标方法）
        Object proceed = pjp.proceed();
        System.out.println("环绕后增强...");
        return proceed;
    }
    @After("MyAspect.pointCut()")
    public void after() {
        System.out.println("最终增强...");
    }
    /**
     * 定义切点表达式
     */
    @Pointcut("execution(* com.jclight.anno.*.*(..))")
    public void pointCut() {
    }
}
```

### Spring JdbcTemplate

它是spring框架中提供的一个对象，是对原始繁琐的Jdbc API对象的简单封装。Spring框架为我们提供了很多的操作模板类。如：操作关系型数据库的JdbcTemplate和HibernateTemplate，操作nosql数据库的RedisTemplate，操作消息队列的JmsTemplate等。

#### 基本步骤

1. 导入坐标（spring-jdbc和spring-tx）

   ```xml
   <!--   Spring JdbcTemplate基本包     -->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>5.0.5.RELEASE</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-tx</artifactId>
       <version>5.0.5.RELEASE</version>
   </dependency>
   ```

2. 创建数据库表和实体

   **SQL文件**

   ```sql
   SET FOREIGN_KEY_CHECKS=0;
   
   -- ----------------------------
   -- Table structure for staff
   -- ----------------------------
   DROP TABLE IF EXISTS `staff`;
   CREATE TABLE `staff` (
     `id` int(10) unsigned NOT NULL auto_increment,
     `name` varchar(25) NOT NULL,
     `sex` varchar(45) NOT NULL,
     `age` int(10) unsigned NOT NULL,
     PRIMARY KEY  (`id`)
   ) ENGINE=InnoDB DEFAULT CHARSET=gb2312;
   
   -- ----------------------------
   -- Records of staff
   -- ----------------------------
   INSERT INTO `staff` VALUES ('1', '张三', '男', '12');
   INSERT INTO `staff` VALUES ('2', '王五', '男', '22');
   INSERT INTO `staff` VALUES ('3', '马六', '女', '15');
   ```

   **实体对象**

   ```java
   public class Staff {
   
       private String name, sex;
       private int age;
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public String getSex() {
           return sex;
       }
   
       public void setSex(String sex) {
           this.sex = sex;
       }
   
       public int getAge() {
           return age;
       }
   
       public void setAge(int age) {
           this.age = age;
       }
   
       @Override
       public String toString() {
           return "Staff{" +
                   "name='" + name + '\'' +
                   ", sex='" + sex + '\'' +
                   ", age=" + age +
                   '}';
       }
   }
   ```

3. 创建JdbcTemplate对象

   ```java
   @Test
   public void test1() throws PropertyVetoException {
       ComboPooledDataSource dataSource = new ComboPooledDataSource();
       JdbcTemplate jdbcTemplate = new JdbcTemplate();
       //设置数据源对象
       jdbcTemplate.setDataSource(dataSource);
   }
   ```

4. 执行数据库操作

   ```java
   @Test
   public void test1() throws PropertyVetoException {
       //创建数据源对象
       ComboPooledDataSource dataSource = new ComboPooledDataSource();
       dataSource.setDriverClass("com.mysql.jdbc.Driver");
       dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
       dataSource.setUser("root");
       dataSource.setPassword("root");
       JdbcTemplate jdbcTemplate = new JdbcTemplate();
       //设置数据源对象
       jdbcTemplate.setDataSource(dataSource);
       //执行操作
       int row = jdbcTemplate.update("insert into staff value (?,?,?,?)", 4,"李四", "男", 22);
       System.out.println(row);
   }
   ```

#### 通过Spring来创建JdbcTemplate

##### 方法一

Spring配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--  配置数据源对象  -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>
    <!--  配置JdbcTemplate对象  -->
    <bean id="jdbcTemple" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

测试类如下：

```java
@Test
public void test2() {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);
    int row = jdbcTemplate.update("insert into staff value (?,?,?,?)", 5, "赵四", "男", 22);
    System.out.println(row);
}
```

##### 方法二

jdbc.properties文件配置如下：

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
```

Spring配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
    <!--  加载jdbc.properties  -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--  配置数据源对象  -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--  配置JdbcTemplate对象  -->
    <bean id="jdbcTemple" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

测试类如下：

```java
@Test
public void test2() {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);
    int row = jdbcTemplate.update("insert into staff value (?,?,?,?)", 6, "李丽", "男", 22);
    System.out.println(row);
}
```

##### 方法三

和方法二相同这里采用集成测试来写。

Spring配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
    <!--  加载jdbc.properties  -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--  配置数据源对象  -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--  配置JdbcTemplate对象  -->
    <bean id="jdbcTemple" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

测试类如下：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateCRUDTest {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void testUpdate() {
        jdbcTemplate.update("update staff set sex=? where name=?", "女", "张三");
    }
}
```

这里的三个方法每个方法都对前一个方法进行了优化，当然还可以继续推进。例如（将配置文件写成配置类进行全注解开发等）。

#### Spring JdbcTemplate的查询操作

##### 查询全部

与数据库所对应的对象实体类如下：

```java
public class Staff {
    private int id, age;
    private String name, sex;
    
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }
    public void setSex(String sex) {
        this.sex = sex;
    }

    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Staff{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                '}';
    }
}
```

测试类如下：

```java
@Test
public void testQueryAll() {
    List<Staff> staffList = jdbcTemplate.query("select * from staff",                                               new BeanPropertyRowMapper<Staff>(Staff.class));
    System.out.println(staffList);
}
```

这里的测试类与前面一样使用了Spring的集成测试。

##### 查询单个对象

测试类如下：

```java
@Test
public void testQueryOne() {
    Staff staff = jdbcTemplate.queryForObject("select * from staff where id=?",                                              new BeanPropertyRowMapper<Staff>(Staff.class), 3);
    System.out.println(staff);
}
```

##### 聚合查询

测试类如下：

```java
@Test
public void testQueryRowCount() {
    Integer rows = jdbcTemplate.queryForObject("select count(*) from staff", Integer.class);
    System.out.println(rows);
}
```

这三个不同的查询其实主要还在于JdbcTemplate中方法参数的理解，当我们需要查询多个数据的时候可以使用前两种，将其封装在一个集合中供我们使用，当查询一个时就可以用比较简单的第三种查询方法，通过返回值来进行使用。

### Spring 中的事务控制

#### 编程式事务控制

所谓编程式事务控制就是用一些相关的API来进行事务处理的操作，需要通过我们手写代码来实现，同时它也是Spring声明式事务控制的底层实现。

##### `PlatformTransactionManager`

`PlatformTransactionManager`接口是Spring的事务管理器，它里面提供了我们常用的操作事务的方法。

|                            方法名                            |        描述        |
| :----------------------------------------------------------: | :----------------: |
| `TransactionStatus getTransaction(TransactionDefination defination)` | 获取事务的状态信息 |
|           `void commit(TransactionStatus status)`            |      提交事务      |
|          `void rollback(TransactionStatus status)`           |      回滚事务      |

**注意**：`PlatformTransactionManager`是一个接口所以在使用的时候需要有实现类，但是在不同的Dao层技术中有不同的实现。所以这里就需要进行配置，使得Spring能知道所使用的是哪种Dao层的框架。

##### `TransactionDefinition`

`TransactionDefinition`是事务的定义信息对象，**常用方法**如下：

|             方法名             |        描述        |
| :----------------------------: | :----------------: |
|   `int getIsolationLevel()`    |  获得事务隔离级别  |
| `int getPropogationBehavior()` | 获得事务的传播行为 |
|       `int getTimeout()`       |    获得超时时间    |
|     `boolean isReadOnly()`     |      是否只读      |

设置隔离级别可以解决事务并发产生的问题，如脏读、不可重复读和虚读。

|       隔离级别（参数）       |           描述           |
| :--------------------------: | :----------------------: |
|     `ISOLATION_DEFAULT`      |           默认           |
| `ISOLATION_READ_UNCOMMITTED` |       读不可提交的       |
|  `ISOLATION_READ_COMMITTED`  |        读可提交的        |
| `ISOLATION_REPEATABLE_READ`  |         可重复读         |
|   `ISOLATION_SERIALIZABLE`   | 上述问题都能解决但性能差 |

事务的传播行为的作用主要是解决业务方法在调用业务方法时他们间的事务统一性的问题。

| 事务的传播行为（参数） |                             描述                             |
| :--------------------: | :----------------------------------------------------------: |
|       `REQUIRED`       | 若当前没有事务，就新建一个事务，若已经存在一个事务中，加入到这个事务中(默认) |
|       `SUPPORTS`       |  支持当前事务，若当前没有事务，就以非事务方式执行(没有事务)  |
|      `MANDATORY`       |          使用当前的事务，若当前没有事务，就抛出异常          |
|     `REQUERS_NEW`      |           新建事务，若当前在事务中，把当前事务挂起           |
|    `NOT_SUPPORTED`     |    以非事务方式执行操作，若当前存在事务，就把当前事务挂起    |
|        `NEVER`         |          以非事务方式运行，若当前存在事务，抛出异常          |
|        `NESTED`        | 若当前存在事务，则在嵌套的事务内执行。若当前没有事务，则执行`REQUIRED`类似的操作 |
|        超时时间        |      默认值为-1，没有超时限制。若有，以秒为单位进行设置      |
|        是否只读        |                     建议查询时设置为只读                     |

##### `TransactionStatus`

`TranscationStatus`接口提供的是事务具体的运行状态，常用方法如下：

|            方法名            |      说明      |
| :--------------------------: | :------------: |
|   `boolean hasSavepoint()`   | 是否存储回滚点 |
|   `boolean isCompleted()`    |  事务是否完成  |
| `boolean isNewTransaction()` |  是否是新事务  |
|  `boolean isRollbackOnly()`  |  事务是否回滚  |

在进行Spring配置的过程中`TransactionStatus`不需要我们去进行配置，因为它是在事务处理过程中与事务进行变化的对象，简言之`TranscationStatus`是PlatformTransactionManager和TransactionDefinition的综合，也就是被动的对象，所以不需要我们进行配置。

#### 声明式事务控制

声明式事务控制就是采用声明的方式来处理事务，这里的声明就是在配置文件中声明，用在Spring配置文件中声明式的处理事务来代替代码式的处理事务。它的作用就是事务管理不侵入开发的组件。也就是业务逻辑对象不会意识到正在事务管理之中，因为事务管理是属于系统层面的服务，而不是业务逻辑的一部分，如果想要改变事务管理策划的话，也只需要在定义文件中重新配置即可（也就是Spring中的AOP思想）。此外，在不需要事务管理的时候，只需要在设定文件上修改一下，即可移去事务管理服务，无需改变代码重新编译，这样维护起来极为方便。

##### XML的声明式事务控制

 当我们进行声明式事务控制的时候，需要明确三个问题（①切面？②切入点？③通知/增强？），因为Spring声明式事务控制是基于AOP的思想实现的，所以这里就要采用AOP的方式进行事务控制。一般用到的事务控制最多的就是账户流通问题。

**关于引入事务控制的命名空间配置如下**：

`pom.xml`

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
```

**Spring配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
">
</beans>
```

###### 示例

测试的数据库文件如下：

```sql
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for bankaccount
-- ----------------------------
DROP TABLE IF EXISTS `bankaccount`;
CREATE TABLE `bankaccount` (
  `name` varchar(30) default NULL COMMENT '账户人',
  `deposit` int(30) default NULL COMMENT '存款'
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

-- ----------------------------
-- Records of bankaccount
-- ----------------------------
INSERT INTO `bankaccount` VALUES ('张三', '1500');
INSERT INTO `bankaccount` VALUES ('李四', '5000');
```

controller层代码如下：

```java
public class BankAccountController {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        BankAccountService bankAccountService = app.getBean(BankAccountService.class);
        bankAccountService.transfer("张三", "李四", 500);
    }
}
```

dao层代码如下：

```java
public interface BankAccountDao {

    void out(String outMan, int money);

    void in(String inMan, int money);
}
```

```java
public class BankAccountDaoImpl implements BankAccountDao {
    private JdbcTemplate jdbcTemplate;

    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }


    public void out(String outMan, int money) {
        jdbcTemplate.update("update bankaccount set deposit=deposit-? where name=?", money, outMan);
    }

    public void in(String inMan, int money) {
        jdbcTemplate.update("update bankaccount set deposit=deposit+? where name=?", money, inMan);
    }
}
```

domain层代码如下：

```java
public class BankAccount {
    private String name;
    private int deposit;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getDeposit() {
        return deposit;
    }

    public void setDeposit(int deposit) {
        this.deposit = deposit;
    }
}
```

service层代码如下：

```java
public interface BankAccountService {
    void transfer(String outMan, String inMan, int money);
}
```

```java
public class BankAccountServiceImpl implements BankAccountService {
    private BankAccountDao bankAccountDao;

    public void setBankAccountDao(BankAccountDao bankAccountDao) {
        this.bankAccountDao = bankAccountDao;
    }

    public void transfer(String outMan, String inMan, int money) {
        bankAccountDao.out(outMan, money);
        int i = 1 / 0;
        bankAccountDao.in(inMan, money);
    }
}
```

Spring配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop   http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx  http://www.springframework.org/schema/tx/spring-tx.xsd
">

    <!--  加载jdbc.properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--  配置数据源对象  -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--  配置JdbcTemplate  -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--配置BankAccountDao的依赖注入-->
    <bean id="bandAccountDao" class="com.jclight.dao.impl.BankAccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>
    <!--  事务控制配置  -->
    <!--配置BankAccountService的依赖注入（AOP中的目标对象，该对象内部的方法就是切点）-->
    <bean id="bankAccountService" class="com.jclight.service.impl.BankAccountServiceImpl">
        <property name="bankAccountDao" ref="bandAccountDao"/>
    </bean>
    <!--  配置平台事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!-- 通知（事务通知） -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*"/>
        </tx:attributes>
    </tx:advice>
    <!--  配置事务的AOP织入  -->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.jclight.service.impl.*.*(..))"/>
    </aop:config>

</beans>
```

通过示例代码的运行结果可以看出当我们不使用事务管理时，程序报异常且数据库中的数据会有所改变，当我们使用事务管理时程序报异常但数据库中的数据不会进行改变。这里的`<tx:method name="*"/>`代表切点方法的事务参数的配置，如：`<tx:method name="transfer" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false"/>`，这里的`name`代表切点方法的名称，`isolation`代表事务的隔离级别，`propogation`代表事务的传播行为，`timeout`代表超时时间，`read-only`代表是否只读。

##### 注解的声明式事务控制

关于注解的声明式事务控制，首先将上面的XML文件中的自定义的Bean的配置改成注解，然后对事务管理的通知和织入进行注解配置即可。注意这里必须配置两个关键的部分（①配置组件扫描②配置事务注解驱动）。

###### 示例

Spring配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop   http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx  http://www.springframework.org/schema/tx/spring-tx.xsd
">

    <!--  加载jdbc.properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--  配置组件扫描  -->
    <context:component-scan base-package="com.jclight"/>
    <!--  配置数据源对象  -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--  配置JdbcTemplate  -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--  配置平台事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--  配置事务注解驱动  -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

BankAccountDaoImpl.java文件：

```java
@Repository("bankAccountDao")
public class BankAccountDaoImpl implements BankAccountDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;


    public void out(String outMan, int money) {
        jdbcTemplate.update("update bankaccount set deposit=deposit-? where name=?", money, outMan);
    }

    public void in(String inMan, int money) {
        jdbcTemplate.update("update bankaccount set deposit=deposit+? where name=?", money, inMan);
    }
}
```

BankAccountServiceImpl.java文件：

```java
@Service("bankAccountService")
@Transactional(isolation = Isolation.DEFAULT)
public class BankAccountServiceImpl implements BankAccountService {
    @Autowired
    private BankAccountDao bankAccountDao;

    @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    public void transfer(String outMan, String inMan, int money) {
        bankAccountDao.out(outMan, money);
        bankAccountDao.in(inMan, money);
    }
}
```

**注意**：这里的`@Transactional`配置了两次，使用原则是就近原则哪一个注解离该方法近就使用哪一个。其次还可以在注解后的括号中加入参数，以此来达到想要的事务控制的效果。因为这里是示例没有加入事务回滚的方法或方式，一般而言要加入事务回滚。

###### 要点

1. 使用`@Transactional`在需要进行事务控制的类或是方法上修饰，注解可用的属性同XML配置方式一样。（如：隔离级别、传播行为等）
2. 注解使用在类上，该类下所有方法都使用同一套注解参数配置。（一个`@Transactional`注解）
3. 注解使用在方法上，该方法使用该注解的参数进行配置，不同的方法可以采用不同的事务参数配置。
4. 使用注解控制事务一定要开启事务的注解驱动`<tx:annotation-driven/>`（XML文件中）