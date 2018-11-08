一.NIO简介





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


