## Java基础——问题记录（二）

### 问题记录

#### 问题 一：在Java中什么是虚方法?

**答**：Java中的虚方法可以简单理解为Java里所有被**overriding**的方法都是虚方法，所有重写的方法都是**overriding**。在`JVM`字节码执行引擎中，方法调用会使用`invokevirtual`字节码指令来调用所有的虚方法。虚方法调用，可以实现运行时的多态。在Java中，普通的方法是虚方法，但是**static/private**方法不是虚方法调用。

#### 问题 二：多态、重构

**答**：**多态**是指一个程序中的某个方法有相同的名字却拥有不同的含义。多态有两种（**编译时多态**/**运行时多态**），**编译时多态**是重载多个同名的不同方法。**运行时多态**有两个特点（覆盖、动态绑定），覆盖是子类对父类方法进行覆盖，动态绑定也称为虚方法的调用，真正该用什么方法在运行时才能确定。**重构**是通过调整程序代码改善软件的质量、性能，使其程序的设计模式和架构更趋合理，提高软件的扩展性和维护性。

#### 问题 三：Java中什么是上溯造型？

**答**：上溯造型是指将派生类当作基本类型处理。例如：

```java
Animal animal=new Cat();
void run(Animal animal)
{
……
};
run(new Animal());
```

其实就是父类引用指向子类对象，上面这个例子中就是如此，声明的父类引用`animal`指向的其实是子类对象的实例化。那么，下塑造型就是子类引用指向父类对象。

#### 问题 四：Java中什么是方法签名？

**答**：在Java中方法声明的两个组件构成了方法签名（方法的名称和参数类型）。例如`public void fun();`它的方法签名就是`fun()V`。比较特殊的参数类型的特殊字符`Z-boolean、J-long、'['开头配合其他的特殊字符表示什么类型的数组，几个'['代表几个数组-数组、L开头;结尾，中间是引用类型的全类名-其他类`。

#### 问题 五：Java中什么是序列化和反序列化？

**答**：**序列化**是指把堆内存中的Java的对象数据通过某种方式把对象存储在磁盘文件中或者传输到其他的网络节点。其实就是将数据结构或对象转换成二进制串的过程。**反序列化**是把磁盘中的对象数据或网络节点中的对象数据恢复成Java对象模型的过程。也就是将二进制串转换成数据结构或者对象的过程。只要是需要将对象在网络上传输或者需要共享数据的JavaBean对象，都得做序列化处理。在Java中对需要做序列化处理的类都必须实现序列化接口`Serializable`，该接口不需要实现任何方法。在Java中大部分方法都实现了该接口，当需要实现序列化的类实现了该接口，那么在底层的时候会进行判断，如果实现了该接口，就会允许做实例化。在Java中使用对象流来完成序列化和反序列化。

#### 问题 六：什么是不定长参数？

**答**：在Java中不定长参数是从JDK1.5之后才有的。通常用省略号表示，且是最后一个参数。实际上Java将其当作一个数组进行处理。例如：`int Sum(int … nums){int sum=0;for(int n:nums){sum+=n;}return sum;}`调用时`Sun(1,2,3,4);`。

#### 问题 七：构造方法的初始化、实例初始化与静态初始化的执行步骤

**答**：静态初始化总是先于实例初始化执行，而实例初始化先于构造方法{}中的语句执行。

#### 问题 八：为什么系统知道对象是否为垃圾？

**答**：在Java中存在垃圾回收机制，我们不需要使用其他的方法对对象进行释放，完全可以交给JVM中的垃圾回收线程来完成。 而JVM判断对象是否为垃圾的一个机制就是对象的引用计数器。任何对象都有一个引用计数器，当其值为0时，说明该对象可以回收。此时，JVM就会对对象进行垃圾回收。

#### 问题 九：创建线程的三种方法

**答**：①通过继承Thread类创建线程。②通过向Thread()构造方法传递Runnable对象来创建线程，也就是实现Runnable接口。③通过Callable和Future创建线程。

#### 问题 十：Java中的原子类是什么？

**答**：**原子操作**是指在一行代码中，这行代码的操作是不可拆分的，这时这个操作就叫做原子操作（比如：给变量一个初始值等）。**非原子操作**是指某个操作是可以拆分的操作，例如`i++`可以拆分成`i=i+1`。非原子操作会存在线程安全问题，这时需要我们使用同步将该操作变成一个原子操作。一个操作是原子操作，就称该操作具备原子性。在Java中的`concurrent`包下提供了一些原子类，在对一个不具备线程安全性的代码进行修改时，除了使用`synchroinzed`关键字实现同步外，还可以使用原子类进行实现。`java.util.concurrent.atomic`包下提供了一系列的原子类，它们都是线程安全的。（例如：`AtomicBoolean `[原子布尔类型]、`AtomicIntegerArray `[原子整型数组]等）。

****

### 常用方法补充

#### 1、this关键字的几种用法

this关键字的使用主要有**三个地方**：

+ ①在方法及构造方法中，使用this来访问字段及方法。
+ ②使用this解决局部变量与域同名的问题。
+ ③在构造方法中用this调用另一个构造方法，但是这个语句必须放在构造方法的第一句。

#### 2、super关键字的几种用法

super关键字的使用主要有**两个地方**：

+ ①使用super访问父类的域和方法。
+ ②使用父类的构造方法，其语句必须放在第一句。

**注意**：super和this不能同时使用，this用于调用重载的构造器，super用于调用父类被子类重写的方法。因为this和super都指的是对象，所以不可以在static环境中使用。

#### 3、声明一个方法为static的含义

+ ①非static的方法是属于某个对象的方法，在这个对象创建时，对象的方法在内存中拥有自己专用的代码段。而static的方法是属于整个类的，它在内存中的代码段将随着类的定义而进行分配和装载，不被任何一个对象专有。
+ ②由于static方法是属于整个类的，所以它不能操纵和处理属于某个对象的成员变量，而只能处理属于整个类的成员变量，即static方法只能处理本类中的static域或调用static方法。
+ ③static方法中，不能访问实例变量，不能使用this 或super。
+ ④调用这个方法时，应该使用类名直接调用，也可以用某一个具体的对象名。

#### 4、注解

注解又称为注记、标记、标注、注释（不同于comments），它是在各种语法要素上加上附加信息，以供编译器或其他程序使用。常用的注解有`@Override、@Deprecated、@SuppressWarnings`，`@Deprecated`表示过时的方法、`@SuppressWarnings`表示让编译器不产生警告。除了内置的一些注解外，还可以自定义注解，语法格式：`public @interface Author{String name();}`。注解的生命周期有三个阶段（Java源文件阶段、class文件阶段、运行期阶段）

JDK内置的部分注解如下：

+ `@Override`——表示覆盖
+ `@Deprecated`——表示过时的方法
+ `@SuppressWarnings({"unchecked","deprecation"})`——表示不产生警告信息
+ `@Target`——表示限定某个自定义注解能够被应用在哪些Java元素上面的
+ `@Retention`——表示修饰自定义注解的生命周期，这里面有三个参数（`RetentionPolicy.SOURCE`表示生命周期是Java源文件阶段，该注解即不会参与编译也不会在运行期起任何作用；`RetentionPolicy.CLASS`表示生命周期是class文件阶段，该注解将被编译到Class文件中编译器可以在编译时根据注解做一些处理动作但是运行时JVM会忽略它；`RetentionPolicy.RUNTIME`表示生命周期是运行期阶段，该注解可以在运行期的加载阶段被加载到Class对象中。在程序运行阶段，可以通过反射得到这个注解，并通过判断是否有这个注解或这个注解中属性的值，从而执行不同的程序代码段。）
+ `@Documented`——表示自定义注解是否能随着被定义的java文件生成到JavaDoc文档中

注记的定义：使用`@interface`来定义一个类型，表示它是一个注记，**使用方法名()**表示它的一个属性(值或数组)，其中`value()`是默认属性，使用default表示其默认值。

当定义一个注记时，`@Target(ElementType.METHOD)` 注记——表明可以用于方法上，`@Retention(RetentionPolicy.RUNTIME)`注记——表明可以用反射来读取，`@Documented`注记——表明它会生成到javadoc中。

**自定义注记**示例如下：

```java
@interface DebugTime{
	boolean value() default true;
	long timeout() default 100;
	String msg();
	int[] other() default {};
}
```

注记的使用：`@注记名(属性=值,属性={值,值})`
使用注记示例如下：

```java
class MyClass{
	@DebugTime(value=true, timeout=10, msg="时间太长", other={1,2,3})
	public double fib(int n){
		if(n==0||n==1) 
			return 1; 
		else 
			return fib(n-1)+fib(n-2);
	}
}
```

**注意**：在自定义注解时，定义注解类型元素时需要注意以下几点，①访问修饰符必须为public，不写默认为public。②元素的类型只能为基本数据类型、String、Class、枚举、注解以及数组。③如果注解中只有一个元素，该元素名最好为value。④元素后面的括号中不能含有参数。⑤`default`代表默认值。如果没有默认值，则在使用该注解时必须给该元素赋值。

#### 5、多线程的同步问题

当同时运行的线程需要共享数据就必须考虑其他线程的状态与行为，这时就需要实现同步。在Java中引入了对象**互斥锁**的概念，以此来保证共享数据操作的完整性。每个对象都对应于一个monitor，它上面的一个称为**互斥锁（lock, mutex)**的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。通过使用关键字**synchronized**来与对象的互斥锁进行联系。关键字**synchronized**的用法有两点：

+ ①对于代码片段使用**synchronized**关键字，`synchronized(对象){……}`
+ ②对于某个方法使用**synchronized**关键字，**synchronized**放在方法声明中，表示整个方法为同步方法。

对于线程的同步控制问题，使用`wait()`方法可以释放对象锁，使用`notify()`或`notifyAll()`可以让等待的一个或所有线程进入就绪状态。在Java中可以将**wait**和**notify**放在**synchronized**里面，在**synchronized**代码被执行期间，线程调用对象的`wait()`方法释放对象锁标志，然后进入等待状态由其它线程调用`notify()`或者`notifyAll()`方法通知正在等待的线程。对于Java中的锁机制，在JDK1.5之后增加了更多的类，便于灵活的操作它们。例如：（`Lock接口、ReentrantLock类、ReadWriteLock接口、ReentrantReadWriteLock类等`），几个实用的类（单变量、集合、Timer、线程池）。

##### （1）并发的集合类

|                   类名                    |                     描述                     |
| :---------------------------------------: | :------------------------------------------: |
| CopyOnWriteArrayList、CopyOnWriteArraySet |        适合于很少写入而读取频繁的对象        |
|             ConcurrentHashMap             | 包含putIfAbsent()、remove()、replace()等方法 |
|            ArrayBlockingQueue             |      生产者与消费者，使用put()及take()       |

##### （2）线程池

**常见的类如下**

|                   类名                    | 描述 |
| :---------------------------------------: | :--: |
| ExecutorService接口、ThreadPoolExecutor类 |      |
|             Executors 工具类              |      |

**常见的用法**

`ExecutorServicepool = Executors.newCachedThreadPool();` 使用其`execute( Runnable r)`方法

**注意：**在线程中更新图形化界面，要调用`SwingUtilites.invokeLater`方法。

#### 6、显式锁

|                    类名                     |                     描述                     |
| :-----------------------------------------: | :------------------------------------------: |
|          Lock接口、ReentrantLock类          |       包含`lock() tryLock() unlock()`        |
| ReadWriteLock接口、ReentrantReadWriteLock类 | 包含`writeLock().lock() readLock().unlock()` |

#### 7、异步编程

异步简而言之就是在同一时间段发生两个操作，当没有异步时，当一个操作完成后另一个才会开始进行，但是我们需要将两个操作同时进行，这时就需要异步操作，实现异步后两个操作就会在同一时间段开始执行，从而减少了系统的时间开销。异步编程里比较常用的就是`Executor`与`Future`，`Future`时异步取得结果。

当`Executor`执行与任务分开，使用线程池。在一定意义上可以实现异步编程。

#### 8、队列（Queue）

队列遵循"先进先出（FIFO）"的原则，固定在一端输入数据（称为入队），另一端输出数据（称为出队）。在Java中，队列的重要实现是LinkedList类。队列是一种特殊的线性表，它只允许在表的前端进行删除操作，在表的后端进行插入操作。LinkedList类实现了Java中的Queue接口，所以我们可以将LinkedList当作队列来使用。

#### 9、栈（Stack）

栈遵循"后进先出（LIFO）"的原则，它是一个重要的线性数据结构。其中栈包含三个方法，具体方法如下：

|             方法名              |                    描述                    |
| :-----------------------------: | :----------------------------------------: |
| public Object push(Object item) |             将指定对象压入栈中             |
|       public Object pop()       | 将栈最上面的元素从栈中取出，并返回这个对象 |
|     public boolean empty()      |            判断栈中没有对象元素            |

#### 10、流

Java中的流可以分为字符流和字节流，字节流包括（InputStream、OutputStream），字符流包括（Reader、Writer）。

+ **Reader类常用方法**

  |                   方法名                    |
  | :-----------------------------------------: |
  |              public int read()              |
  |          public int read(char b[])          |
  | public int read(char[] b, int off, int len) |

+ **Writer类常用方法**

  |                     方法名                      |                        描述                         |
  | :---------------------------------------------: | :-------------------------------------------------: |
  |            public void write (int b)            |           将参数 b 的低两字节写入到输出流           |
  |          public void write (char b[])           |     将字符数组 b[] 中的全部字节顺序写入到输出流     |
  | public void write(char[] b, int off, int len )  | 将字节数组 b[] 中从 off 开始的 len 个字节写入到流中 |
  |          public void write( String s)           |                  将字符串写入流中                   |
  | public void write( String s, int off, int len ) |     将字符串写入流中 , off 为位置， len 为长度      |
  |              public void flush ()               |                       刷新流                        |
  |               public void close()               |                       关闭流                        |

除此之外，还可以将流分为节点流和处理流，节点流可以从或向一个特定的地方（节点）读写数据，例如（FileInputStream、ByteArrayInputStream等）。处理流是对一个已经存在的流的连接和封装，处理流又称为过滤流，例如（缓冲处理流BufferedReader）。通常，在使用处理流时，它的构造方法总是要带一个其他的流对象作为参数。这种称之为流的链接。

**常用的节点流**

|       节点类型        |                     字节流                     |               字符流                |
| :-------------------: | :--------------------------------------------: | :---------------------------------: |
|       File 文件       |      FileInputStream<br/>FileOutputStream      |      FileReader<br/>FileWriter      |
| Memory Array 内存数组 | ByteArrayInputStream<br/>ByteArrayOutputStream | CharArrayReader<br/>CharArrayWriter |
| Memory String 字符串  |                                                |    StringReader<br/>StringWriter    |
|       Pipe 管道       |     PipedInputStream<br/>PipedOutputStream     |     PipedReader<br/>PipedWriter     |

**常用的处理流**

|                        处理类型                         |                    字节流                    |                  字符流                  |
| :-----------------------------------------------------: | :------------------------------------------: | :--------------------------------------: |
|                     Buffering 缓冲                      | BufferedInputStream<br/>BufferedOutputStream |    BufferedReader<br/>BufferedWriter     |
|                     Filtering 过滤                      |   FilterInputStream<br/>FilterOutputStream   |      FilterReader<br/>FilterWriter       |
| Converting between bytes and character 字节流转为字符流 |                                              | InputStreamReader<br/>OutputStreamWriter |
|             Object Serialization 对象序列化             |   ObjectInputStream<br/>ObjectOutputStream   |                                          |
|            Data conversion 基本数据类型转化             |     DataInputStream<br/>DataOutputStream     |                                          |
|                    Counting 行号处理                    |            LineNumberInputStream             |             LineNumberReader             |
|                 Peeking ahead 可回退流                  |             PushbackInputStream              |              PushbackReader              |
|                   Pinting 可显示处理                    |                 PrintStream                  |               PrintWriter                |

对字符的读写时，需要注意字符的编码格式（如：UTF-8、ASCII、GB2312、默认编码等）。对对象的读写使用（ObjectInputStream、ObjectOutputStream）。对基本数据的读写使用（DataInputStream、DataOutputStream）。

#### 11、RandomAccessFile

RandomAccessFile类似于C语言的文件操作，可以实现对文件的随机读写操作。

**常用的构造方法**

|                   方法名                   |                       描述                       |
| :----------------------------------------: | :----------------------------------------------: |
| RandomAccessFile (String path,String mode) | 当文件不存在时创建文件，或者打开文件，并指定操作 |
|   RandomAccessFile (File f,String mode)    | 当文件不存在时创建文件，或者打开文件，并指定操作 |

这里的mode和C语言类似，有`r、rw、rwd、rws`等。

定位方法：public void seek (long pos）

读方法：readBealoon()、readChar()、readInt()、readLong()、readFloat()、readDouble()、readLine()、readUTF()等

写方法：writeBealoon()、writeChar()、writeInt()、writeLong()、writeFloat()、writeDouble()、writeLine()、writeUTF()等

#### 12、Java中的网络编程

在Java中可以使用java.net.URL进行网络信息的获取。读取一个网页文件的内容的步骤如下：

+ ①创建一个URL类型的对象，`URL url=new URL("www.baidu.com");`
+ ②利用URL类的openStream()，获得对应的InputStream类的对象。如：`InputStreamstream = url.openStream();`
+ ③通过InputStream或InputStreamReader来读取内容。

除此之外，还可以使用第三方库进行更复杂的网络信息的获取。

#### 13、Java中的多媒体编程

在Java中可以使用绘图来实现多媒体编程，绘图可以使用Graphics类及其子类Graphics2D。通常获得Graphics对象常有两种方法：

+ ①使用组件的`getGraphics()`方法来获得Graphics对象
+ ②用Canvas及JComponent对象来进行绘图，例如：`可以重载Cavans的paint(Graphics g)方法或者使用JPanel的paintComponent(Graphics g) 方法`这两个方法会带一个Graphics参数。

除此之外，还可以使用一些辅助类来进行多媒体编程，例如：（`Point`表示一个位置点、`Dimension`表示宽和高、`Rectangle`表示一个矩形、`Polygon`表示一个多边形、`Color`、`Font`）。我们可以使用Graphics类的drawImage()方法显示图像，使用此方法可以显示一个我们喜欢的图片，此外我们还可以使用BufferedImage对象的getGraphics() 可以得到一个绘图对象，通过此对象使得我们可以得到一个我们喜欢的图形在界面上。当然，我们也可以使用一些外部的API接口来进行多媒体编程。例如（JAI、JMF、Java 3D等）。

#### 14、卫语句

卫语句就是将复杂的条件表达式拆分成多个条件表达式，以达到优化多层的if-else嵌套。当程序使用了卫语句后有个明显的特点就是整个代码看起来比较清晰明了。减少了对复杂逻辑的理解。

对卫语句比较经典的解释出自《重构——改善既有代码的设计》一书，具体解释如下。

> 如果条件语句极其复杂，就应该将条件语句拆解开，然后逐个检查，并在条件为真时立刻从函数中返回，这样的单独检查通常被称之为“卫语句”（guard clauses）。                                                                                                       ——《重构——改善既有代码的设计》

其实只要在代码书写的过程中，尽量减少废话和重复的话，我认为此时的代码就足够简洁明了了。

#### 15、经典的23种设计模式的总结

|          |                       创建型Creational                       |                       结构型Structural                       |                       行为型Behavioral                       |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  **类**  |                   工厂方法(Factory Method)                   |                       适配器(Adapter)                        |      解释器(Interpreter)<br/>模板方法(Template Method)       |
| **对象** | 抽象工厂(Abstract Factory)<br/>生成器(Builder)<br/>原型(Prototype)<br/>单态(Singleton) | 适配器(Adapter)<br/>桥接(Bridge)<br/>组成(Composite)<br/>装饰(Decorator)<br/>外观(Facade)<br/>享元(Flyweight)<br/>代理(Proxy) | 责任链(Chain of Responsibility)<br/>命令(Command)<br/>迭代器(Iterator)<br/>中介者(Mediator)<br/>备忘录(Memento)<br/>观察者(Observer)<br/>状态(State)<br/>策略(Strategy)<br/>访问者(Visitor) |

**设计模式的原则**：

1. 单一职责原则

   要把功能尽可能的细分，每一个类应该只负责一块内容或只执行一个任务。那么怎么样才算达到单一职责了呢，那就是当一个类仅有一个引起它变化的原因时。

2. 开放封闭原则

   尽量不要去修改原有的类，但却可以扩展现有的功能。

3. 替换原则

   子类必须能够替换它们的基类。

4. 依赖倒置原则

   高层模块不应该依赖于低层模块，二者都应该依赖于抽象；抽象不应依赖于实现细节，实现细节应该依赖于抽象。

5. 接口隔离原则

   客户端不应该依赖它不需要的接口；一个类对另一个类的依赖应该建立在最小的接口上。

#### 16、反射

反射就是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。在一些框架性的程序中，反射是相当重要的。与Java中的反射机制相关的类如：Class、Field、Method、Constructor类。

#### 17、一个JavaWeb项目的目录结构

一般的Java项目分为四层分别是数据操作层（Dao）、业务处理层（Service）、逻辑判断层（Servlet）、表现层（Jsp）。

##### （1）Common

**作用**：用来封装一些常用的公共方法。

##### （2）Dao

**作用**：**Dao**又称为数据访问层，主要用来封装对数据库的增、删、查、改。

##### （3）Service

**作用**：**Service**又称为服务层，比Dao层要高一个层级，它主要用来将多种方法封装起来。

##### （4）Po

**作用**：将数据库表中的记录放在Java对象中，一个Po就是一个数据库表的记录。

##### （5）Vo

**作用**：包含数据，一般用来存储数据和传递数据。

##### （6）Util

**作用**：封装一些实用的方法和数据结构。

##### （7）Web

**作用**：可以放一些Servlet接口，通常实现HttpServlet接口，它能够处理HTTP请求的servlet，它在原有Servlet接口上添加了一些与HTTP协议处理方法，比Servlet接口的功能更为强大。

**补充**：对于一个基于Java开发的窗体程序中，`app`包下放程序的驱动，也就是主运行函数。`controller `主要是对外的接口。

一般来说，一个优秀的Java项目，必须要有一个明了的项目目录结构，比较常见的目录结构有两种（MVC模式和DDD模式）。MVC适合简单的Java项目，DDD适合大型的Java项目，MVC模式其实就是传统的三层架构（数据访问层、业务逻辑层、表现层）。

此外，对于不同接口的实现也有对应的包，一般起名为Impl，专门用来实现接口。在对应的Web项目中，还会有Webapp文件夹，该文件夹下还有几个比较重要的文件夹（如：js、css、Web-INF），Web-INF下面主要存放项目的外部依赖（放在lib下）、所有页面文件（放在page下）以及项目的配置文件**web.xml**，通常我们在使用Maven工程构建一个web项目的时候会有这些的目录结构。当我们使用SSM框架来构建项目时，还会多几个文件夹专门用来存放MyBatis的配置文件、资源文件等。

#### 18、Lambda 表达式

该表达式是Java8的新特性，在某种意义上来讲，它其实是匿名类的一个实例对象，目的是为了使实例化对象的代码更加简洁。例如在事件处理方面可以将以前的事件监听方法段简写成短短的一小段，代码如下：

```java
btn.addActionListener(e->{……});
```

Lambda表达式是接口或接口函数的简写。由于Lambda只能表示一个函数，所以能写成Lambda的接口要求包含且最多只能有一个抽象函数，这样的接口可以使用注记来标明是函数式接口(**@FunctionalInterface**)。

**基本语法**：`参数->结果`，例如：`String s -> s.length()`或`x -> x*x`。

#### 19、Oracle 数据库要点

oracle数据库和其他主流的数据库类似，在语法方面略有不同。在DOS下进入Oracle数据库的方法如下：在控制台界面输入`sqlplus`，大多数情况下的用户名为`system`，密码是自己设定的密码。这里和mysql数据库有所区别。

##### （1）数据类型

**number(P,S)** P：数据的最大位数，S：小数点后的最大位数；

**char(N)** 固定长度的字符串，N占用的字节数，最大是2000字节；

**varchar2(N)** 可变长度的字符串，N最多可以占用的字节数，最大为4000字节，Varchar2和varchar是同义词，Varchar2是oracle自己设计的数据类型；

**date** 日期类型，占用7个字节，日期默认的格式是“DD-MON-RR”，例如：09-SEP-20；

##### （2）语法

`default` 添加到某一行，表示默认值，如：`default ‘男’`；`desc` 查看表结构；`rename` 修改表名，如`rename emp1 to emp2`；

`add `给表增加一个字段，但是只能加到最后一行，不能再原有列之间插入，如：`alter table emp add(hirDate date)`；

`modify`修改列，如：`alter table emp modify(birth date default sysdate)`；

`modify`修改列，如：`alter table emp modify(birth date default sysdate)`；

序列：用来生成唯一数字值的数据库对象。创建序列`create sequence emp_seq;`(1,2,3，.....)或`create sequence emp_seq start with 100 increment by 10;`；

 `emp_seq.nextval`获取序列中的值

` drop sequence emp_seq;`删除序列

关于在Java中使用Oracle数据库，在书写Url时会略有些不同，java连接oracle数据库的url可以写成`jdbc:oracle:thin:@127.0.0.1:1521:XE`，其中thin代表瘦连接，它是连接数据库的模式，还有一种是oci；@127.0.0.1代表数据库所在的位置，1521是端口号，XE是数据库的名称。根据这些格式可以指定任何一个oracle数据库。和mysql相同，在使用oracle数据库时，也需要引入外部依赖。

#### 20、使用jar命令打包

对编译好的java文件，可以使用java自带的打包工具jar来将一系列的java文件打包成jar文件，具体的使用方法如下：

在Windows平台下使用命令行工具输入`jar cvfm ....`命令即可将编译好的java文件打包成jar文件，`cvfm`参数解释`c——表示创建，v——表示显示详情，f——表示指定文件名，m——表示清单文件`，例如：`jar cvfm A.jar A.man A.class`其中A.man是清单文件。该方式一般是用来对一个程序所关联的一系列的java文件进行打包，打包完成后可以使用`java -jar A.jar`来运行程序。

清单文件：该文件是用来指明一系列的java文件中，哪一个是含有主运行类的文件以及该文件所在的具体路径，清单文件可以任意命名，常见的是用`MANIFEST.MF`做清单文件的文件名。

****

### 补充/技巧

1、Java的访问控制符控制的访问权限。

| 访问控制符 | 同类 | 同包子类 | 同包其他类 | 跨包子类 | 跨包其他类 |
| :--------: | :--: | :------: | :--------: | :------: | :--------: |
|   public   |  √   |    √     |     √      |    √     |     √      |
|  private   |  √   |    ×     |     ×      |    ×     |     ×      |
| protected  |  √   |    √     |     √      |    √     |     ×      |
|    默认    |  √   |    √     |     √      |    ×     |     ×      |

2、Java中的枚举是用class来实现的，可以复杂地使用。

3、构造方法的执行过程：先父类构造，再本类成员赋值，最后执行构造方法中的语句。

4、用static修饰内部类表明该内部类实际是一种外部类。

5、在JDK1.5之前**ArrayList/HashMap**不是线程安全的，Vector及Hashtable是线程安全的。产生一个线程安全的集合对象，`Collections.synchronizedArrayList(list)`。

6、Map类有两个重要的实现，一个是HashMap类，另一个是TreeMap类。TreeMap类使用了红黑树的算法。

7、一个流对象经过其他流的多次包装，称为流的链接。

8、for-each语句不能用于所有的Enumerable对象。

9、Queue的主要方法不包括push及pop等。

10、JPanel的 paintComponent() 方法不带Graphics2D参数。  

11、如果一个类被final修饰符所修饰和限定，说明这个类不能被继承，即不可能有子类。final修饰符所修饰的方法，是不能被子类所覆盖的方法。

12、abstruct关键字不能与final、private、static关键字共存。抽象方法只可以被public和protected修饰。final可以修饰类、方法、变量，分别表示该类不可以继承、该方法不可以重写、该变量为常量。static和final一块修饰的方法，表示该方法是静态的不可重写的方法。private修饰的方法，表示私有的方法，本类可以访问，外界不能访问。

13、当一个对象成为垃圾后，不会立刻被收集掉。一个对象成为垃圾是因为不再有引用指着它，但是线程并非如此。线程的释放是在run()方法结束后，此时线程的引用可能还未被释放。

14、内部类可以有四种访问权限，可以将内部类理解成类的成员，成员有几种内部类就有几种。

15、在多线程中x=y、x++、++x需要进行同步，而x=1不需要。x=y因为y的值不确定，所以要进行加锁处理。x++和++x必须要加锁，因为他们会先被读入寄存器然后再进行+1操作，若不进行加锁，有可能会出现数据异常。而x=1是一个原子操作，不需要进行加锁同步。Java中的原子操作包括除long和double之外的基本类型的赋值操作，以及所有引用reference的赋值操作和java中自带的原子类的一切操作。

16、枚举、静态内部类、双检索模式、饿汉式实现的单例是线程安全的。Java中四种线程安全的单例模式的实现方式，                            第一种**饿汉模式**，代码示例如下：

```java
public class HungryMode {
    private static HungryMode instance = new HungryMode();

    private HungryMode() {
        System.out.println("Hungry:" + System.nanoTime());
    }

    public static HungryMode getInstance() {
        return instance;
    }
}
```

第二种**懒汉模式**，代码示例如下：

```java
public class LazyMode {
    private static LazyMode instance = null;

    private LazyMode() {
        System.out.println("LazyMode:" + System.nanoTime());
    }

    public static synchronized LazyMode getInstance() {
        if (instance == null) {
            instance = new LazyMode();
        }
        return instance;
    }
}
```

第三种**改进的懒汉模式**，代码示例如下：

```java
public class ImproveLazyMode {
    private volatile static ImproveLazyMode instance = null;

    private ImproveLazyMode() {
        System.out.println("ImproveLazyMode:" + System.nanoTime());
    }

    public static ImproveLazyMode getInstance() {
        if (instance == null) {
            synchronized (ImproveLazyMode.class) {
                if (instance == null) {
                    instance = new ImproveLazyMode();
                }
            }
        }
        return instance;
    }
}
```

第四种**私有的内部工厂方法**，代码示例如下：

```java
public class InsideFactory {
    private InsideFactory() {
        System.out.println("InsideFactory:" + System.nanoTime());
    }

    public static InsideFactory getInstance() {
        return InsideSingletonFactory.insideFactoryInstance;
    }

    private static class InsideSingletonFactory {
        private static InsideFactory insideFactoryInstance = new InsideFactory();
    }
}
```

17、不能用来修饰interface的有private、protected、static。

18、Java网络编程API建立再Socket基础之上，且支持IP以上的所有高层协议。

19、存根（Stub）与动态链接技术有关。

20、Java有八种基本类型，byte、short、int、long、float、double、boolean、char对应的所占字节数为12484812。

### 算法补充

#### 欧拉筛法

欧拉筛法的基本思想是在埃氏筛法的基础上，让每个合数只被它的最小质因子筛选一次，以达到不重复的目的。由于它在求很多的素数上有很高的查找效率，所以在这种情况下选用欧拉筛法就好过埃氏筛法。那么如何从埃氏筛法过渡到欧拉筛法呢？这里只要解决了埃氏筛法的一个小的缺陷就可以实现欧拉筛法。这个缺陷就是当有一个合数参与到埃氏筛法的运算中时有可能被筛选多次，对于这个问题我们只需要使用它的最小质因子来筛选，即可解决这个问题。这也就是欧拉筛法的核心思想。核心代码如下：

```java
private static final int MAX = 999999999;
/**
* subscript ---->数组下标
* mark      ---->boolean类型的标记表
* primeTable---->int类型的素数表
*/
private int subscript = 0;
private final boolean[] mark = new boolean[MAX];
private int[] primeTable;	
/**
* 该算法还有不足之处，欢迎指正修改
*
* @return 返回素数组成的数组
*/
@Override
public int[] isPrime(int max) {
    primeTable = new int[max];
    Arrays.fill(mark, false);
    Arrays.fill(primeTable, 0);
    for (int i = 2; i <= max; i++) {
        if (!mark[i]) {
            primeTable[subscript++] = i;
        }
        for (int j = 0; j < subscript && i * primeTable[j] <= max; j++) {
            mark[i * primeTable[j]] = true;
            if (i % primeTable[j] == 0) {
                break;
            }
        }
    }
    return primeTable;
}
```

**注意**：

> 本人写的这种方式的欧拉筛法是查阅资料后进行书写的，有很大的不足，相比之前埃氏筛法的解决思想有很大的出入。所以导致了两种算法的特性并没有发挥出来，经过验证反而埃氏筛法在大数上的计算效率高了，这显然不满足欧拉筛法的高效。如果两种算法都使用统一的代码来写，明显欧拉筛法效率要高。所以为了使代码更加高效，采取埃氏筛法的解决办法来写，但目前未找到头绪，后续解决这个问题后会将整理好的代码上传至个人github下。欢迎各位博友指正修改。