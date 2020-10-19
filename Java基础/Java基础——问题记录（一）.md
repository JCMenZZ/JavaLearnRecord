## Java基础——问题记录（一）

### 问题记录

#### 问题 一：一个`.java`文件里面可以有多个class,但是最多只能有一个“public class”,为什么?

**答**：因为.java文件在经过JVM编译时，这个public class将作为一个单一的公共接口。若存在多个public class，编译器将会报错；若不存在public class，编译器会生成多个. class文件。简而言之，public class是提供给编译器的一个标识，告诉编译器程序的入口在哪里，以便于编译器可以处理存在多个类的java文件，所以只能有一个。

#### 问题 二：Java的基本数据类型是？

**答**：Java的基本数据类型一共有八种，分别为四种整数类型（byte、short、int、long）、两种浮点数类型（float、double）、一种字符类型（char）、一种布尔类型（boolean）；根据这八种基本数据类型又对应了Java中内置的八种包装类，分别为Byte、Short、Integer、Long、Float、Double、Character、Boolean。注意，这八种包装类都是Object类型。

#### 问题 三：StringBuffer(String s)的实体容量？

**答**：StringBuffer(String s)在创建时可以指定给对象的实体的初始容量为参数字符串s的长度额外再增加16个字符。

#### 问题 四：什么叫Socket?怎样建立socket连接？

**答**：Socket又称为套接字，它是计算机之间进行通信的一种约定或一种方式。通过 socket 这种约定，一台计算机可以接收其他计算机的数据，也可以向其他计算机发送数据。在C/S结构中建立socket连接，要分别在客户端和服务器上创建socket对象，客户端socket用来向服务器发送连接请求，服务器端ServerSocket用来和进行请求的客户端进行连接。在Java中建立socket连接很简单，没有C/C++中复杂，大致的步骤就是建立socket对象进行客户端和服务器端的连接通信，之后根据需求创建流对象可以是输入输出流或视频或图像流对象，进行简单的IO交互，最后关闭socket对象和流对象即可。注意这里可以使用不同的类来创建是基于TCP或UDP的网络程序。

#### 问题 五：接口和抽象类的区别？

**答**：接口是对动作的抽象，表示的是这个对象能做什么动作，而抽象类是对根源的抽象，表示的是这个对象能做什么。它们都不能直接被实例化，若要实例化抽象类变量必须指向实现所有抽象方法的子类对象，而接口变量必须指向实现所有接口方法的类对象。抽象类要被子类继承，而接口要被类实现。接口中只能做方法的声明，抽象类中可以做方法的声明，也可以做方法的实现。接口中定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量。抽象类里的抽象方法必须全部被子类所实现，如果子类不能全部实现父类抽象方法，那么该子类只能是抽象类。同样实现接口的时候，如不能全部实现接口方法，那么该类也只能为抽象类。抽象方法只能声明，不能实现，抽象类里可以没有抽象方法。接口是设计的结果 ，抽象类是重构的结果。如果一个类里有抽象方法，那么这个类只能是抽象类。抽象方法要被实现，所以不能是静态的，也不能是私有的。接口可继承接口，并可多继承接口，但类只能单根继承。这也是Java中实现多重继承的方法。抽象类和接口都是用来抽象具体对象的，但是接口的抽象级别最高。

#### 问题 六：抽象类的构造函数存在的意义？

**答**：抽象类中的构造方法其实是给实现该抽象类的子类对象进行初始化的。因为在面向对象编程语言中，初始化一个类，无论该类是不是抽象类，都会先调用其父类的构造方法，进行初始化父类。在java中可以使用super关键字在子类的构造方法中，调用父类的有参构造方法。简而言之，抽象类是一个特殊的类，只要是类都有构造函数，只不过这个构造函数生效的时间不一样。

#### 问题 七：包装类有何意义？

**答**：包装类是将基本类型包装成Object引用类型，总共有八类（Boolean、Byte、Short、Character、Integer、Long、Float、Double），它的特点是会自动装箱和自动拆箱。因为在面向对象开发中，有时基本类型会参与到开发中，但是在Java中基本类型是以值的形式保存在内存中，而不是以引用类型，所以不能参与到面向对象开发，这时就需要使用包装类对基本类型进行装箱。包装类有五大特点，①包装类提供了一些常数，例如：`Integer.MAX_VALUE`(整数的最大值)、`Double.NaN`(非数字)、`Double.POSITIVE_INFINITY`(正无穷)。②提供了从字符串转换或转换成字符串的方法（`valueOf(String) toString()`）。③通过xxxValue()方法可以得到所包装的值。④对象中所包装的值是不可改变的，要改变对象中的值就只有重新生成新的对象。⑤toString()、equals()等方法进行了覆盖

#### 问题 八：final关键字？

**答**：在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）。final关键字与static关键字进行组合使用时，会将变量变成常量。当final修饰类时，表明这个类不能被继承，final修饰的类中的所有成员方法都会被隐式地指定为final方法，该类中的成员变量可以根据需要设为final。使用final修饰方法的原因主要有两点，第一点是把方法锁定，以防任何继承类修改它的含义；第二点是效率。（注意：类的private方法会隐式地被指定为final方法）使用final修饰变量时，对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能再更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。

#### 问题 九：C/S结构、B/S结构的优缺点，它们有什么特点？

**答**：C/S结构是客户端+服务器+DB的结构模式，它的特点是数据库只负责数据库的管理、应用服务器只负责业务逻辑、客户端只负责界面操作。优点是适合大型应用，缺点是需要单独安装客户端，客户端还需独立开发，定义协议，比较复杂。B/S结构是浏览器+服务器+DB的结构模式，它的特点是数据库只负责数据库的管理、Web服务器只负责业务逻辑、浏览器负责界面操作。它的优点是不用装客户端了，只要有浏览器既可以，缺点是浏览器的一些局限性。

#### 问题 十：重载和重写的区别？

**答**：方法重载是让类以统一的方式处理不同类型数据的一种手段。多个同名函数同时存在，具有不同的参数个数或类型。重载是多态性的一种表现。Java中的方法重载，就是在类中可以创建多个方法，它们具有相同的名字，但具有不同的参数和不同的定义。调用方法时通过传递给它们的不同参数个数和参数类型来决定具体使用哪个方法。重载的时候，方法名要一样，但是参数类型和个数不一样，返回值类型可以相同也可以不相同。如果在子类中定义某方法与其父类有相同的名称和参数，我们说该方法被重写。在Java中，子类可继承父类中的方法，而不需要重新编写相同的方法。但有时子类并不想原封不动地继承父类的方法，而是想作一定的修改，这就需要采用方法的重写。方法重写又称方法覆盖。

****

### 常用方法补充

#### 1、数组元素复制

数组元素复制可以使用Java.lang.System类的arrayCopy()方法，方法如下：

```java
public static void arraycopy(Object src, int srcPos,  Object dest, int destPos, int length)
```

**参数解释**：src：源数组；srcPos：源数组下标；dest：目的数组；destPos：目的数组下标；length：需要复制几个数组。

**方法作用**：复制源数组中从下标srcPos开始的length个元素到目的数组，从下标destPos的位置开始存储。

**注意**：数组复制的常用方法有四种：

1. + for循环复制，效率最低
2. + System.arraycopy() 效率最高
3. + Arrays.copyOf() 效率次于第二种方法
4. + Object.clone() 效率次于第二种和第三种

#### 2、数组的排序和数组的查找

在Java中的数组排序，除去我们自己定义的方法之外，可以使用Java中提供给我们的sort方法，方法示例如下：

```java
int array[]={2,5,-9,33,22,11,56,-3};
Arrays.sort(array);
//排序结果：-9，-3，2，5，11，22，33，56
```

对于数组元素的查找，可以使用binarySearch()方法来查找数组中的元素，方法示例如下：

```java
int array[]={2,5,-9,33,22,11,56,-3};
int index = Arrays.binarySearch(array, 56);
System.out.println("元素56在第 " + index + " 个位置");
//输出结果：元素56在第6个位置
```

除此之外，我们还可以使用自己定义的方法来进行数组元素的排序和数组元素的查找。

#### 3、Date类的构造方法

通过查阅Java的JDK文档，可以得知目前未过时的Date类的构造方法主要有两个，`Date()`和`Date(long date)`。第一个构造函数使用当前日期和时间来初始化对象。第二个构造函数接收一个参数，该参数是从1970年1月1日起的毫秒数。使用这两个构造方法，可以获取或指定当前时间。

#### 4、SimpleDateFormat类

SimpleDateFormat类是一个与语言环境有关的方式来格式化和解析日期的具体类，它允许进行格式化（日期->文本）、解析（文本->日期）和规范化。SimpleDateFormat 使得可以选择任何用户定义的日期/时间格式的模式。除此之外，还可以使用DateFormat类格式化日期。

**构造方法：**

|                     方法名                     |                       描述                        |
| :--------------------------------------------: | :-----------------------------------------------: |
|               SimpleDateFormat()               | 用默认的格式和默认的语言环境构造 SimpleDateFormat |
|        SimpleDateFormat(String pattern)        | 用指定的格式和默认的语言环境构造 SimpleDateFormat |
| SimpleDateFormat(String pattern,Locale locale) | 用指定的格式和指定的语言环境构造 SimpleDateFormat |

这里的pattern代表的是用户指定的格式，在自定义格式中常用的字母和含义如下：

```java
/**
y 代表年份，一般使用yy表示两位年份，yyyy代表四位年份
M 代表月份，一般使用MM表示月份如‘05’，使用MMM表示月份时，会根据语言环境显示不同语言的月份如Locale.CHINA下显示‘三月’
d 代表天数，一般使用dd表示天数
D 代表年份中的天数，表示当天是该年中的第几天，一般用D表示
E 代表星期，一般使用E表示，它也会根据语言环境的不同显示不同格式的星期
H 代表一天中的小时数（0~23），一般使用HH表示小时数
h 代表一天中的小时数（1~12），一般使用hh表示小时数
m 代表分钟数，一般使用mm表示分钟数
s 代表秒数，一般使用ss表示秒数
S 代表毫秒数，一般使用SSS表示毫秒数
在指定格式pattern中，可以加入一些中文字符串来使输出的日期更好看
*/
Date now=new Date();
String simpleTime = new SimpleDateFormat("今天是 " + "yyyy年MM月dd日 E HH:mm:ss").format(now);
System.out.println(simpleTime);
//输出：今天是 2020年10月1日 星期四 08:56:45
```

**注意：**指定语言环境可以使用`Locale.CHINA`指定中国语言环境，或使用`Locale.US`指定美国语言环境等。

#### 5、Calendar类

使用Calendar类的static方法`getInstance()`可以初始化一个日历对象，例如：`Calendar calendar=Calendar.getInstance();`

**常用方法**：

|                            方法名                            |                 描述                 |
| :----------------------------------------------------------: | :----------------------------------: |
|      public final void set(int year,int month,int date)      |         将日历翻到指定的时间         |
| public final void set(int year,int month,int date,int hour,int minute) |         将日历翻到指定的时间         |
| public final void set(int year,int month, int date, int hour, int minute,int second) |         将日历翻到指定的时间         |
|                public long getTimeInMillis()                 |           将时间表示为毫秒           |
|             public final void setTime(Date date)             |  使用给定的Date设置此Calendar的时间  |
|                  public int get(int field)                   | 获取有关年份、月份、小时、星期等信息 |

**注意**：`calendar.get(Calendar.MONTH); `的返回值是整数，不同的数字代表不同的月份，0代表一月，11代表十二月。

`calendar.get(Calendar.DAY_OF_WEEK);`的返回值是整数，代表星期，这里的1代表周日、2代表周一，以此类推。

#### 6、BigInteger类

BigInteger大数类，当要存储或处理比Integer更大的数字时，就可以使用BigInteger类来处理更大的数字。BigInteger支持任意精度的整数，在运算中BigInteger类型可以准确地表示任何大小的整数值。

**构造函数：**

|         函数名         |                             描述                             |
| :--------------------: | :----------------------------------------------------------: |
| BigInteger(String val) | 将String类型的val转换成BigInteger对象，val代表的是十进制的字符串 |

使用方法如下：

```java
BigInteger bi = new BigInteger("9999999999");
```

**常用方法：**

|               方法名               |                             描述                             |
| :--------------------------------: | :----------------------------------------------------------: |
|        add(BigInteger val)         |                           加法运算                           |
|      subtract(BigInteger val)      |                           减法运算                           |
|      multiply(BigInteger val)      |                           乘法运算                           |
|       divide(BigInteger val)       |                           除法运算                           |
|     remainder(BigInteger val)      |                           取余运算                           |
| divideAndRemainder(BigInteger val) |       除法运算，返回数组的第一个值为商，第二个值为余数       |
|         pow(int exponent)          |                  做参数的 exponent 次方运算                  |
|              negate()              |                           取相反数                           |
|          shiftLeft(int n)          |         将数字左移 n 位，如果 n 为负数，则做右移操作         |
|         shiftRight(int n)          |         将数字右移 n 位，如果 n 为负数，则做左移操作         |
|        and(BigInteger val)         |                            与运算                            |
|         or(BigInteger val)         |                            或运算                            |
|     compareTo(BigInteger val)      |                        数字的比较运算                        |
|         equals(Object obj)         | 当参数 obj 是 Biglnteger 类型的数字并且数值相等时返回 true, 其他返回 false |
|        min(BigInteger val)         |                        返回较小的数值                        |
|        max(BigInteger val)         |                        返回较大的数值                        |

#### 7、DecimalFormat类

DecimalFormat是NumberFormat的一个具体子类，用于格式化十进制的数字。DecimalFormat包含一个模式和一组符号，根据这些可以对任意语言环境的数进行分析和格式化。（例如：对于一个double类型的数值而言，可以使用DecimalFormat类对其小数点后的数字进行格式化操作，使其输出我们想要的数字，具体书写方法例如`String payNum = new DecimalFormat("#.00").format((double) request.getSession().getAttribute("priceNum"));`这里即可控制显示的小数点后的位数为两位）常用的符号如下：

|  符号  |     位置     |                             含义                             |
| :----: | :----------: | :----------------------------------------------------------: |
|   0    |     数字     |                          阿拉伯数字                          |
|   #    |     数字     |               阿拉伯数字，如果不存在则显示为空               |
|   .    |     数字     |                  小数分隔符或货币小数分隔符                  |
|   -    |     数字     |                             减号                             |
|   ,    |     数字     |                          分组分隔符                          |
|   E    |     数字     |   分隔科学计数法中的尾数和指数。在前缀或后缀中无需加引号。   |
|   ;    | 子模式的边界 |                     分隔正数和负数子模式                     |
|   %    |  前缀或后缀  |                   乘以 100 并显示为百分数                    |
| \u2030 |  前缀或后缀  |                   乘以 1000 并显示为千分数                   |
| \u00A4 |  前缀或后缀  | 货币记号，由货币符号替换。如果两个同时出现，则用国际货币符号替换。如果出现在某个模式中，则使用货币小数分隔符，而不使用小数分隔符。 |
|   '    |  前缀或后缀  | 用于在前缀或或后缀中为特殊字符加引号，例如 `"'#'#"` 将 123 格式化为 `"#123"`。要创建单引号本身，请连续使用两个单引号：`"# o''clock"`。 |

它的具体用法和上面的例子差不了多少，一般而言几个0就代表显示几个数字，在进行货币显示时，可以使用`,`来进行格式化输出，使用E可以将数字表示成科学计数法显示。

```java
//在这里priceNum代表的数字是256.353535353直接显示在界面很不友好，所以在这里进行格式化操作，之后再进行输出
String payNum = new DecimalFormat("#.00").format((double) request.getSession().getAttribute("priceNum"));
//输出结果为：payNum:256.35
```

#### 8、Pattern与Matcher类

这两个类都是Java中和正则表达式相关的类，Pattern类的作用在于编译正则表达式后创建一个匹配模式。Matcher类使用Pattern实例提供的模式信息对正则表达式进行匹配。关于Pattern类的常用方法如下：

|                  方法名                  |                             描述                             |
| :--------------------------------------: | :----------------------------------------------------------: |
|      Pattern complie(String regex)       | 相当于Pattern的构造方法，将给定的正则表达式regex编译并赋值给Pattern类 |
|             String pattern()             |                  返回正则表达式的字符串形式                  |
| Pattern compile(String regex, int flags) | 给方法和第一个方法类似，功能基本相同，flags用来控制正则表达式的匹配行为 |
|               int flags()                |                返回当前Pattern匹配的flag参数                 |
|   Pattern.matcher(CharSequence input)    |            对指定输入的字符串创建一个Matcher对象             |
|         Pattern.quote(String s)          |                   返回给定的字符串的字面量                   |

关于方法的详细信息，请参考JDK的开发文档。

**注意：**Pattern类的构造方法是私有的，不能直接创建。

关于Matcher类的**常用方法**如下：

|       方法名        |                             描述                             |
| :-----------------: | :----------------------------------------------------------: |
|  boolean matches()  | 尝试对整个目标字符展开匹配检测，当整个目标字符串完全匹配时返回true |
| boolean lookingAt() |  对前面的字符串进行匹配，当匹配到的字符串在最前面时返回true  |
|   boolean find()    |          对字符串进行匹配，匹配到的字符串时返回true          |
|     int start()     |         返回当前匹配到的字符串在原目标字符串中的位置         |
|      int end()      | 返回当前匹配的字符串的最后一个字符在原目标字符串中的索引位置 |
|   String group()    |                     返回匹配到的子字符串                     |

#### 9、Scanner类

在进行I/O交互的时候，我们可以使用Scanner类来获取用户的输入。初始化Scanner类对象如下：

```java
Scanner sc=new Scanner(System.in);
```

**常用方法**

|    方法名     |                  描述                  |
| :-----------: | :------------------------------------: |
|    next()     |   获取从键盘上输入的不带空格的字符串   |
|  nextLine()   | 获取从键盘上输入的字符以回车键作为结束 |
|   hasNext()   |            判断是否还有输入            |
| hasNextLine() |            判断是否还有输入            |

**注意：**next()一般与hasNext()一起使用，nextLine()一般与hasNextLine()一起使用。

#### 10、关于GUI的一些补充

**① 为菜单项设置快捷键**

```java
public void setAccelerator(KeyStroke keyStroke)
```

**② 为菜单项设置图标**

​	第一步：用图标类Icon声明一个图标，然后使用其子类ImageIcon类创建一个图标。

```java
Icon icon=new ImageIcon(“a.gif”);
```

​	第二步：菜单项调用setIcon方法将图标设置为icon。

```java
setIcon(Icon icon)
```

**③ 拆分窗格**

拆分窗格用到JSplitPane类，拆分窗格就是被分成两部分的容器。拆分窗格有两种类型：水平拆分和垂直拆分。水平拆分窗格用一条拆分线把窗格分成左右两部分，拆分线可以水平移动。垂直拆分窗格用一条拆分线把窗格分成上下两部分，拆分线可以垂直移动。

**常用构造方法**：

|                        方法名                        |                             描述                             |
| :--------------------------------------------------: | :----------------------------------------------------------: |
|      JSplitPane(int a,Component b,Component c)       | 参数a取JSplitPane的静态常量HORIZONTAL_SPLIT或VERTICAL_SPLIT，以决定是水平还是垂直拆分。 |
| JSplitPane(int a, boolean b,Component c,Component d) |  参数b决定当拆分线移动时，组件是否连续变化（true是连续）。   |

**④ 分层窗格**

JLayeredPane类用于分层窗格，如果添加到容器中的组件经常需要处理重叠问题，需要将组件添加到分层窗格。分层窗格分成5个层，用`add(Jcomponent com, int layer);`方法添加组件com，并指定com所在的层，其中参数layer的取值取自JLayeredPane类中的类常量：`DEFAULT_LAYER（最低层）、PALETTE_LAYER、MODAL_LAYER、POPUP_LAYER、DRAG_LAYER（最高层）`。

分层窗格调用`public void setLayer(Component c,int layer)`可以重新设置组件c所在的层

调用`public int getLayer(Component c)`可以获取组件c所在的层数。

#### 11、数据库事务处理

事务是访问数据库的一个操作序列，数据库应用系统通过事务集来完成对数据库的存取。事务的正确执行，使数据库从一种状态转变为另一个状态。事务必须服从ACID原则（原子性（atomicity）、一致性（consistency）、隔离性（isolation）、持久性（durability））。数据库中使用事务处理会保持数据的一致性。在Java中的事务类型主要有三种，JDBC事务、JTA（Java Transaction API）事务、容器事务。这里针对数据库的事务处理具体是JDBC的事务处理，JDBC的事务处理提供了两种事务模式（自动提交和手动提交）。控制事务的方法如下：

```java
public void setAutoCommit(boolean autoCommit) //是否自动提交，autoCommit赋值为false后将不再自动提交
public boolean getAutoCommit()
public void commit()	//手动提交
public void rollback()
```

**自动提交**：它会自动完成提交，且每条语句都被当成一个事务。在Java中关于数据库的事务处理一般默认为自动提交。

**手动提交**：在提交之前的所有数据都被认为是一个事务，当这个事务中的某一条语句执行失败，事务会回滚，从而保证了数据库数据的一致性。（例如：主表和从表同时插入相关的数据，当主表或从表成功，从表或主表失败时，数据也不会出现在两张表中）。

**注意**：JDBC事务的范围局限在一个数据库连接，一个JDBC事务不能跨越多个数据库。

#### 12、广播数据报

广播数据报**MulticastSocket**是一种较新的技术，要广播或接收广播的主机都必须加入到同一个D类地址。 D类地址也称作组播地址。广播数据报涉及到地址和端口。当要发送一个数据报时，可以使用随机端口创建一个MulticastSocket,也可以在指定端口创建MulticastSocket。

**构造函数**：

|                 方法名                  |                        描述                         |
| :-------------------------------------: | :-------------------------------------------------: |
|            MulticastSocket()            | 使用本机默认地址、随机端口来创建MulticastSocket对象 |
|     MulticastSocket(int portNumber)     |        使用本机默认地址、指定端口来创建对象         |
| MulticastSocket(SocketAddress bindaddr) |       使用本机指定IP地址、指定端口来创建对象        |

#### 13、远程调用（RMI）

Java中的远程调用是允许运行在一个Java虚拟机的对象调用运行在另一个Java虚拟机上的对象的方法。这两个虚拟机可以是运行在相同计算机上的不同进程中，也可以运行在网络上的不同计算机中。RMI（Remote Method Invocation）远程方法调用，在java语言中是一种用于实现远程方法调用的应用程序的编程接口。它可以使客户机上运行的程序调用远程服务器上的对象。除此之外，RPC（Remote Procedure Call）远程过程调用，可以用于一个进程调用另一个进程。Java 的 RMI 则在 RPC 的基础上向前了一步，即提供分布式对象间的通讯。创建一个简单的RMI应用大致分为四个步骤：①创建远程接口（继承java.rmi.Remote接口）②创建远程类实现远程接口 ③创建服务器程序（创建远程对象，通过createRegistry()方法注册远程对象。并通过bind或者rebind方法，把远程对象绑定到指定名称空间（URL）中）④创建客户端程序（通过 lookup()方法查找远程对象，进行远程方法调用）。

#### 14、定时器类Timer

Java中实现定时器功能的两个类一个是Timer，另一个是TimerTask。Timer类是用来执行任务的类，它接收一个TimerTask做参数。Timer有两种执行任务的模式，一个是使用schedule执行任务，另一个是使用内部类实现run方法执行任务。

**构造方法**

|               方法名                |                             描述                             |
| :---------------------------------: | :----------------------------------------------------------: |
|               Timer()               |                      创建一个新的计时器                      |
|       Timer(boolean isDaemon)       |   创建一个新的计时器，可以指定其相关的线程作为守护线程运行   |
|         Timer(String name)          |        创建一个新的计时器，其相关的线程具有指定的名称        |
| Timer(String name,boolean isDaemon) | 创建一个新的计时器，其相关的线程具有指定的名称，并且可以指定其相关线程作为守护线程运行 |

**常用方法**

|                            方法名                            |                          描述                          |
| :----------------------------------------------------------: | :----------------------------------------------------: |
|                        void cancel()                         |               终止此计时器，放弃当前任务               |
|                         int purge()                          |       从计时器的任务队列中移除所有已经取消的任务       |
|           void schedule(TimerTask task,Date time)            |            安排定时器在指定时间执行指定任务            |
|   void schedule(TimerTask task,Date firstTime,long period)   |   安排定时器在指定时间开始重复的固定延迟执行指定任务   |
|           void schedule(TimerTask task,long delay)           |           安排计时器在指定延迟后执行指定任务           |
|     void schedule(TimerTask task,long delay,long period)     | 安排计时器从指定的延迟后开始重复的固定延迟执行指定任务 |
| void scheduleAtFixedRate(TimerTask task,Date firstTime,long period) |  安排定时器在指定的时间开始重复的固定速率执行指定任务  |
| void scheduleAtFixedRate(TimerTask task,long delay,long period) | 安排定时器在指定的延迟后开始重复的固定速率执行指定任务 |

#### 15、窗体设计的可视化界面

在Java中开发PC端窗体程序设计时，通常都使用的是纯代码开发不像开发C#窗体程序时的那么简便，那么我们如何可以在Java开发窗体程序时使用可视化界面来开发窗体程序呢？这里主要说明在Eclipse中和IDEA中的可视化窗体设计界面。

在Eclipse中，在Help中找到Instal New Software，找到之后在界面中的Work with中输入`http://download.eclipse.org/windowbuilder/WB/release/R201506241200-1/输入Eclipse的版本号/`，等待片刻后将出现的内容全部安装。安装完成后重启Eclipse，在包上右键选择New再选择Other，找到WindowBuilder文件夹，这里就可以选择你想要开发的任何窗体。完成创建后，会发现在代码文本区域下会出现两个按钮一个是`source`，这个是当前页面的代码的视图；另一个是`Design`，这个是可视化编程设计界面。这个就是我们想要的功能。

在IDEA中，不需要下载插件，这里只需要在包上右键点击选择New，再选择GUI Form。有的版本的IDEA还有一层目录，再New后找到Swing UI Designer，在其中可以创建GUI Form填写必要的数据后即可实现可视化窗体设计。不过这个没有Eclipse中的可视化窗体设计插件好用。这里推荐一个需要安装的插件`JFormDesigner`，但是需要破解。下载地址：[JFormDesigner](https://plugins.jetbrains.com/plugin/274-jformdesigner)。

#### 16、try-with-resources语句

这个语句是从JDK7之后提供的，对于资源类的一系列的处理操作（关闭数据库、关闭流等）以前是放在try-catch-finally的finally中处理的，而JDK7开始，引入了AutoCloseable接口和try-with-resources语句，只要资源类继承了AutoCloseable，就可以不再显式的关闭资源了。这部分操作交由JVM来自动完成资源的释放。它和try-catch-finally的区别，在try后增加了一对小括号`try()`，在括号中完成资源初始化的操作，此时只需要将该类继承AutoCloseable即可实现资源的自动释放。

#### 17、装箱、枚举

**装箱**：就是将一个基本类型直接赋值给引用类型。

**拆箱**：是把一个引用类型赋值给基本类型。

**枚举**：它是一种特殊的class类型，用法和其他语言的枚举类似，如：`enum Light{Red,Yellow,Blue};Light l=Light.Red`。可以在枚举的定义体中，添加字段、方法、构造方法

#### 18、重抛异常与异常链接

对于异常有时还需要将此异常进一步传递给调用者，以便让调用者也可以感受到这种异常。这时还可以在catch语句块或finally语句块中采取以下三种方式：

①将当前捕获的异常再次抛出`throw e`

②重新生成一个异常，并抛出`throw new Exception("message")`

③重新生成并抛出一个异常，该异常中包含了当前异常的信息`throw new Exception("message",e);e.getCause()`，使用该方法可以得到内部异常。当程序出错时，该方法的解决效率和分析效率是最优的，因为它针对异常来进行剖析，最终得到的错误信息是最接近准确值的。

#### 19、断言

**语法格式**：

```java
assert 表达式;
//或者
assert 表达式:信息;
```

当调试程序时，如果表达式不为true，则程序会产生异常，并输出相关的错误信息。在运行时要想使assert起作用，必须在java命令中使用选项-ea即-enableassertions。例如：`java -ea -classpath . Assert`。

断言是用来在JDK1.4之后进行程序调试的一种手段，就目前而言它在使用时不如当前的JUnit好用，所以在当前环境下进行程序调试最好使用JUnit单元测试。

#### 20、stream流

流操作分为两类，一类是中间的流操作：中间的操作保持流打开状态，并允许后续的操作。例如：`filter sorted limit map等`。另一类是末端的流操作：末端的操作必须是对流的最终操作。例如：`max min count forEachfindAny等`。

流操作的步骤：①从某个源头获得一个流。②执行一个或更多的中间操作。③执行一个末端的操作。

数组获取流：`Arrays.stream(arr)`，collection及List获取流：`list.stream()`。**注意**：Map没有流，但是提供了类似的方法，例如：`map.putIfAbsent map.computeIfPresent map.merge`。

实现流的并行计算，只需要将`.stream()`换成`.parallelStream()`就可以实现并行计算。

**中间的流操作**

|              方法名              |                          描述                          |
| :------------------------------: | :----------------------------------------------------: |
|              filter              |               排除所有与断言不匹配的元素               |
|               map                |           通过Function对元素执行一对一的转换           |
|             flatMap              |      通过FlatMapper将每个元素转变为无或更多的元素      |
|               peek               |              对每个遇到的元素执行一些操作              |
|             distinct             |           根据.equals行为排除所有重复的元素            |
|              sorted              | 确保流中的元素在后续的操作中，按照比较器决定的顺序访问 |
|              limit               |         保证后续的操作所能看到的最大数量的元素         |
|            substream             |    确保后续的操作只能看到一个范围的（根据index）元     |
|               skip               |                      忽略一些元素                      |
| mapToDouble  mapToInt  mapToLong |                        类型转换                        |

**末端的流操作**

|  方法名   |                        描述                         |
| :-------: | :-------------------------------------------------: |
|  forEach  |            对流中的每个元素执行一些操作             |
|  toArray  |             将流中的元素倾倒入一个数组              |
|    min    |         根据一个比较器找到流中元素的最小值          |
|    max    |         根据一个比较器找到流中元素的最大值          |
|   count   |                 计算流中元素的数量                  |
| anyMatch  |         判断流中是否至少有一个元素匹配断言          |
| allMatch  |          判断流中是否每一个元素都匹配断言           |
| noneMatch |          判断流中是否没有一个元素匹配断言           |
| findFirst |                查找流中的第一个元素                 |
|  findAny  | 查找流中的任意元素，可能对某些流要比findFirst代价低 |

### 补充/技巧

1、变量在栈，引用在堆。（针对内存而言）

2、通过**JNI**可以在Java中调用其他语言所写的代码。

3、**static,private,final**修饰的方法不是虚方法。 

4、在import后跟一个static实际上是导入一个类，在使用时无需创建对象实例即可调用。

5、catch多个异常时，子类异常要排在父类异常的前面。

6、如果不覆盖`equals()`方法，则equals()方法与`==`的结果是一样的。

7、String对象中所包装的内容是不可改变的（immutable）。

8、一般在覆盖时，要同时覆盖hashCode、equals方法。

9、`ConcurrentHashMap`类是线程安全的类。

10、虚方法调用不是指编译时就决定了调用哪个类中的哪个方法，它是实现了运行时的多态。

11、abstract类可以有不包含abstract修饰的方法。

12、如果省略访问控制符，则表示该类只能被自己的包中的类访问。

13、static函数中不可以使用this关键字。

14、静态初始化先于实例初始化执行。

15、内部类中可以访问外部类的private字段及方法。

16、方法签名不包括参数的类型及参数的名字，方法声明的两个组件构成了方法签名（方法的名称和参数类型）。

17、Java中没有非零即真的说法。

18、Double对象中所包装的值是不可改变的。

19、在线程中更新图形化界面，要调用`SwingUtilites.invokeLater（）`方法。  

20、处理流的构造方法总是要带一个其他的流对象作参数。  

21、Java中引用类型在赋值时，复制的不是对象实体。

22、构造方法没有返回值，但实际上是隐式返回的类的对象。

23、后台线程会自动结束。

24、Integer I = 5 实际上表示的是Interger.paresInt(new Interger(5)）。

25、序列化不是将对象读入到内存中。

### 算法补充

#### 埃氏筛法

埃氏筛法（埃拉托斯特尼筛法）是一种**求素数**的算法。它的原理非常简单。例如求2～100以内的素数，在这个范围内，首先先去掉2的倍数，其次再去掉3的倍数，再次去掉5的倍数，……依此类推，最后剩下的就是素数。它的算法复杂度是O(n*log(log n))。这里使用最简单的方式来填充数组，将boolean类型的数组构造成素数表，true表示素数，false表示不是素数，当对某个范围内的整数进行遍历时只需要对非素数的数组下标位置的值标记为false即可，核心代码如下：

```java
boolean[] primeTable=new boolean[100];
Arrays.fill(primeTable, true);//假设max以下的所有数全部是素数
//根据算法0和1是无法根据倍数剔除，所以要对max以下的0和1进行处理，先声明其不是素数
primeTable[0] = primeTable[1] = false;
//外层循环，循环控制条件为sqrt(max)，对max以下的数进行循环
for (int i = 2; i <= Math.sqrt(max); i++) {
	if (primeTable[i]) {//若i是素数则剔除它的倍数
     //内层循环，遍历i的倍数
		for (int j = i * i; j < max; j = j + i) {
			primeTable[j] = false;
        }
     }
}
return primeTable;
```

对于普通的筛法求素数而言，埃氏筛法在运行时的速度要快一大截，因为它不用去一个数一个数的遍历查找。但是埃氏筛法并不是运算效率最高的算法，在众多筛法中还有欧拉筛法，它的运算效率要比埃氏筛法还要高。对于求成百上千的素数而言，使用埃氏筛或欧拉筛明显要好于其他的筛法。

