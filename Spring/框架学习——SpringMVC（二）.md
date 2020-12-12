## 框架学习——SpringMVC（二）

### SpringMVC 拦截器

#### 作用

SpringMVC的拦截器（interceptor）类似于Servlet中的过滤器Filter，用于对处理器进行预处理和后处理。它与过滤器Filter类似，将拦截器按照一定的顺序联结成一条链，这条链称为拦截器链（interceptor Chain）。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。拦截器的底层也是使用AOP的思想进行实现的。

#### 拦截器和过滤器的区别*

|  区别  |                           使用范围                           |                           拦截范围                           |
| :----: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 过滤器 |   它是Servlet规范中的一部分，任何的Java Web工程都可以使用    |  在url-pattern中配置了/*后，可以对所有要访问的资源进行拦截   |
| 拦截器 | 它是SpringMVC框架中自带的，只有使用了SpringMVC框架的工程才能使用 | 只会拦截访问的控制器方法，如果访问的是jsp、html、css、image或js是不会进行拦截的 |

#### 使用步骤

自定义拦截器的使用步骤有三步，具体如下：

1. 创建拦截器类实现HandlerInterceptor接口

   ```java
   public class MyInterceptor1 implements HandlerInterceptor {
   
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("preHandle...");
           return true;
       }
   
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("postHandle...");
       }
   
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("afterCompletion...");
       }
   }
   ```

   **注意**：preHandle方法的返回值为false时会进行拦截操作，之后的目标方法以及其他的相关方法都不会再执行。这里我们可以对这三个方法进行更进一步的处理，使其满足我们的需求条件。

2. 配置拦截器

   ```xml
   <mvc:interceptors>
       <mvc:interceptor>
           <mvc:mapping path="/**"/>
           <bean class="com.jclight.interceptor.MyInterceptor1"/>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

   **注意**：这里的`<mvc:mapping path="/**"/>`表示对哪些资源执行拦截操作，`/**`代表对所有资源进行拦截操作。

3. 测试拦截器的拦截效果

   编写好Controller层的目标方法后，启动服务器并观察效果。

#### 常用方法

|     方法名      |                             描述                             |
| :-------------: | :----------------------------------------------------------: |
|    preHandle    | 方法将在请求处理之前进行调用，该方法的返回值类型为boolean类型，返回false时，表示请求结束，后续的Interceptor和Controller都不会再执行；当返回值为true时就会继续调用下一个Interceptor的preHandle方法 |
|   postHandle    | 方法是在当前请求进行处理之后被调用，前提是preHandle方法的返回值为true时才会被调用，且它会在DispatcherServlet进行视图返回渲染之前被调用，所以我们可以在这个方法中对Controller处理之后的ModelAndView对象进行操作 |
| afterCompletion | 方法将在整个请求结束之后，也就是在DispatcherServlet渲染了对应的视图之后执行，前提是preHandle方法的返回值为true时才能被调用 |

通过SpringMVC的拦截器我们可以对用户登录进行权限控制，使得系统更加的完善。此外我们还可以对其他有需求的业务方法进行拦截，以达到我们的目的。

### SpringMVC 异常处理机制

系统中异常包括两类：预期异常和运行时异常，预期异常可以通过捕获异常获取异常信息，运行时异常主要通过规范代码开发、测试等手段减少异常的发生。系统的Dao、Service、Controller出现异常都通过throw向上抛出，最后由SpringMVC的前端控制器交由异常处理器进行异常处理，它的执行流程如下：

首先，客户端向前端控制器发送请求，前端控制器向Controller层发送请求，Controller层再请求Service，Service再请求Dao，在这些请求中如果出现了异常就向上抛出，如Dao层出现异常就将异常抛给Service，若Service出现异常将继续向上抛，抛给Controller，最后Controller将异常抛给前端控制器，此时前端控制器将会去找异常处理器（HandlerExceptionResolver）最后由异常处理器进行统一处理。通过MVC框架，我们可以自定义异常处理器，也可以使用MVC自带的异常处理器（SimpleMappingExceptionResolver）。自定义异常处理器可以通过实现Spring的异常处理接口（HandlerExceptionResolver）自定义自己的异常处理器。

#### ①使用简单异常处理器

简单异常处理器在SpringMVC中已经被定义好了，在使用时直接进行配置即可，示例如下：

```xml
<!--配置异常处理器-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="defaultErrorView" value="error"/>
    <property name="exceptionMappings">
        <map>
            <entry key="java.lang.ClassCastException" value="error1"/>
            <entry key="com.jclight.exception.MyException" value="error2"/>
        </map>
    </property>
</bean>
```

这样就可以实现我们的异常处理了，上述的配置中`defaultErrorView`代表默认的异常，若没有配置其他异常的处理方法，该方法就会执行。而`exceptionMappings`代表可以配置多个异常的解决方案，里面是map映射，key对应哪个异常，value对应跳转哪个页面。当着两种都在配置文件中时，出现异常会先看有无某个单独的异常的处理方案，若无则会执行默认异常的处理方案。

#### ②自定义异常处理

使用这种方法的步骤有四步，第一步创建异常处理器类实现HandlerExceptionResolver接口，第二步配置该异常处理器，第三步编写异常页面便于进行不同的异常处理，第四步测试。示例如下：

MyExceptionResolver.java

```java
public class MyExceptionResolver implements HandlerExceptionResolver {
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView modelAndView = new ModelAndView();
        if (e instanceof MyException) {
            modelAndView.addObject("info", "自定义异常");
        } else if (e instanceof ClassCastException) {
            modelAndView.addObject("info", "类转换异常");
        }
        modelAndView.setViewName("error");
        return modelAndView;
    }
}
```

这时将该类在SpringMVC的配置文件中进行配置即可，配置方式就是配置一个该类的Bean。



