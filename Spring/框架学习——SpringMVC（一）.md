## 框架学习——SpringMVC（一）

### SpringMVC

#### 一.概述

SpringMVC是一种基于Java实现的MVC设计模式的请求驱动类型的轻量级Web框架。SpringMVC已经是当前最主流的MVC框架之一，它通过一套注解，让一个简单的Java类成为处理请求的控制器，而无需实现任何接口。同时它还支持RESTful编程风格的请求。

#### 二.开发步骤与C/S间的访问流程

**开发步骤**如下：

1. 导入SpringMVC的坐标

   `pom.xml`

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>5.0.5.RELEASE</version>
   </dependency>
   ```

2. 配置SpringMVC核心控制器`DispathcerServlet`

   `web.xml`

   ```xml
   <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:spring-mvc.xml</param-value>
       </init-param>
       <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

3. 编写Controller类和视图页面

   `UserController.java`

   ```java
   public class UserController {
       public String save() {
           System.out.println("save UserController...");
           return "success.jsp";
       }
   }
   ```

   `success.jsp`

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <h1>Success!</h1>
   </body>
   </html>
   ```

4. 使用注解配置Controller类中的业务方法的映射地址

   `UserController.java`

   ```java
   @Controller
   public class UserController {
       @RequestMapping("/test")
       public String save() {
           System.out.println("save UserController...");
           return "success.jsp";
       }
   }
   ```

5. 配置spring-mvc.xml文件（配置组件扫描）

   `spring-mvc.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
   ">
   
       <!--  Controller的组件扫描  -->
       <context:component-scan base-package="com.jclight.controller"/>
   </beans>
   ```

   **注意**：这里需要将该配置文件通过在web.xml中去加载才可以使用。在开发过程中需要将属于SpringMVC配置的提取出来，让其成为一个单独的spring-mvc.xml配置文件，减少spring主配置文件的篇幅。

6. 执行访问测试

**访问流程**如下：

客户端向Tomcat服务器发送请求信息，这时Tomcat服务器中的Tomcat引擎会接收客户端请求并封装代表请求的request和代表响应的response，此时Tomcat引擎会向Tomcat服务器中的web应用请求调用资源，这时在web应用中的Servlet会存在共有行为和特有行为，这里我们将共有行为进行提取使得调用请求资源只去调用一个共有行为，再通过共有行为调用不同的特有行为，通过不同的特有行为执行不同的动作。简单来讲就是客户端发起请求，服务器端接收请求，执行逻辑并进行视图跳转这几步。

#### 三.组件解析

SpringMVC中含有前端控制器组件（DispatcherServlet），处理器映射器组件（HandlerMapping），处理器适配器组件（HandlerAdaptor），处理器组件（Handler），视图解析器组件（ViewResolver），视图组件（View）。

##### 1.执行流程

浏览器向前端控制器（DispatcherServlet）发出请求，前端控制器向处理器映射器（HandlerMapping）请求查询Handler，处理器映射器向前端控制器返回处理器执行链（HandlerExecutionChain），此时前端控制器向处理器适配器（HandlerAdaptor）请求执行Handler，处理器适配器向处理器（Handler）发出请求，处理器向返回响应给处理器适配器，处理器适配器向前端控制器返回ModelAndView，前端控制器请求视图解析器（ViewResolver），视图解析器向前端控制器返回视图View对象，前端控制器渲染视图页面并向浏览器返回响应信息。

##### 2.注解

|      注解       |                             描述                             |
| :-------------: | :----------------------------------------------------------: |
| @RequestMapping | 用来建立请求URL和处理请求方法之间的对应关系。它的作用位置有两处，①作用在类上，作为请求URL的第一级访问目录，在该处不写就相当于应用的根目录；②作用在方法上，作为请求URL的第二级访问目录，与在类上使用的该注解标注的一级目录一起组成访问虚拟路径 |

`@RequestMapping`参数：`value`：用于指定请求的URL，它和`path`的作用是一样的。`method`：用来指定请求的方式，`method`后面的值是枚举类型的值，所以应该写成`RequestMethod.POST`等方式。`params`：用于指定限制请求参数的条件，支持简单的表达式，要求请求参数的key和value必须和配置的一模一样，例如`params = {"userName"}`，表示请求参数必须有userName。

##### 3.组件扫描

在使用Spring开发中，spring-mvc.xml是针对关于SpringMVC的配置内容，在其中需要的组件扫描也只需要扫描与SpringMVC相关的内容，那么此时Spring的主配置文件中的组件扫描和SpringMVC中的组件扫描就有一部分是重复扫描的，所以此时可以将组件扫描改写一下，让其包括或排除某一部分的内容，示例如下：

```xml
<context:component-scan base-package="com.jclight.controller"/>
```

可改写为：

```xml
<context:component-scan base-package="com.jclight.controller">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

上面的两种功能是相同的，所以我们可以使用这种写法来优化我们的配置文件，使得各配置文件各司其职。

##### 4.XML配置

**关于配置内部资源视图解析器**

之前在Controller层的代码编写中，我们在给定跳转到某个页面的值时的写法为`return /jsp/success.jsp;`，如果我们有很多页面都要进行跳转到/jsp下的.jsp文件的时候，这样的编写看起来不美观且浪费开发时间，所以此时我们可以利用springmvc的配置文件去进行配置，使得最终我们的返回值书写只需要如`return success;`即可，做法如下：

配置文件中的编写如下：

```xml
<!--  配置内部资源视图解析器  -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

上述就是使用`InternalResourceViewResolver`中的set方法进行前后缀的注入，最终达到一个拼接的效果。

此外我们可以在return中指定转发或重定向，例如：转发：`return "forward:/jsp/success.jsp";`，重定向：`return "redirect:/jsp/success.jsp";`，默认为转发。

#### 四.请求与数据响应

SpringMVC中数据响应的方式主要有两种，①页面跳转（返回字符串或通过ModelAndView对象返回），②回写数据（返回字符串或返回对象或集合）。

##### 1.页面跳转——返回字符串

返回字符串的方式的页面跳转例如：

```java
@RequestMapping("/test")
public String success() {
    return "success";
}
```

```xml
<!--  配置内部资源视图解析器  -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

最后这个转发的资源地址就是：/jsp/success.jsp，所以真正的返回值是带有前后缀的字符串，转发`forward:/jsp/success.jsp`，重定向`redirect:/success.jsp`。**注意**：在我们的webapp下有个WEB-INF文件夹，该文件夹是一个受保护的文件夹，外界不能直接访问该文件夹，所以这里的转发和重定向会有所区别。

##### 2.页面跳转——返回ModelAndView

返回ModelAndView方式的页面跳转例如：

```java
 @RequestMapping("/test2")
public ModelAndView returnModelAndView() {
    ModelAndView modelAndView = new ModelAndView();
    //设置模型数据
    modelAndView.addObject("username", "tom");
    //设置视图名称
    modelAndView.setViewName("success");
    return modelAndView;
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>Success! ${username}</h1>
</body>
</html>
```

通过上述的操作，我们可以将一些数据通过ModelAndView的方式传递给前端界面，通过EL表达式取出传递过来的值，并对其使用即可。除此之外，还可以如下做法：

```java
@RequestMapping("/test3")
public ModelAndView returnModelAndView2(ModelAndView modelAndView) {
    modelAndView.addObject("username", "jerry");
    modelAndView.setViewName("success");
    return modelAndView;
}
```

通过这种做法依然可以获取到页面，并在页面上显示出我们想要的值，该做法是利用了SpringMVC会将ModelAndView自动注入，所以这种方式可以省去创建对象的过程。此外，我们还可以使用返回字符串的方式来进行数据的添加，如：

```java
@RequestMapping("/test4")
public String returnModelAndView3(Model model) {
    model.addAttribute("username", "Mary");
    return "success";
}
```

此时就是将ModelAndView拆开，返回的字符串作为视图，需要传递的数据作为Model，这样依然可以在前端页面显示数据。当然还存在一个不常用的方法来进行数据的传递，这个也就是Servlet中的传递数据的方法，比较原始，代码如下：

```java
@RequestMapping("/test5")
public String returnModelAndView4(HttpServletRequest request) {
    request.setAttribute("username", "Mark");
    return "success";
}
```

##### 3.回写数据——返回字符串

当客户端访问服务器端，若想直接回写字符串作为响应体返回，在Servlet中的写法如`response.getWriter().print("...")`，在SpringMVC中的写法如下：

```java
@RequestMapping("/test6")
public void writeBackStr(HttpServletResponse response) throws IOException {
    response.setCharacterEncoding("GBK");
    response.getWriter().println("回写字符串");
}
```

此外，我们还可以使用注解的方式进行回写，例如：

```java
@RequestMapping("/test7")
@ResponseBody
public String writeBackStr2() {
    return "writeBack";
}
```

注解`@ResponseBody`的作用就是告诉SpringMVC框架，不进行视图跳转，进行的是数据响应。除了返回一段话之外，还可以返回一些特定格式的字符串，例如：

```java
@RequestMapping("/test8")
@ResponseBody
public String writeBackStr3() throws JsonProcessingException {
    User user = new User();
    user.setName("TOM");
    user.setAge(25);
    //使用JSON转换工具，将对象转换程JSON格式的字符串再返回
    ObjectMapper objectMapper = new ObjectMapper();
    return objectMapper.writeValueAsString(user);
}
```

上述就是利用JSON格式转换工具，返回一个JSON格式的字符串，使用该工具之前需要导入相关依赖，相关依赖如下：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.0</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.0</version>
</dependency>
```

##### 4.回写数据——返回对象或集合

如果想要回写返回的对象或集合时，且对象或集合回写的格式同JSON一样，我们需要在配置文件中进行处理器映射器的配置，当配置好了后，就可以将我们需要的对象或集合以JSON串的形式返回给前端页面。具体做法如下：

```java
@RequestMapping("/test9")
@ResponseBody
public User returnObjAndGather1() {
    User user = new User();
    user.setName("TOM");
    user.setAge(25);
    return user;
}
```

配置文件如下：

```xml
<!--  配置处理器映射器  -->
<bean id="handlerAdapter"          class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <list>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
        </list>
    </property>
</bean>
```

此时SpringMVC就能对我们返回的对象或集合进行JSON串的自动转换，并返回给前端界面进行展示。但是，在Spring框架中这样的配置显得比较繁琐，所以这里SpringMVC提供了一套注解驱动，使用它就能完成上述的配置。做法如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc  http://www.springframework.org/schema/mvc/spring-mvc.xsd
">
    <context:component-scan base-package="com.jclight.controller"/>
    <!--  MVC的注解驱动  -->
    <mvc:annotation-driven/>
</beans>
```

在SpringMVC中有三大组件（处理器映射器、处理器适配器、视图解析器），使用MVC注解驱动会自动加载处理器映射器（RequestMappingHandlerMapping）和处理器适配器（RequestMappingHandlerAdapter），所以可以使用配置注解驱动来代替处理器和适配器的配置，同时该配置默认底层集成了Jackson进行对象或集合的JSON格式字符串的转换，比较方便。

#### 五.获得请求数据

客户端请求参数的格式为：`name=value&name=value...`，有时服务器端还要对获取的请求参数进行封装，在使用Servlet进行处理时要比SpringMVC复杂，SpringMVC可以接收四种类型的参数（基本类型参数、POJO（简单JavaBean）类型参数、数组类型参数、集合类型参数），以便对参数进行封装，具体如下：

##### 1.基本类型参数

要获得基本类型的参数并使用，必须在Controller中的业务方法的参数名称与请求参数的名称一致，这时参数值才会自动映射匹配。示例如下：

```java
@RequestMapping("/test12")
@ResponseBody
public void getBasicTypeRequestParameter1(String username, int age) {
    System.out.println(username + ":" + age);
}
```

此时它的请求URL为`http://localhost:8898/user/test12?username=tom&age=10`，这时控制台会打印`tom:10`。注意：这里的`@ResponseBody`注解依然要加上，此时代表该方法不进行跳转由于返回值类型是void所以也不进行数据回写。在前端页面返回的请求参数的类型都是String类型的，这里SpringMVC框架会帮助我们进行强转，所以这里的age不必再进行手动的数据类型转换了。

##### 2.POJO类型参数

有时我们会需要一些Bean对象的数据，这时我们可以使用SpringMVC帮助我们将前端获得的参数，自动封装成一个Bean对象，便于我们使用。具体代码如下：

```java
@RequestMapping("/test13")
@ResponseBody
public void getPojoTypeRequestParameter1(User user) {
    System.out.println(user);
}
```

此时它的请求URL为`http://localhost:8898/user/test13?username=tom&age=30`，这时我们在控制台就能看到User对象中包含的数据。（注意：这里必须要覆写一个toString方法否则不能看出来数据是否进行了传递）

##### 3.数组类型参数

要获得数组类型的参数，必须在Controller中的方法中的数组名称与请求的参数的名称一致，这样参数值才能自动映射匹配。具体如下：

```java
@RequestMapping("/test14")
@ResponseBody
public void getArrayTypeRequestParameter1(String[] names) {
    System.out.println(Arrays.toString(names));
}
```

此时它的请求URL为`http://localhost:8898/user/test14?names=tom&names=jerry&names=mary`，这时控制台会打印数组的相关数据。注意：这里的`Arrays.toString(names)`与`Arrays.asList(names)`的作用是一样的，都可以显示数组的相关数据。

##### 4.集合类型参数

获得集合参数时，要将集合参数包装到一个POJO中才行。过程为通过表单将多个对象填充到集合中并提交给服务器，服务器将集合封装成VO对象，通过Controller中的方法进行操作，示例如下：

```java
@RequestMapping("/test15")
@ResponseBody
public void getAssembleTypeRequestParameter1(Vo vo) {
    System.out.println(vo);
}
```

**form.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>FORM</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/user/test15" method="post">
    <label>
        <input type="text" name="userList[0].name"><br/>
        <input type="text" name="userList[0].age"><br/>
        <input type="text" name="userList[1].name"><br/>
        <input type="text" name="userList[1].age"><br/>
    </label>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

**Vo.java**

```java
public class Vo {
    private List<User> userList;

    public List<User> getUserList() {
        return userList;
    }

    public void setUserList(List<User> userList) {
        this.userList = userList;
    }

    @Override
    public String toString() {
        return "Vo{" +
                "userList=" + userList +
                '}';
    }
}
```

这样就可以实现获得集合类型的参数了，简单来说就是利用对象将集合封装进去，并使用获得POJO类型参数的方式获得集合。这种方式显然比较麻烦，所以我们可以使用ajax进行提交，指定contentType为JSON形式，在方法参数位置使用`@RequestBody`注解，这时就可以直接接收集合数据。这种方式就省去了POJO包装这一步。示例如下：

```java
@RequestMapping("/test16")
@ResponseBody
public void getAssembleTypeRequestParameter2(@RequestBody List<User> userList) {
    System.out.println(userList);
}
```

**ajax.jsp**

```jsp
<html>
<head>
    <title>AJAX</title>
    <script src="${pageContext.request.contextPath}/js/jquery-3.3.1.js"></script>
    <script>
        let userList = new Array();
        userList.push({name: "Tom", age: 22});
        userList.push({name: "Jerry", age: 33});

        $.ajax({
            type: "POST",
            url: "${pageContext.request.contextPath}/user/test16",
            data: JSON.stringify(userList),
            contentType: "application/json;charset=utf-8"
        });
    </script>
</head>
<body>

</body>
</html>
```

**注意**：当使用这种方式时必须要先做两件事，首先要给项目中引入`jquery-3.3.1.js`文件，然后要开启静态资源访问，否则后端就无法获取到前端的数据。开启静态资源访问只需要在SpringMVC配置文件中加上`<mvc:resources mapping="/js/**" location="/js/"/>`即可，此时控制台就能显示出前端传递过来的集合数据。这里使用到了一个注解`@RequestBody`，使用该注解就可以在不用VO封装的前提下获得集合类型的参数了，使用该注解有严格的步骤，首先客户端发送的是json格式的数据，然后ajax中的contentType指定的是`application/json`格式，此时才能使用该注解将数据放在集合中并进行操作。

关于开启静态资源访问，除了上面的那种方式之外，还可以使用`<mvc:default-servlet-handler/>`配置，该配置是先让SpringMVC容器去找该静态资源，如果找不到就交由原始容器去找，这个原始容器可以是Tomcat等。

##### 5.问题补充

###### 1.乱码问题

当我们对中文进行发送或请求获取时，有时会出现乱码的问题，如果此时在Controller中的方法参数是Servlet的相关API就可以在方法体中使用`response.setCharacterEncoding("GBK");`进行解决，但是这种方式就与框架思想背道而驰了，所以这时我们可以设置一个过滤器来进行编码的过滤，具体做法在web.xml中添加如下代码段即可：

```xml
<!--  配置全局编码过滤器  -->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

此时就可以解决乱码问题了。

###### 2.请求参数名称与业务方法名称不一致问题

当请求参数的名称与Controller的业务方法的参数名称不一致时，就需要通过@RequestParam注解进行显示绑定，做法如下：

```java
@RequestMapping("/test17")
@ResponseBody
public void getBasicTypeRequestParameter2(@RequestParam(value = "name") String username) {
    System.out.println(username);
}
```

此时当我们在客户端的URL上进行请求时参数名称为name也可以成功获取到前端传递过来的数据，它的作用就是将value的参数名称映射到业务方法的参数名称上，所以此时使用name或username都可以获取前端传递过来的值。注解`@RequestParam`有三个参数，具体含义如下：

|    参数名    |                             描述                             |
| :----------: | :----------------------------------------------------------: |
|    value     |                         请求参数名称                         |
|   required   | 指定的请求参数是否必须包括，默认为true，提交时若没有该参数将会报错 |
| defaultValue |           当没有请求参数时，将使用指定的默认值赋值           |

###### 3.自定义类型转换器

在SpringMVC中已经提供了一些常用的类型转换器，比较常用的是将客户端提交的数据（String类型）转换成int型进行参数的设置。但是不是所有的数据类型都提供了转换器，此时我们就需要自定义一个转换器，将客户端传递过来的数据转换成我们需要的数据类型。

**具体流程**

①定义转换器类实现Converter接口

②在配置文件中声明转换器（Spring-MVC.xml）

③在`<annotation-driven>`中引用转换器

**示例**

创建转换器类DateConverter.java

```java
public class DateConverter implements Converter<String, Date> {

    public Date convert(String dateStr) {
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
        Date date = null;
        try {
            date = format.parse(dateStr);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
```

spring-mvc.xml

```xml
<!--  声明转换器  -->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.jclight.converter.DateConverter"/>
        </list>
    </property>
</bean>
<!--  MVC的注解驱动  -->
<mvc:annotation-driven conversion-service="conversionService"/>
```

最后启动服务器进行测试。

##### 6. 获得RestFul风格的参数

RestFul是一种软件架构风格、设计风格，它不是标准，只是提供了一组设计原则和约束条件，主要用于客户端和服务器交互类的软件，基于此风格设计的软件可以更简洁，更有层次，更易于实现缓存机制等。Restful风格的请求是使用`url+请求方式`的类型表示一次请求的目的，在HTTP协议中这四个表示操作方式的参数如（`GET`--用来获取资源，`POST`--用于新建资源，`PUT`--用于更新资源，`DELETE`--用于删除资源），例如：`/user/1 GET`表示获取id为1的user。在实际的应用中，我们需要前后端进行交互，所以此时的`1`需要用占位符进行替换，在业务方法中可以使用注解`@PathVariable`注解进行占位符的匹配获取工作。代码示例如下：

```java
@RequestMapping("/test18/{name}")
@ResponseBody
public void getRestFulStyleData1(@PathVariable(value = "name", required = true) String username) {
    System.out.println(username);
}
```

此时我们在浏览器中输入相应的url即可获取到数据，如果加入一些请求的限定条件，此时可以在注解`@RequestMapping`后，有一个参数method可以指定我们的请求方式是哪种。

##### 7.获得请求头信息

当我们需要获取HTTP中的请求头的信息的时候，不用框架的方法可以用`request.getHeader(name)`方式获得请求头的信息，在SpringMVC中我们可以使用注解`@RequestHeader`获得请求头的信息。该注解的属性有两个（`value`--表示请求头的名称，`required`--是否必须携带此请求头）。示例如下：

```java
@RequestMapping("/test21")
@ResponseBody
public void getRequestHeaderMessage(@RequestHeader(value = "User-Agent", required = false) String userAgent) {
    System.out.println(userAgent);
}
```

此外，还可以使用注解`@CookieValue`获得指定Cookie的值，该注解的属性也有两个（`value`--指定cookie的名称，`required`--是否必须携带此cookie）。示例如下：

 ```java
@RequestMapping("/test22")
@ResponseBody
public void getRequestHeaderCookie(@CookieValue(value = "JSESSIONID", required = false) String jsessionid) {
    System.out.println(jsessionid);
}
 ```

此时就可以在控制台看见我们指定Cookie名称的Cookie值了。

##### 8.文件上传

实现文件上传，客户端必须具备三个要点（①表单项的type类型必须为file。②表单的提交方式必须为post。③表单的enctype属性必须为多部分表单形式即为`enctype="multipart/form-data"`）。它的原理是当form表单修改为多部分表单时，一些如request.getParameter()的API将会失效，此时enctype的取值为`Mutilpart/form-data`，请求的正文内容为多部分形式。而当enctype的取值为`application/x-www-form-urlencoded`，请求的正文内容格式为键值对格式。

**实现单文件上传的步骤**

1. 导入fileupload和io的坐标

   ```xml
   <dependency>
       <groupId>commons-fileupload</groupId>
       <artifactId>commons-fileupload</artifactId>
       <version>1.3.1</version>
   </dependency>
   <dependency>
       <groupId>commons-io</groupId>
       <artifactId>commons-io</artifactId>
       <version>2.4</version>
   </dependency>
   ```

2. 配置文件上传解析器

   ```xml
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
       <!--    上传文件的编码类型    -->
       <property name="defaultEncoding" value="UTF-8"/>
       <!--    上传文件的总大小    -->
       <property name="maxUploadSize" value="5242800"/>
   </bean>
   ```

3. 编写文件上传的代码

   ```java
   @RequestMapping("/test23")
   @ResponseBody
   public void uploadSimpleFile(String username, MultipartFile uploadFile) throws IOException {
       System.out.println(username + ":" + uploadFile);
       //获得上传文件的名称
       String filename = uploadFile.getOriginalFilename();
       uploadFile.transferTo(new File("F:\\资料\\Practice\\java\\" + filename));
   }
   ```

   此时创建一个用来上传文件的jsp文件，代码如下：

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Upload</title>
   </head>
   <body>
   <form action="${pageContext.request.contextPath}/user/test23" method="post" enctype="multipart/form-data">
       <label>
           名称：<input type="text" name="username"><br/>
           文件：<input type="file" name="uploadFile"><br/>
           <input type="submit" value="提交">
       </label>
   </form>
   </body>
   </html>
   ```

   此时启动服务器，访问该jsp文件并填写数据即可成功获取到提交的用户名和上传文件的对象并将该文件放到我们需要的位置。若要实现多文件上传，此时jsp页面上的上传文件的属性name为相同值，在后端服务代码上只需将MultipartFile改为数组形式MultipartFile[]即可。

