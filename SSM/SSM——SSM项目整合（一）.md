## SSM——SSM项目整合（一）

### 原始整合方式

**基本步骤**：

①准备数据库及数据表；②创建Maven工程；③导入Maven坐标；④编写实体类；⑤编写mapper接口；⑥编写service（业务层）接口；⑦编写service接口实现；⑧编写controller；⑨编写或添加页面；⑩编写相应的配置文件；⑪运行测试

**总结**：

实际上原始整合SSM项目就是利用MyBatis的相关API对MyBatis的配置文件、映射文件进行加载从而操作数据库；而Spring与SpringMVC通过`web.xml`进行串联，利用controller层的方法去调用业务层方法，在业务层中通过MyBatis相关API来链接MyBatis的核心配置文件，通过配置文件中的加载映射文件和映射文件相关接口去实现数据库的操作，然后再通过层层返回返回相关数据结果，最后显示在页面中。

**弊端**：

①每次在调用业务层方法时都会加载一次MyBatis核心配置文件且都会创建一个`SqlSessionFactory`。

②每次都需要进行事务的提交和SqlSession的关闭操作。

### 通过Spring整合MyBatis

其实就是将service中的关于MyBatis的API的使用进行抽取，在Spring配置文件中进行配置，以此达到优化代码的目的。做法如下：

Spring配置文件：

```xml
<!--  加载外部资源文件  -->
<context:property-placeholder location="classpath:jdbc.properties"/>
<!--  配置数据源  -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
<!--  配置sessionFactory  -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--加载MyBatis核心文件-->
    <property name="configLocation" value="classpath:SqlMapConfig.xml"/>
</bean>
<!--  扫描mapper所在的包  -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.jclight.mapper"/>
</bean>
```

此时在业务层中使用注解注入mapper对象，然后在不同的业务方法中使用该mapper对象调用相应的方法即可。这样做可以减少MyBatis的核心配置文件的代码量，减少sqlSession工厂对象的创建次数，从而提高系统性能减少资源浪费。但这种只是解决了第一个弊端，还有一个弊端没有解决，所以针对该弊端我们可以使用Spring的事务控制进行处理，具体如下：

Spring配置文件：

```xml
<!--  配置声明式事务控制  -->
<!--平台事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
<!--  配置事务增强  -->
<tx:advice id="txAdvice">
    <tx:attributes>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
<!--  配置事务的AOP织入  -->
<aop:config>
    <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.jclight.service.impl.*.*(..))"/>
</aop:config>
```

此时Spring框架就会帮助我们自动的完成事务控制，当我们进行多表操作时，配置事务控制能有效的避免数据库操作时出现的问题。例如：多表操作出现错误，但数据表中进行了更新操作等问题。

### 补充/技巧

1. **快速开发技巧**：首先根据上述整合的基本步骤进行基础环境搭建，然后根据搭好的环境进行填充，最后测试即可。

2. 在MyBatis核心配置文件中，定义别名的两种方式如下：

   ```xml
    <typeAliases>
        <typeAlias type="com.jclight.domain.Account" alias="account"/>
   </typeAliases>
   ```

   ```xml
   <typeAliases>
       <package name="com.jclight.domain"/>
   </typeAliases>
   ```

   第二种方式是针对某一个包下的所有接口进行自定义别名，这个别名为接口的名字或接口名字的首字母小写形式。

3. 在MyBatis核心配置文件中，加载映射的两种方式如下：

   ```xml
   <mappers>
       <mapper resource="com\jclight\mapper\AccountMapper.xml"/>
   </mappers>
   ```

   ```xml
   <mappers>
        <package name="com.jclight.mapper"/>
   </mappers>
   ```

   第二种方式是针对放有映射文件的包下的全部映射文件，通过该标签进行全部加载，不需要配置多个mapper。

4. 设置数据响应的乱码过滤器，这里在controller层下的注解中去实现。如下：

   ```java
   @RequestMapping(value = "/save",produces = "text/html;charset=UTF-8")
   @ResponseBody
   public String save(Account account) {
       accountService.save(account);
       return "保存成功!";
   }
   ```

   