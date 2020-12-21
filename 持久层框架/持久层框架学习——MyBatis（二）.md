## 持久层框架学习——MyBatis（二）

### MyBatis映射文件（补充）

在实际开发中使用Dao层的MyBatis实现是非常普遍的，MyBatis的Dao层的实现有两个，最重要也是最常用的是接口代理方式，这种方式不需要我们去写接口的实现类，只需要通过MyBatis的映射配置文件就可以帮助我们处理复杂的数据库操作。在一些复杂业务逻辑中，复杂的SQL语句占有大多数，同样MyBatis的映射文件也可以帮助我们进行处理，从而不去实现接口就能达到我们想要的结果了。

#### 动态SQL语句

动态SQL语句简而言之就是可以动态变化的，有时我们的业务逻辑比较复杂，我们所写的SQL语句一定是动态变化的，否则我们的SQL语句就不能满足我们的要求了。例如：

```xml-dtd
select * from user where id=#{id} and username=#{username} and password=#{password};
```

上述的SQL语句并不是动态SQL，此时我们必须给足SQL语句所需的三个条件，缺少任何一个都会使得查询的结果失败，最后导致我们想查的数据查不出来。显然在实际的业务中，我们不会将所有条件的查询语句都写出来，只让用户去选择一种查询模式，而后台服务器去调用方法的时候执行动态SQL，将用户需要的给与用户，满足用户的查询条件，所以这就是动态SQL的作用。动态SQL有很多种模式，在MyBatis中是利用不同的标签来实现动态SQL的，如下所示：

##### if 标签

**if标签**如同我们编程语言中的if语句一样，可以在标签中设定判断条件，如果满足则在SQL语句中加入if标签内部的部分语句，不满足则进行下一步操作，示例如下：

映射文件中的SQL如下：

```xml
<select id="findByCondition" parameterType="user" resultType="com.jclight.domain.User">
    select * from user
    <where>
        <if test="id!=0">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
        <if test="password!=null">
            and password=#{password}
        </if>
    </where>
</select>
```

测试如下：

```java
@Test
public void findByCondition() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User condition = new User();
    condition.setId(1);
    condition.setUsername("刘一");
    //        condition.setPassword("123");
    List<User> userList = mapper.findByCondition(condition);
    System.out.println(userList);
}
```

此时，动态SQL会根据提供的条件进行判断并扩充SQL语句，若没有条件则查询表中的全部数据，这里使用的标签就是`<if>`标签，其中的属性`test`的值为判断条件，MyBatis会根据用户提交的数据进行判断，如果if判断为真则扩充SQL语句，if判断为假则不进行扩充。注意，这里的单元测试模拟了用户提交的数据，在实际开发中应为前端界面表单提交的数据或其他方式等。

##### foreach 标签

它的作用是循环执行SQL的拼接操作，示例如下：

映射文件中的SQL如下：

```xml
<select id="findByIds" resultType="com.jclight.domain.User" parameterType="list">
    select * from user
    <where>
        <foreach collection="list" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```

测试如下：

```java
@Test
public void findByIds() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    //模拟ids的数据
    List<Integer> ids = new ArrayList<>();
    ids.add(1);
    ids.add(3);
    ids.add(5);
    List<User> userList = mapper.findByIds(ids);
    System.out.println(userList);
}
```

这里的动态SQL用到了**foreach标签**，该标签的属性`collection`的值表示的是传递过来的参数的类型，属性`open`的值表示foreach从哪个地方之后开始，属性`close`的值表示从哪个地方之后结束，`item`表示数据的标识，`separator`表示有多个数据时以什么作为分隔符。foreach标签内部写查询条件代表的占位符。

#### SQL片段的抽取

在映射文件中可以将重复的SQL语句提取出来，使用时用`include`引用即可，最终达到SQL重用的目的。示例如下：

```xml
<sql id="selectUser">select * from user</sql>
<select id="findByCondition" parameterType="user" resultType="com.jclight.domain.User">
    <include refid="selectUser"/>
    <where>
        <if test="id!=0">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
        <if test="password!=null">
            and password=#{password}
        </if>
    </where>
</select>
```

从上述的代码中我们可以看出，使用**标签sql**即可将重复的SQL语句提取出来，这里的属性`id`的值代表该SQL语句的标识。如果要用的时候，可以使用**标签include**去引用这个SQL语句，这里的属性`refid`的值代表要使用哪个SQL语句，对应的就是sql标签中的id值。

### MyBatis核心配置文件（补充）

#### typeHandlers 标签

该标签的意思是类型处理器，在我们使用MyBatis框架，在预处理语句中设置参数或从结果集取值时，都会用到类型处理器将获取的值以合适的方式转换成Java类型。部分对应关系如下（默认类型处理器）：

|     类型处理器     |           Java类型           |        JDBC类型        |
| :----------------: | :--------------------------: | :--------------------: |
| BooleanTypeHandler | `java.lang.Boolean`，boolean |        BOOLEAN         |
|  ByteTypeHandler   |    `java.lang.Byte`，byte    |     NUMERIC或BYTE      |
|  ShortTypeHandler  |   `java.lang.Short`，short   | NUMERIC或SHORT INTEGER |
| IntegerTypeHandler |   `java.lang.Integer`，int   |    NUMERIC或INTEGER    |
|  LongTypeHandler   |    `java.lang.Long`，long    | NUMERIC或LONG INTEGER  |

MyBatis为我们提供了部分默认的类型处理器，但是有时会遇到不符合的类型，此时就需要我们去自定义类型处理器。具体步骤如下：

首先，实现MyBatis提供的TypeHandler接口或者集成BaseTypeHandler类。然后，可以选择性的将它映射到一个JDBC类型。例如：Java中的Date类型，存储到数据库的类型要求为1970年至今的毫秒数，从数据库取出来时转换为Date，存放到数据库时转换为varchar。这种就需要我们自定义转化器。它的具体开发步骤如下：

①定义转换类继承BaseTypeHandler<T>；②覆盖四个未实现的方法（其中的setNonNullParameter是为Java程序设置数据到数据库的回调方法，getNullableResult是为查询时MySql的字符串类型转换为Java的Type类型的方法）；③完成上述两部分后要在MyBatis核心配置文件中进行注册；④测试是否转换成功

示例如下：

转换器类如下：

```java
public class DateTypeHandler extends BaseTypeHandler<Date> {
    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Date date, JdbcType jdbcType) throws SQLException {
        long time = date.getTime();
        preparedStatement.setLong(i, time);
    }
    @Override
    public Date getNullableResult(ResultSet resultSet, String s) throws SQLException {
        //获取结果集中需要的数据（Long），将其转换成Date类并返回
        long aLong = resultSet.getLong(s);
        return new Date(aLong);
    }
    @Override
    public Date getNullableResult(ResultSet resultSet, int i) throws SQLException {
        long aLong = resultSet.getLong(i);
        return new Date(aLong);
    }
    @Override
    public Date getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        long aLong = callableStatement.getLong(i);
        return new Date(aLong);
    }
}
```

核心配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--  引入外部资源文件  -->
    <properties resource="jdbc.properties"/>
    <!--  自定义别名  -->
    <typeAliases>
        <typeAlias type="com.jclight.domain.User" alias="user"/>
    </typeAliases>
    <!--  自定义类型处理器  -->
    <typeHandlers>
        <typeHandler handler="com.jclight.handler.DateTypeHandler"/>
    </typeHandlers>
    <!--  数据源环境  -->
    <environments default="Developement">
        <environment id="Developement">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--  加载映射文件  -->
    <mappers>
        <mapper resource="com\jclight\mapper\UserMapper.xml"/>
    </mappers>
</configuration>
```

映射文件如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jclight.mapper.UserMapper">
    <insert id="saveUser" parameterType="user">
        insert into user values(#{id},#{username},#{password},#{birthday});
    </insert>
    <select id="findById" resultType="com.jclight.domain.User" parameterType="int">
        select * from user where id=#{id};
    </select>
</mapper>
```

此时我们进行插入或查询测试，最后的结果为数据库中存放Date的毫秒值，而经过转换器类处理后读取出来的数据为Date类型。由此，这种方式极大的方便了我们遇到数据库中的类型和Java代码中的类型不同的问题，也由此可以对一些数据做一定的处理，达到安全性的标准。

#### plugins 标签

MyBatis可以使用第三方插件对功能进行拓展，例如比较复杂的分页功能的实现，在MyBatis中我们可以在plugins标签下引入分页助手PageHelper，它将复杂的分页操作封装起来，使用简单的方式即可获得分页的数据。操作步骤：首先导入PageHelper的坐标，然后在MyBatis核心配置文件中配置该插件，最后进行测试即可。示例如下：

导入坐标：

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>3.7.5</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>0.9.1</version>
</dependency>
```

在MyBatis核心配置文件中配置插件：

```xml
 <plugins>
     <plugin interceptor="com.github.pagehelper.PageHelper">
         <property name="dialect" value="mysql"/>
     </plugin>
</plugins>
```

**注意**：这里的属性interceptor的值为分页助手的PageHelper类，下面的name的值dialect表示该分页是采用哪种语言来写的，我们这里是mysql所以value的值也为mysql。如果当前的项目的数据库为sqlserver，那这里的value值为sqlserver。对于使用其他插件而言，只需要修改interceptor的值，其他的配置只需查看相关插件的文档进行配置即可。

测试：

```java
PageHelper.startPage(3, 3);
```

在我们之前的测试代码中加入该语句即可实现分页操作，这里`startPage`的第一个参数表示第几页的数据，第二个参数表示每页显示几条数据。在我们使用日志去处理执行时，即可看到控制台输出的执行流程，在这里明显可以观察到该插件自动帮我们加入了limit语句。

上述的操作是简单的分页功能实现操作，如果我们需要让控制台输出当前页的页码、下一页的页码、剩余几页等数据时，我们可以更进一步去运用该插件，具体如下：

```java
//获得与分页相关的参数
PageInfo<User> pageInfo = new PageInfo<User>(userList);
System.out.println("当前页：" + pageInfo.getPageNum());
System.out.println("每页显示条数：" + pageInfo.getPageSize());
System.out.println("总条数：" + pageInfo.getTotal());
System.out.println("总页数：" + pageInfo.getPages());
System.out.println("上一页：" + pageInfo.getPrePage());
System.out.println("下一页：" + pageInfo.getNextPage());
System.out.println("是否为第一页：" + pageInfo.isIsFirstPage());
System.out.println("是否为最后一页：" + pageInfo.isIsLastPage());
```

这里不需要再对配置文件进行处理，只需要在之前进行测试的地方加上上面的代码片段即可，这里的参数userList为查询到的结果，根据PageInfo的相关API我们可以获取分页的一些相关参数。使用该插件比我们自己手动去实现分页要简单的多。PageInfo的原理是利用查询到的数据进行逆向推理，最终进行处理得到相关的数据。根据这些我们就可以在WEB工程中快速实现分页功能的编写了。

### MyBatis多表操作

#### 一对一查询

数据库中多个表之间的关系可以有三种关系（一对一、一对多或多对一、多对多）。之前我们对一对一关系的多表操作是利用一个VO对象，将两个实体类封装在一个VO对象中进行操作的，那么在这里使用框架进行一对一关系的多表操作具体步骤如下：首先搭建基本的运行环境，然后将需要操作的数据表封装成实体对象，最后进行配置和测试即可。这里面的前两步和之前一样，具体配置如下：

根据创建的实体类创建对应的Mapper类接口，并声明一个查询方法，然后根据该Mapper接口创建对应的映射文件具体如下：

```xml
<mapper namespace="com.jclight.mapper.OrderMapper">
    <resultMap id="orderMap" type="order">
        <id column="oid" property="id"/>
        <result column="ordertime" property="ordertime"/>
        <result column="total" property="total"/>
        <result column="uid" property="user.id"/>
        <result column="username" property="user.username"/>
        <result column="password" property="user.password"/>
        <result column="birthday" property="user.birthday"/>
    </resultMap>
    <select id="multiTableFindAll" resultMap="orderMap">
        SELECT *,o.id oid FROM orders o,`user` u WHERE o.uid=u.id
    </select>
</mapper>
```

**注意**：这里的`<resultMap id="orderMap" type="order">`中的属性id可随意命名，type属性的值为要操作的实体类对象，由于这里在核心配置文件中配置了自定义类型别名，所以这里为order。下面的`<id column="oid" property="id"/>`中的属性column表示数据表的字段名称，property表示实体的属性名称。注意这里的id为`oid`表示在执行SQL语句时一定也要含有`oid`，否则将会报错导致查询不到结果。`<result column="ordertime" property="ordertime"/>`表示对结果集配置（封装），column属性表示数据库表的字段的名字，property表示对应的实体的属性名称。这些组成了一个resultMap，通过定义好的resultMap，我们就可以将查询的结果封装在一个实体对象中，利用该实体对象从而得到另一个实体对象在数据库中的值。这里的SQL标签中的resultType换为resultMap，它的属性值对应的就是之前配置好的resultMap的id。在SQL语句的编写中一定要含有上面含有的自定义的名称`oid`，因为这个别名是自己定义的，在数据表中没有，一旦SQL语句中没有声明，那么执行的查询结果将不存在或报错。

除此之外，还有另一种方式处理实体对象中含有另一个实体对象的方法，具体如下：

```xml
<resultMap id="orderMap" type="order">
    <id column="oid" property="id"/>
    <result column="ordertime" property="ordertime"/>
    <result column="total" property="total"/>
    <association property="user" javaType="user">
        <id column="uid" property="id"/>
        <id column="username" property="username"/>
        <id column="password" property="password"/>
        <id column="birthday" property="birthday"/>
    </association>
</resultMap>
<select id="multiTableFindAll" resultMap="orderMap">
    SELECT *,o.id oid FROM orders o,`user` u WHERE o.uid=u.id
</select>
```

上述的`<association property="user" javaType="user">`中的属性property的值为当前实体的属性名称，属性javaType的值为当前实体的属性的类型。

测试方法同之前一样，使用该方式进行配置后可以快速的操作多个表，不需要去实现复杂的多表操作业务逻辑。

#### 一对多查询

一般而言我们的一对多关系的多表操作，通常在实体中将某个实体封装成集合，通过VO对象来进行操作。例如：用户和订单的关系，这里在用户的实体中封装订单的数据，并将订单封装为集合对象，通过集合对象我们可以实现一个用户含有多个订单的需求。在不使用持久层框架前，这些都放在一个VO对象的实体中进行处理，通过VO对象来实现这种关系，使用持久层框架后我们只需要在关系为1的实体中使用集合封装关系为N的实体对象，然后对映射文件进行配置即可。示例如下：

关系为1的实体类（部分代码）：

```java
private List<Order> orders;
public List<Order> getOrders() {
    return orders;
}
public void setOrders(List<Order> orders) {
    this.orders = orders;
}
```

映射文件：

```xml
<mapper namespace="com.jclight.mapper.UserMapper">
    <resultMap id="userMap" type="user">
        <id column="uid" property="id"/>
        <result column="username" property="username"/>
        <result column="password" property="password"/>
        <result column="birthday" property="birthday"/>
        <collection property="orders" ofType="order">
            <!--      封装集合内部Order的数据      -->
            <id column="oid" property="id"/>
            <result column="ordertime" property="ordertime"/>
            <result column="total" property="total"/>
        </collection>
    </resultMap>
    <select id="findAll" resultMap="userMap">
        SELECT *,o.id oid FROM `user` u,orders o WHERE u.id=o.uid
    </select>
</mapper>
```

**注意**：这里的一些配置和一对一查询的多表操作的配置类似，不同之处在于使用`<collection property="orders" ofType="order">`标签封装集合内部的实体数据，它的属性property的值为关系为1的实体中的属性名，ofType的值为property的值对应的集合的泛型。

配置完成后进行测试即可。

#### 多对多查询

根据建表原则多对多的关系需要使用一张中间表进行关联，所以这里所操作的其实是三张数据表，它的具体的配置和一对一关系的多表操作一样，只是稍有不同。示例如下：

这里还是如一对多查询类似，在某一个实体中封装另一个实体对象并将其封装成集合，映射配置如下：

```xml
 <resultMap id="userRoleMap" type="user">
     <!--    封装user的信息    -->
     <id column="userid" property="id"/>
     <result column="username" property="username"/>
     <result column="password" property="password"/>
     <result column="birthday" property="birthday"/>
     <!--    封装user内部的roles信息    -->
     <collection property="roles" ofType="role">
         <id column="roleid" property="id"/>
         <result column="rolename" property="roleName"/>
         <result column="reledesc" property="roleDesc"/>
     </collection>
</resultMap>
<select id="findUserAndRoleAll" resultMap="userRoleMap">
    SELECT * FROM `user` u,sys_role sr,sys_user_role sur WHERE u.id=sur.userid AND sur.roleid=sr.id
</select>
```

这里的不同之处在于所写的SQL语句的不同，其他大体相同。

### MyBatis注解开发

在MyBatis中也存在注解，通过注解开发方式我们可以减少编写Mapper映射文件从而实现简化开发，常用的注解如下：

|  注解名  |                 描述                  |
| :------: | :-----------------------------------: |
| @Insert  |           实现新增（插入）            |
| @Update  |           实现更新（修改）            |
| @Delete  |           实现删除（删除）            |
| @Select  |           实现查询（查询）            |
| @Result  |            实现结果集封装             |
| @Results | 可以与@Result一起使用，封装多个结果集 |
|   @One   |         实现一对一结果集封装          |
|  @Many   |         实现一对多结果集封装          |

#### 使用注解实现简单CRUD

当我们使用注解进行CRUD操作时，这时就不用去写映射文件了。我们只需要在接口上加入对应的注解并写好SQL语句，并在配置文件中指定接口所在的包即可。示例如下：

Mapper接口如下：

```java
public interface StaffMapper {
    /**
     * 新增职员
     * @param staff Staff对象
     */
    @Insert("insert into staff values(#{id},#{name},#{sex},#{age})")
    void save(Staff staff);

    /**
     * 删除职员
     * @param id 职员编号
     */
    @Delete("delete from staff where id=#{id}")
    void delete(int id);

    /**
     * 修改职员信息
     * @param staff Staff对象
     */
    @Update("update staff set name=#{name},sex=#{sex},age=#{age} where id=#{id}")
    void update(Staff staff);

    /**
     * 查询全部职员信息
     * @return Staff对象封装的集合
     */
    @Select("select * from staff")
    List<Staff> findAll();
    
    /**
     * 根据员工编号查询员工信息
     * @param id 员工编号
     * @return Staff对象封装的集合
     */
    @Select("select * from staff where id=#{id}")
    Staff findById(int id);
}
```

这时的核心配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--  导入外部资源文件  -->
    <properties resource="jdbc.properties"/>
    <!--  配置数据源环境  -->
    <environments default="Developement">
        <environment id="Developement">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--加载映射关系-->
    <mappers>
        <!--指定接口所在的包-->
        <package name="com.jclight.mapper"/>
    </mappers>
</configuration>
```

此时就可以进行测试了，测试代码示例如下：

```java
public class CRUDTest {
    private StaffMapper mapper;
    @Before
    public void before() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        mapper = sqlSession.getMapper(StaffMapper.class);
    }
    @Test
    public void baseOnXmlCreate() {
        Staff staff = new Staff();
        staff.setName("测试");
        staff.setSex("女");
        staff.setAge(15);
        mapper.save(staff);
    }
    @Test
    public void baseOnXmlDelete() {
        mapper.delete(12);
    }
}
```

观察测试结果可得，这时的注解CRUD操作也是成功的。

#### 注解开发的一对一多表操作

通过注解我们也可以对一些含有外键约束的表进行操作，这些表可能是（`1:1、1:N或N:1、N:N`）的关系。在基于XML的MyBatis多表操作中，我们使用的是`resultMap`或`resultMap+association`或`resultMap+collection`标签实现的，在注解开发中可以使用`@Result、@Results、@One、@Many`这些注解组合进行实现。这些注解的作用对应的XML中的标签如下：

|   注解   |                       对应XML标签说明                        |
| :------: | :----------------------------------------------------------: |
| @Results | 代替的是标签`resultMap`，该注解可以使用单个@Result注解，也可以使用@Result集合。<br/>使用格式：`@Results({@Result(),@Result()})` |
| @Result  | 代替了标签`id`和`result`，@Result中的属性说明如下：<br />column-->数据库中表的列名<br />property-->需要装配的属性名<br />one-->需要使用的@One注解，如`@Result(one=@One)()`<br />many-->需要使用的@Many注解，如`@Result(many=@Many)()` |
|   @One   | 主要用来一对一查询，代替了`assocation`标签，在注解中用来指定子查询返回单一对象。@One注解属性说明：<br />select-->指定用来多表查询的sqlmapper，使用格式：`@Result(column="",property="",one=@One(select=""))` |
|  @Many   | 主要用来一对多查询，代替了`collection`标签，在注解中用来指定子查询返回对象集合。使用格式如下：<br />`@Result(property="",column="",many=@Many(select=""))` |

示例如下：

```java
public interface OrderMapper {
    /**
     * 查询order和user表中的数据
     *
     * @return Order对象组成的集合
     */
    @Select("select *,o.id oid from orders o,`user` u where o.uid=u.id")
    @Results({
            @Result(column = "oid",property = "id"),
            @Result(column = "ordertime",property = "ordertime"),
            @Result(column = "total",property = "total"),
            @Result(column = "total",property = "total"),
            @Result(column = "uid",property = "user.id"),
            @Result(column = "username",property = "user.username"),
            @Result(column = "password",property = "user.password"),
    })
    List<Order> findAll();
}
```

测试如下：

```java
public class BaseOnAnnotationCRUDTest {
    private OrderMapper orderMapper;

    @Before
    public void before() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        orderMapper = sqlSession.getMapper(OrderMapper.class);
    }

    @Test
    public void retrieve() {
        List<Order> orders = orderMapper.findAll();
        for (Order order : orders) {
            System.out.println(order);
        }
    }
}
```

这种方式和之前的XML配置类似，只不过使用注解替代了标签的配置。此外还有另一种配置的方式，该方式比起上述更为简便具体如下：

```java
@Select("select * from orders")
@Results({
    @Result(column = "id", property = "id"),
    @Result(column = "ordertime", property = "ordertime"),
    @Result(column = "total", property = "total"),
    @Result(
        property = "user",
        column = "uid",
        javaType = User.class,
        one = @One(select = "com.jclight.mapper.UserMapper.findById")
    )
})
List<Order> findAll();
```

这里我们使用注解@Result中自带的属性去封装实体，并简化SQL语句。这里的属性property表示要封装的属性名称，column表示根据哪个字段查询对应表中的数据，javaType表示要封装的实体类型。这里加入的一对一查询的属性one，它所对应的注解的属性select表示查询哪个接口的方法获得数据，值对应的是哪个接口下的对应的方法。

#### 注解开发的一对多多表操作

示例如下：

```java
@Select("select * from user")
@Results({
    @Result(id = true, column = "id", property = "id"),
    @Result(column = "username", property = "username"),
    @Result(column = "password", property = "password"),
    @Result(column = "birthday", property = "birthday"),
    @Result(
        property = "orders",
        column = "id",
        javaType = List.class,
        many = @Many(select = "com.jclight.mapper.OrderMapper.findByUid")
    )
})
List<User> findUserAndOrderAll();
```

`com.jclight.mapper.OrderMapper.findByUid`如下：

```java
@Select("select * from orders where uid=#{uid}")
List<Order> findByUid(int uid);
```

上述即可实现一对多或多对一的查询，这里的配置和一对一查询类似，主要在于使用@Results({@Result()})组合来封装数据，其中的many为一对多或多对一操作，指定的值为另一个表的查询操作（相对应的）。

#### 注解开发的多对多多表操作

示例如下：

```java
@Select("select * from user")
@Results({
    @Result(id = true, column = "id", property = "id"),
    @Result(column = "username", property = "username"),
    @Result(column = "password", property = "password"),
    @Result(column = "birthday", property = "birthday"),
    @Result(
        property = "roles",
        column = "id",
        javaType = List.class,
        many = @Many(select = "com.jclight.mapper.RoleMapper.findByUid")
    )
})
List<User> findUserAndRoleAll1();
```

`com.jclight.mapper.RoleMapper.findByUid`接口方法如下：

```java
@Select("select * from sys_user_role sur,sys_role sr where sur.roleid=sr.id and sur.userid=#{id}")
List<Role> findByUid(int id);
```

此时进行测试即可，由上述代码可以看出，多对多的查询和一对多的查询是类似的。这里只需要修改对应的SQL语句加入中间表参与查询即可。

### 补充/技巧

1. 当我们进行单元测试时，可以使用`Junit4`提供的注解`@Before`抽取相同的代码片段，这样在其他的测试时就不需要写大量重复的代码了。它里面提供了很多注解可以帮助我们简化代码，这些注解的执行流程为`@BeforeClass -> @Before -> @Test -> @After -> @AfterClass`。

