## Java基础——笔记要点（三）

### Java编程基础要点记录

#### 1、JDBC（Java Database Connectivity）

JDBC API主要位于Java.sql包中，它使得应用程序与数据库之间起到一个桥梁的作用，使程序的可移植性大大增强。JDBC中的Driver接口是所有JDBC驱动程序必须实现的接口，专门提供给数据库厂商使用。在编写JDBC程序的时候，必须将数据库的驱动程序或类库加载到项目的classpath中。（如：Mysql的jar包等）

![image-20201018125959548](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201018125959548.png)

##### （1）DriverManager类

**定义**：该类用于加载JDBC驱动并创建与数据库的连接。

**常用方法：**

|                            方法名                            |                   描述                   |
| :----------------------------------------------------------: | :--------------------------------------: |
|          static void registerDriver(Driver driver)           | 向DriverManager中注册给定的JDBC驱动程序  |
| static Connection getConnection(String url,String user,String pwd) | 建立和数据库的连接，并返回Connection对象 |

##### （2）Connection接口

**定义**：该接口代表Java程序和数据库的连接，只有获得该连接对象后，才可访问数据库，并操作数据表。

**常用方法：**

|                     方法名                     |                            描述                            |
| :--------------------------------------------: | :--------------------------------------------------------: |
|         DatabaseMetaData_getMetaData()         |          返回数据库的元数据的DatabaseMetaData对象          |
|          Statement createStatement()           |         创建一个Statement对象将SQL语句发送到数据库         |
| PreparedStatement prepareStatement(String sql) | 创建一个PreparedStatement对象将参数化的SQL语句发送到数据库 |
|   CallableStatement prepareCall(String sql)    |     创建一个CallableStatement对象来调用数据库存储过程      |

##### （3）Statement接口

**定义**：该接口用来执行静态的SQL语句，并返回结果对象。Statement接口对象可以通过Connection实例的createStatement()方法获得，该对象会把静态的SQL语句发送到数据库中编译执行，返回处理结果。

**常用方法：**

|               方法名               |                             描述                             |
| :--------------------------------: | :----------------------------------------------------------: |
|    boolean execute(String sql)     | 执行各种sql语句，返回boolean的值，若为true则表示所执行的sql语句有查询结果，可通过Statement的getResultSet()方法获取查询结果 |
|   int executeUpdate(String sql)    | 执行sql语句中的insert、update和delete语句。返回的值表示数据库中受影响的记录条数 |
| ResultSet executeQuery(String sql) |  执行sql中的select语句，返回一个表示查询结果的ResultSet对象  |

##### （4）PreparedStatement

**定义：**
该接口是Statement的子接口，用于执行预编译的SQL语句。其扩展了带参数SQL语句的执行操作，应用该接口中的SQL语句可使用占位符"?"来代替其参数，然后通过setXXX()方法为SQL语句的参数赋值。

**常用方法：**

|                            方法名                            |                             描述                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                     int executeUpdate()                      | 在此PreparedStatement对象中执行SQL语句，该语句必须是一个DML语句或是无返回内容的SQL语句，如DDL语句 |
|                   ResultSet executeQuery()                   | 在此PreparedStatement对象中执行SQL查询，该方法返回的是ResultSet对象 |
|            void setInt(int parameterIndex,int x)             |                 将指定参数设置为给定的int值                  |
|          void setFloat(int parameterIndex,float x)           |                将指定参数设置为给定的float值                 |
|         void setString(int parameterIndex,String x)          |                将指定参数设置为给定的String值                |
|           void setDate(int parameterIndex,Date x)            |                 将指定参数设置为给定的Date值                 |
|                       void addBatch()                        |    将一组参数添加到此PreparedStatement对象的批处理命令中     |
| void setCharacterStream(int parameterIndex,java.io.Reader reader,int length) |              将指定的输入流写入数据库的文本字段              |
| void setBinaryStream(int parameterIndex,java.io.inputStream x,int length) |            将二进制的输入流数据写入到二进制字段中            |

**注意：**
①setDate方法中的参数Date的类型是java.sql.Date，而不是java.util.Date。
②setXXX设置参数的方法，还可以有很多，如setObject()。

##### （5）ResultSet接口

**定义：**
该接口用来保存JDBC执行查询时返回的结果集，该结果集封装在一个逻辑表格中。在ResultSet接口内部有一个指向表格数据行的游标（或指针）。ResultSet对象初始化时，游标在表格的第一行之前，调用next()方法可将游标移动到下一行。若下一行没有数据则返回false。

**常用方法：**

|               方法名                |                             描述                             |
| :---------------------------------: | :----------------------------------------------------------: |
|  String getString(int columnIndex)  | 用于获取指定字段的String类型的值，参数columnIndex代表字段的索引 |
| String getString(String columnName) | 用于获取指定字段的String类型的值，参数columnName代表字段的名称 |
|     int getInt(int columnIndex)     | 用于获取指定字段的int类型的值，参数columnIndex代表字段的索引 |
|    int getInt(String columnName)    | 用于获取指定字段的int类型的值，参数columnName代表字段的名称  |
|    Date getDate(int columnIndex)    | 用于获取指定字段的Date类型的值，参数columnIndex代表字段的索引 |
|   Date getDate(String columnName)   | 用于获取指定字段的Date类型的值，参数columnName代表字段的名称 |
|           boolean next()            |                 将游标从当前位置向下移动一行                 |
|      boolean absolute(int row)      |             将游标移动到此ResultSet对象的指定行              |
|          void afterLast()           |      将游标移动到此ResultSet对象的末尾，即最后一行之后       |
|         void beforeFirst()          |       将游标移动到此ResultSet对象的开头，即第一行之前        |
|         boolean previous()          |             将游标移动到此ResultSet对象的上一行              |
|           boolean last()            |            将游标移动到此ResultSet对象的最后一行             |

##### （6）JDBC API的使用步骤

**第一步**：加载并注册数据库驱动，注册数据库驱动的方式如下

```java
DriverManager.registerDriver(Driver driver);
//或
Class.forName("DriverName");
```

**第二步**：通过DriverManager获取数据库连接，获取数据库连接的方式如下

```java
Connection conn=DriverManager.getConnection(String url,String user,String pwd);
```

其中url的书写格式例如（MySql）`jdbc:mysql://hostname:port/databasename`

**第三步**：通过Connection对象获取Statement对象，Connection创建Statement的方式有如下三种

+ createStatement()：创建基本的Statement对象
+ prepareStatement()：创建PreparedStatement对象
+ prepareCall()：创建CallableStatement对象

例如：`Statement stmt=conn.createStatement()`

**第四步**：使用Statement执行SQL语句，有如下三种方式执行SQL语句

+ execute()：可执行任何SQL语句
+ executeQuery()：通常执行查询语句，执行后返回代表结果集的ResultSet对象
+ executeUpdate()：主要执行DML和DDL语句；执行DML语句时，返回受影响的行数；执行DDL语句时，返回0

例如：`ResultSet rs=stmt.executeQuery(sql);//获取结果集`

**第五步**：操作ResultSet结果集，程序可以通过操作ResultSet对象来取出结果

**第六步**：关闭连接，释放资源

#### 2、线程

在Java中提供了两种多线程实现方式，实现方法如下：

①继承自java.lang包下的Thread类，覆写Thread类的run()方法，在run方法中实现运行在线程上的代码；

②实现java.lang.Runnable接口，同样是在run()方法中实现运行在线程上的代码

**常用方法：**

|               方法名                |              描述              |
| :---------------------------------: | :----------------------------: |
|        setName(String name)         |          设置线程名称          |
| Thread(Runnable target,String name) | 构造方法，在创建时指定线程名称 |
|              getName()              |          获取线程名称          |
|           currentThread()           |        获取当前线程对象        |



##### （1）继承Thread类创建多线程

程序通过自定义的类继承Thread类，并实现run()方法后在主运行方法中通过调用Thread类的start()方法，启动新的线程，新线程启动后会自动调用run方法，这样就实现了创建多线程的操作。但这种方法存在弊端，根据java的特性，一个类一旦继承了其他类就无法再继承Thread类来创建线程。

##### （2）实现Runnable接口创建新线程

该方法克服了第一种方法的弊端，其中Thread类定义的另一个构造方法Thread(Runnable target)的Runnable接口提供了一个run()方法，通过这个构造方法创建线程对象时，只需要为该方法传递一个Runnable接口的实例对象，这时创建的线程会调用Runnable中的run()方法，而不是Thread类中的run()方法。

##### （3）线程的状态转换

线程的状态转换与线程的生命周期有关，线程的生命周期可分为五个阶段（新建状态`New`、就绪状态`Runnable`、运行状态`Running`、阻塞状态`Blocked`和死亡状态`Terminated`）。

**新建状态**：该状态下的线程不能运行，和其他java对象有关，仅由虚拟机为其分配了内存。通过`start()`方法可转换为就绪状态。

**就绪状态**：该状态下的线程位于线程队列中，它只具备了运行条件，能否运行获得CPU的使用权并开始运行还要等待系统的调度。当其获得CPU的使用权并开始运行时就转换成了运行状态。

**运行状态**：当线程启动后该线程就处于运行状态，当线程使用完系统分配的时间后，系统就会剥夺该线程占用的CPU资源。当线程失去CPU使用权时线程就会返回到就绪状态。当其调用run()执行完Exception或Error后线程就会转换为死亡状态。当其获取同步锁或调用IO阻塞、wait()、join()或sleep()方法后就会转换成阻塞状态。

**阻塞状态**：当正在执行的线程由于某些特殊情况，会让出CPU的使用权并暂停自己的执行，进入阻塞状态，只有当阻塞的原因消除后，线程才可转入就绪状态，等待CPU分配使用时间。这些特殊情况可以是被人为挂起或执行耗时的输入/输出操作等。当线程试图获取某个对象的同步锁时，如果该锁被其他线程占用，那么当前线程就会进入阻塞状态。如果想从阻塞状态转换为就绪状态就必须获取到其他线程所持有的锁或等待所有引起阻塞的操作结束。当调用wait()方法阻塞线程时，就需要使用notify()方法唤醒该线程。阻塞状态只能转换为就绪状态不可直接转换成运行状态。

**死亡状态**：线程一旦进入死亡状态就不再运行的资格，也不可再转换成其他状态。

![image-20201015091615694](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015091615694.png)

##### （4）线程调度

**定义：**
Java虚拟机会按照特定的机制为程序中的每个线程分配CPU的使用权，这种机制叫做线程调度。

线程调度模型可分为分时调度模型和抢占式调度模型。分时调度模型是指让所有的线程轮流获得CPU的使用权，并平均分配CPU运行时间片。抢占式调度模型是指让可运行池中优先级高的线程优先占用CPU，对于优先级相同的线程会随机选择一个线程占用优先级。JVM默认采用抢占式调度模型。

**线程优先级：**
线程的优先级用1~10的整数来表示，数字越大优先级越高。Thread类中提供的三个静态常量表示线程的优先级。

|    Thread类的静态常量    |             描述             |
| :----------------------: | :--------------------------: |
| static int MAX_PRIORITY  | 表示线程的最高优先级，值为10 |
| static int MIN_PRIORITY  | 表示线程的最低优先级，值为1  |
| static int NORM_PRIORITY | 表示线程的普通优先级，值为5  |

**线程的优先级可以通过`setPriority(int newPriority)`方法来设置，参数newPriority接收的是1~10之间的整数或者Thread类的三个静态常量**。

**注意：**
不同的操作系统对线程的优先级的支持不一样。

##### （5）线程休眠

当调用静态方法sleep(long millis)方法时，该方法可以让当前正在执行的线程暂停一段时间，进入休眠等待状态，在指定的millis时间内该线程不会执行。当休眠时间结束后，线程就会返回到就绪状态，而不是立即开始执行。

##### （6）线程让步

线程让步可以通过yield()方法实现，该方法可以让正在执行的线程暂停，但其不会阻塞线程只是将线程的当前状态转换成就绪状态，让系统调度器重新调度一次。当某个线程调用yield()方法之后，只有与当前线程优先级相同或更高的线程才能获得执行机会。

##### （7）线程插队

当线程调用其他线程的join()方法时，调用的线程将被阻塞，直到被join()方法加入的线程执行完成后它才会继续运行。

##### （8）多线程同步

当多个线程同时处理共享资源时就会引发线程的安全问题，要想解决线程安全问题就必须使得处理共享资源在任何时刻只能有一个线程访问。为此需要使用Java提供的同步机制，将处理共享资源的代码放在Synchronized关键字来修饰代码块，这个代码块被称为同步代码块，语法格式如下：

```java
synchronized(lock){
    操作共享资源的代码块
}
```

当某一个线程执行同步代码块时，其他线程将无法执行当前同步代码块，会发生阻塞，等当前线程执行完同步代码块后，其他线程才能执行。lock是一个锁对象，可以是任意类型的对象，但多个线程共享的锁对象必须是唯一的。**注意：锁对象的创建代码不能放在run()方法中。**

除了使用synchronized修饰同步代码块，还可以通过synchronized修饰方法，被其修饰的方法称为同步方法。例如：`public synchronized void sendTicket()`，同步方法与同步代码块的作用大体相同且同步方法也拥有锁，这个锁就是当前调用该方法的对象（this所指向的对象），对于静态方法的锁就是该方法所在类的class对象，该对象在装载该类时自动创建，这个锁指向的对象可以使用类名.class的方式获取。缺点线程每次执行同步代码时会判断锁的状态，非常耗费资源，效率较低。

#### 3、InetAddress类

该类用来封装IP地址，与其相关的常用方法如下：

|               方法名               |                             描述                             |
| :--------------------------------: | :----------------------------------------------------------: |
| InetAddress getByName(String host) | 参数host表示指定的主机，该方法用于在给定主机名的情况下确定主机的IP地址 |
|     InetAddress getLocalHost()     |            创建一个表示本地主机的InetAddress对象             |
|        String getHostName()        | 得到IP地址的主机名，如果是本机则是计算机名，不是本机则是主机名，如果没有域名则是IP地址 |
|  boolean isReachable(int timeout)  |               判断指定的时间内地址是否可以到达               |
|      String getHostAddress()       |                  得到字符串格式的原始IP地址                  |

#### 4、UDP协议与TCP协议

TCP是传输控制协议，UDP是用户数据报协议。它们的区别在于，TCP建立的是计算机之间逻辑连接，能为两台计算机之间提供无差错的数据传输。而UDP不需要建立这种逻辑连接，在数据传输时无需等待其他主机的响应就可直接发送，使用UDP消耗资源小，通信效率高，所以通常都会用于音频、视频和普通数据的传输，但在传输重要数据时不能使用UDP协议，因为UDP不能保证数据的完整性。此外，TCP通信的两端都需要创建Socket对象，它是严格区分客户端与服务器端的，通信时客户端必须先连接服务器才能实现通信。而UDP只有发送端和接收端，计算机之间可以任意发送数据，不需要先启动哪一个程序。

##### （1）TCP的三次握手

第一次握手：客户端向服务器发送连接请求，等待服务器确认。

第二次握手：服务器向客户端回送一个响应，通知客户端收到了连接请求。

第三次握手：客户端再次向服务器发送确认信息，确认连接。

**注意：**
使用TCP协议在通信时，首先需要创建代表服务器端的ServerSocket对象，此时相当于开启的一个服务等待客户端的连接；然后创建代表客户端的Socket对象，使用该对象向服务器端发送连接请求，服务器端响应请求后，两者才会建立起连接，并开始通信。

##### （2）DatagramPacket类

该类是UDP通信中的一个类，主要用于封装UDP通信中发送或接收的数据。

**常用的构造方法：**

|                            方法名                            |                             描述                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|            DatagramPacket(byte[] buf,int length)             | 只能用于接收端，参数buf定义了封装数据的字节数组，length定义了数据的大小 |
| DatagramPacket(byte[] buf,int length,InetAddress addr,int port) | 通常用于发送端，参数addr定义了数据包的目标IP地址，port定义了目标端口号 |
|       DatagramPacket(byte[] buf,int offset,int length)       | 只能用于接收端，参数定义了接收到的数据在放入缓冲数组时是从offset开始存放 |
| DatagramPacket(byte[] buf,int offset,int length,InetAddress addr,int port) | 通常用于发送端，参数定义了在发送数据时是从buf的offset位置开始发送的 |

发送端需要明确指定数据的目的地（IP和端口号），而接收端不需要明确知道数据的来源。

**常用方法：**

|          方法名          |                             描述                             |
| :----------------------: | :----------------------------------------------------------: |
| InetAddress getAddress() | 该方法用于返回发送端或接收端的IP地址，若是发送端的DatagramPacket对象，就返回接收端的IP地址；反之，就返回发送端的IP地址 |
|      int getPort()       | 该方法用来返回发送端或接收端的端口号，如果是发送端的DatagramPacket对象，就返回接收端的端口号 |
|     byte[] getData()     | 返回将要接收或发送的数据，若是发送端的DatagramPacket对象，就返回将要发送的数据 |
|     int getLength()      | 返回接收或发送数据的长度，若是发送端的DatagramPacket对象，就返回将要发送的数据长度 |

##### （3）DatagramSocket类

该类主要用于UDP通信，它的作用类似于码头，用来接收或发送数据的一个平台，使用这个类的实例对象就可以发送或接收DatagramPacket数据包。

**常用的构造方法：**

|                  方法名                   |                             描述                             |
| :---------------------------------------: | :----------------------------------------------------------: |
|             DatagramSocket()              | 用来创建发送端的DatagramSocket对象，由于没有指定端口号，此时系统会分配一个没有被其他网络程序所使用的端口号 |
|         DatagramSocket(int port)          | 用来创建发送端或接收端的DatagramSocket对象，在创建接收端的DatagramSocket对象时，必须要指定端口号，这样才能监听指定的端口 |
| DatagramSocket(int port,InetAddress addr) | 该方法适用于计算机有多块网卡的情况，通过指定IP地址来指定DatagramSocket对象通过哪块网卡进行通信 |

**常用方法：**

|             方法名             |                             描述                             |
| :----------------------------: | :----------------------------------------------------------: |
| void receive(DatagramPacket p) | 该方法用于将接收到的数据填充到DatagramPacket数据包中，在接收到数据之前会一直处于阻塞状态，只有当接收到数据包时，该方法才会返回 |
|  void send(DatagramPacket p)   | 用于发送DatagramPacket数据包，发送的数据包中包含将要发送的数据、数据的长度、远程主机的IP地址和端口号 |
|          void close()          |   关闭当前的Socket，通知驱动程序释放为这个Socket保留的资源   |

##### （4）ServerSocket类

该类主要用于TCP通信，该类的实例对象可以实现一个服务器端的程序。

**常用的构造方法：**

|                         方法名                          |                             描述                             |
| :-----------------------------------------------------: | :----------------------------------------------------------: |
|                     ServerSocket()                      | 该方法只是创建了ServerSocket对象，该对象创建的服务器没有监听任何端口，不能直接使用，还需要调用bind()方法，将其绑定到指定的端口号上 |
|                 ServerSocket(int port)                  |        该方法可以将ServerSocket对象绑定到指定的端口上        |
|           ServerSocket(int port,int backlog)            | 该方法与第二个方法类似，增加的参数backlog用于指定在服务器忙时，可以与之保持连接请求的等待客户数量，若没指定该参数，默认为50 |
| ServerSocket(int port,int backlog,InetAddress bindAddr) | 该方法通常用于计算机有多块网卡和多个IP的情况下，可以通过bindAddr指定IP来指定所用的网卡 |

**常用方法：**

|              方法名               |                             描述                             |
| :-------------------------------: | :----------------------------------------------------------: |
|          Socket accept()          |       用来等待客户端连接，在连接之前会一直处于阻塞状态       |
|   InetAddress getInetAddress()    | 该方法用来返回一个InetAddress对象，该对象中封装了ServerSocket绑定的IP地址 |
|        boolean isClosed()         | 该方法用来判断ServerSocket对象是否处于关闭状态，若为关闭状态则返回true |
| void bind(SocketAddress endpoint) | 用来将ServerSocket对象绑定到指定的IP地址和端口号，参数endpoint封装了IP地址和端口号 |

##### （5）Socket类

该类主要用于TCP通信，该类的实例对象可以实现一个客户端的程序。

**常用的构造方法：**

|                方法名                |                             描述                             |
| :----------------------------------: | :----------------------------------------------------------: |
|               Socket()               | 该方法只是创建了Socket对象，该对象创建的客户端没有连接任何服务器，不能直接使用，还需要调用connect()方法，才能完成与指定服务器端的连接 |
|     Socket(String host,int port)     | 该方法会根据参数去连接在指定地址和端口上运行的服务器程序。其中，host接收的是一个字符串类型的IP地址 |
| Socket(InetAddress address,int port) | 该方法与第二个方法类似，参数address用来接收一个InetAddress类型的对象，该对象用来封装一个IP地址 |

**常用方法：**

|             方法名             |                             描述                             |
| :----------------------------: | :----------------------------------------------------------: |
|         int getPort()          |              获取Socket对象与服务器连接的端口号              |
| InetAddress getLocalAddress()  | 获取Socket对象绑定的本地IP地址，并将IP地址封装成InetAddress类型的对象返回 |
|          void close()          |               用来关闭Socket连接，结束本次通信               |
|  InputStream getInputStream()  | 用来返回一个InputStream对象，若该对象是由服务器端的Socket返回的，就用于读取客户端发送的数据，反之则用于读取服务器端发送的数据 |
| OutputStream getOutputStream() | 用来返回一个OutputStream对象，若该对象是由服务器端的Socket返回的，就向客户端发送数据，反之则向服务器端发送数据 |



#### 问题

1、Statement接口和PreparedStatement接口有什么区别？[^1]

2、进程与线程有何不同？[^2]

#### 补充/技巧

1、ResultSet中的next方法可作为while循环的条件来迭代ResultSet结果集。

2、当相同的SQL语句执行多次时，为提高访问效率这时可采用PreparedStatement对象。因为PreparedStatement对象会对SQL语句进行预编译，预编译的信息会保存在PreparedStatement对象中。当相同的SQL语句再执行时，程序就会使用PreparedStatement对象中保存的数据。

3、当要获取结果集中任意位置的值时，可设置ResultSet的常量，使结果集可以滚动显示。如：

```java
Statement st=conn.createStatement(ResultSet.TYPE_SCROLL_INSENITIVE,ResultSet.CONCUR_READ_ONLY);
ResultSet rs=st.excuteQuery(sql);
```

ResultSet.TYPE_SCROLL_INSENITIVE:表示结果集可滚动

ResultSet.CONCUR_READ_ONLY：表示以只读形式打开结果集

4、Thread类中的currentThread()方法可以得到当前线程的实例对象，然后通过调用getName()方法即可获得线程的名称。

****


[^1]:PreparedStatement接口继承自Statement接口，但是PreparedStatement提高了代码的可读性和可维护性且性能更高、更安全。而Statement并没有这些优势。
[^2]:线程是作为调度和分配的基本单位，而进程作为拥有资源的基本单位。进程之间可以并发执行，同一个进程的多个线程之间也可以并发执行。进程是拥有资源的一个独立单位，线程不拥有系统资源，但可以访问隶属于进程的资源。一个线程只能属于一个进程，而一个进程可以有多个线程，但至少要有一个线程。

