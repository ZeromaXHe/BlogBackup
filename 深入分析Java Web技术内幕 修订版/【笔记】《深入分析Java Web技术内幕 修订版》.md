# 第1章 深入Web请求过程

## 1.1 B/S网络架构概述

## 1.3 HTTP解析

### 1.3.2 浏览器缓存机制

ctrl+F5 重新请求页面

## 1.4 DNS域名解析

### 1.4.3 清除缓存的域名

InetAddress类解析域名，必须是单例模式，不然会有严重的性能问题，如果每次都创建InetAddress实例，则每次都要进行一次完整的域名解析，非常耗时。

## 1.5 CDN工作机制

分布内容网络（Content Delivery Network）	可以做这样的比喻：CDN=镜像（Mirror）+缓存（Cache）+整体负载均衡（GSLB）。

目前CDM都以缓存网站中的静态数据为主

# 第2章 深入分析Java I/O的工作机制

## 2.1 Java的I/O类库的基本架构

### 2.1.3 字节与字符的转化接口

FileReader继承了InputStreamReader类，实际上是读取文件流，然后通过StreamDecoder解码成char，只不过这里的解码字符集是默认字符集。

## 2.2 磁盘I/O工作机制

### 2.2.2 Java访问磁盘文件

创建一个FileInputStream对象时会创建一个FileDescriptor对象，其实这个对象就是真正代表一个存在的文件对象的描述。当我们在操作一个文件对象时可以通过getFD()方法获取真正操作的与底层操作系统系统相关联的文件描述。

## 2.3 网络I/O工作机制

### 2.3.2 影响网络传输的因素

窗口（BDP，Bandwidth Delay Product）的大小是由带宽和RTT（Round-Trip Time，响应时间）决定的。

### 2.3.4 建立通信链路

要等到与客户端的3次握手完成后，这个服务端的Socket实例才会返回，并将这个Socket实例对应的数据结构从未完成列表中移到已完成列表中。所以与ServerSocket所关联的列表中每个数据结构都代表与一个客户端建立的TCP连接。

### 2.3.5 数据传输

每个Socket实例都有一个InputStream和OutputStream，并通过这两个对象来交换数据。	操作系统会为InputStream和OutputStream分别分配一定大小的缓存区，数据的写入和读取都是通过这个缓存区完成的。写入端将数据写到OutputStream对应的SendQ队列中，当队列填满时，数据将被转移到另一端InputStream的RecvQ队列中，如果这时RecvQ已经满了，那么OutputStream的write方法将会阻塞，知道RecvQ队列有足够的空间容纳SendQ发送的数据。

## 2.4 NIO的工作方式

### 2.4.1 BIO带来的挑战

BIO即阻塞I/O

### 2.4.2 NIO的工作机制

两个关键类：Channel和Selector，它们是NIO中的两个核心概念。

NIO引入了Channel、Buffer、Selector，就是想把这些信息具体化，让程序员有机会控制它们。