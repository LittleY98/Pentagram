# Java I/O

## 一、概览

### Java 的 I/O 大概可以分成以下几类：

磁盘操作：File
字节操作：InputStream 和 OutputStream
字符操作：Reader 和 Writer
对象操作：Serializable
网络操作：Socket
新的输入/输出：NIO

## 二、磁盘操作

### File 类可以用于表示文件和目录的信息，但是它不表示文件的内容。
从 Java7 开始，可以使用 Paths 和 Files 代替 File。

## 三、字节操作

### 装饰者模式

- Java I/O 使用了装饰者模式来实现。以 InputStream 为例，
InputStream 是抽象组件；
FileInputStream 是 InputStream 的子类，属于具体组件，提供了字节流的输入操作；
FilterInputStream 属于抽象装饰者，装饰者用于装饰组件，为组件提供额外的功能。例如 BufferedInputStream 为 FileInputStream 提供缓存的功能。

## 四、字符操作

### 编码与解码

- 编码就是把字符转换为字节，而解码是把字节重新组合成字符。

如果编码和解码过程使用不同的编码方式那么就出现了乱码。

UTF-8 编码中，中文字符占 3 个字节，英文字符占 1 个字节；

Java 的内存编码使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be 进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。

### String 的编码方式

- String 可以看成一个字符序列，可以指定一个编码方式将它编码为字节序列，也可以指定一个编码方式将一个字节序列解码为 String。
getBytes() 的默认编码方式与平台有关，一般为 UTF-8。

### Reader 与 Writer

- 不管是磁盘还是网络传输，最小的存储单元都是字节，而不是字符。但是在程序中操作的通常是字符形式的数据，因此需要提供对字符进行操作的方法。

InputStreamReader 实现从字节流解码成字符流；
OutputStreamWriter 实现字符流编码成为字节流。

## 五、对象操作

### 序列化

- 序列化就是将一个对象转换成字节序列，方便存储和传输。

序列化：ObjectOutputStream.writeObject()
反序列化：ObjectInputStream.readObject()
不会对静态变量进行序列化，因为序列化只是保存对象的状态，静态变量属于类的状态。

### Serializable

- 序列化的类需要实现 Serializable 接口，它只是一个标准，没有任何方法需要实现，但是如果不去实现它的话而进行序列化，会抛出异常。

### transient

- transient 关键字可以使一些属性不会被序列化。

ArrayList 中存储数据的数组 elementData 是用 transient 修饰的，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。通过重写序列化和反序列化方法，使得可以只序列化数组中有内容的那部分数据。

## 六、网络操作

### InetAddress

- 用于表示网络上的硬件资源，即 IP 地址。
没有公有的构造函数，只能通过静态方法来创建实例。

### URL

- 统一资源定位符。
可以直接从 URL 中读取字节流数据。

### Sockets

- 使用 TCP 协议实现网络通信。
 ServerSocket：服务器端类
 Socket：客户端类
 服务器和客户端通过 InputStream 和 OutputStream 进行输入输出。

### Datagram

- DatagramSocket：通信类
DatagramPacket：数据包类

## 七、NIO

### 概览

- 新的输入/输出 (NIO) 库是在 JDK 1.4 中引入的，弥补了原来的 I/O 的不足，提供了高速的、面向块的 I/O。

### 流与块

- I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，一次处理一个字节，而 NIO 以块的方式处理数据，一次处理一块数据。

### 通道与缓冲区

- 1. 通道

	- 通道 Channel 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。
流只能在一个方向上移动，通道是双向的，可以用于读、写或者同时用于读写。

- 2. 缓冲区

	- 发送给一个通道的所有数据都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要先读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区。

### 缓冲区状态变量

- 位置（position）：当前缓冲区（Buffer）的位置，将从该位置往后读或写数据。
容量（capacity）：缓冲区的总容量上限。
上限（limit）：缓冲区的实际容量大小。

### 选择器

- NIO 实现了 IO 多路复用中的 Reactor 模型，一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件。
应该注意的是，只有套接字 Channel 才能配置为非阻塞，而 FileChannel 不能，为 FileChannel 配置非阻塞也没有意义。

### 内存映射文件

- 内存映射文件 I/O 是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的 I/O 快得多。

*XMind: ZEN - Trial Version*