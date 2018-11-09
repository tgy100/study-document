#### 一.NIO简介

##### 1)简介

	Java NIO ( New IO Non Blocking IO)是从Java 1.4版本开始引入的一个新的IO API,可以替代标准的JavaIOAPI。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的、基于通道的IO操作。NIO将以更加高效的方式进行文件的读写操作。

##### 2)NIO与IO的区别

| NIO                     | IO                          |
| ----------------------- | --------------------------- |
| 面向流(Stream Oriented) | 面向缓冲区(Buffer Oriented) |
| 阻塞I0(Blocking IO)     | 非阻塞I0(Non Blocking I0)   |
| 无                      | 选择器(Selectors)           |

#### 二.通道(channel)与缓冲区(buffer)

##### 1)通道与缓冲区的关系

![image-20181108162155712](image/nio/channel_buffer_relation.png)

通道:通道是链接文件，磁盘，网络的管道，而缓冲区就是数据流的载体，数据通过缓冲区在通道中传输，同时通道是双向的。

##### 2)缓冲区

1. 作用

   在Java NIO 中负责数据的存取。缓冲区就是数组, 用于存储不同数据类型的数据

2. 类型

   根据数据类型不同(boolean除外)，提供了相应类型的缓冲区:
   ByteBuffer，CharBuffer，ShortBuffer，IntBuffer，LongBuffer，FloatBuffer，DoubleBuffer
   上述缓冲区的管理方式几乎一致，通过allocate()获取缓冲区

3. api

   - 缓冲区存取数据的两个核心方法:

     - put( ): 存入数据到缓冲区中
     - get() : 获取缓冲区中的数据
     - flip(): 把缓冲区变成读模式
     - rewind()： 设置position的位置到头部
     - clear(); 清空数据

   - 缓冲区中的四个核心属性:

     - capacity :
       容量，表示缓冲区中最大存储数据的容量。一旦声明不能改变。

     - limit :界限，

       表示缓冲区中可以操作数据的大小。(limit 后数据不能进行读写)

     - position :位置

       表示缓冲区中正在操作数据的位置工

     - mark :标记

       表示记录当前position的位置。可以通过reset( )恢复到mark的位置

     - 总结

       0 <= mark <= position <= limit <= capacity

   - 代码

     ```java
     //创建ByteBuffer
     ByteBuffer buffer = ByteBuffer.allocate(1024);
     
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     
     
     String name = "zhangsan";
     
     buffer.put(name.getBytes());
     System.out.println("---------------------------");
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     
     //flip:把容器变成读模式
     buffer.flip();
     System.out.println("---------------------------");
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     
     byte [] read_tmp = new byte[buffer.limit()];
     //获取数据
     buffer.get(read_tmp);
     System.out.println("---------------------------");
     System.out.println(read_tmp);
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     
     //重新设置position的位置
     buffer.rewind();
     System.out.println("---------------------------");
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     
     byte readBF[] = new byte[buffer.limit()];
     buffer.get(readBF,0,2);
     System.out.println("---------------------------");
     System.out.println(new String(readBF,0,2));
     //记住当前的position的位置
     buffer.mark();
     buffer.get(readBF,0,2);
     System.out.println(new String(readBF,0,2));
     //回到上次mark的position的位置
     buffer.reset();
     buffer.get(readBF,0,2);
     System.out.println(new String(readBF,0,2));
     
     /**
      * 清空缓冲区(设置为写模式),把limit设置为capacity，position设置为0,
      * 但是里面的数据还在，处于"被遗忘状态"
      */
     buffer.clear();
     System.out.println("---------------------------");
     System.out.println(buffer.position());
     System.out.println(buffer.capacity());
     System.out.println(buffer.limit());
     ```

4. 直接缓冲区与非直接缓冲区

   - 非直接缓冲区

     通过allocate()方法分配缓冲区，将缓冲区建立在JVM的内存中

   - 直接缓冲区:

     通过allocateDirect()方法分配直接缓冲区，将缓冲区建立在物理内存中。可以提高效率

   ```java
   //创建直接缓冲区 
   ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
   //是否是直接缓冲区与非直接缓冲区
   buffer.isDirect();
   ```

##### 3)通道

1. 通道(Channel) 

   用于源节点与目标节点的连接。在Java NIO中负责缓冲区中数据的传输。Channel本身不存储数据，因此需要配合缓冲区进行传输。

2. 通道的主要实现类

   实现java.nio.channels包下的: Channel接口

   - 本地文件

     FileChannel

   - TCP

     SocketChannel, ServerSocketChannel

   - UDP

     DategramChannel

3. 获取通道

   - Java 针对支持通道的类提供了getChannel() 方法

     - 本地IO

       FileInputStream/FileOutputStream/RandomAccessFile

     - 网络IO

       Socket/ServerSocket/DategramSocket

   - 在JDK 1.7中的NI0.2 针对各个通道提供了静态方法open()

   - 在JDK 1.7中的NI0.2的Files 工具类的newByteChannel()

4. 读取文件

   - 使用非直接缓冲区

     ```java
     @Test
     public void  test03() throws Exception{
     
         FileInputStream fileInputStream = new FileInputStream("/Users/tgy/Documents/firsttest/class-dump");
         FileOutputStream fileOutputStream = new FileOutputStream("/Users/tgy/Documents/firsttest/class-dump02");
         Channel inputStreamChannel = fileInputStream.getChannel();
         FileChannel outputStreamChannel = fileOutputStream.getChannel();
     
         ByteBuffer buffer = ByteBuffer.allocate(1024);
     
         while (((FileChannel) inputStreamChannel).read(buffer) != -1){
     
             //转换成读模式
             buffer.flip();
             outputStreamChannel.write(buffer);
             //转化成写模式
             buffer.clear();
         }
         fileInputStream.close();
         fileOutputStream.close();
     }
     ```

   - 使用直接缓冲区

     ```java
     @Test
     public void test4() throws Exception {
     
     
         FileChannel inputChannel = FileChannel.open(Paths.get("/Users/tgy/Documents/firsttest/class-dump"), StandardOpenOption.READ);
     
         //CREATE_NEW:文件存在就报错
         FileChannel outputChannel = FileChannel.open(Paths.get("/Users/tgy/Documents/firsttest/class-dump03"), StandardOpenOption.WRITE,StandardOpenOption.READ, StandardOpenOption.CREATE_NEW);
     
     
         //内存映射文件
         MappedByteBuffer inputMap = inputChannel.map(FileChannel.MapMode.READ_ONLY, 0, inputChannel.size());
         MappedByteBuffer outputMap = outputChannel.map(FileChannel.MapMode.READ_WRITE, 0, inputChannel.size());
     
         //直接读文件
         byte buf[] = new byte[inputMap.limit()];
         inputMap.get(buf);
         outputMap.put(buf);
     
         inputChannel.close();
         outputChannel.close();
     
     }
     ```

     使用==transferTo/transferFrom==

     ```java
     @Test
     public void test05() throws  Exception{
     
         FileChannel inFileChannel = FileChannel.open(Paths.get("/Users/tgy/Documents/firsttest/class-dump"), StandardOpenOption.READ);
         FileChannel outFileChannel = FileChannel.open(Paths.get("/Users/tgy/Documents/firsttest/class-dump04"), StandardOpenOption.READ,StandardOpenOption.WRITE,StandardOpenOption.CREATE_NEW);
     
         //使用transferTo/transferFrom
         inFileChannel.transferTo(0,inFileChannel.size(),outFileChannel);
         inFileChannel.close();
         outFileChannel.close();
     }
     ```
