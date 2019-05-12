# 第4章 网络

## 4.1 连接到服务器

### 4.1.1 使用telnet

telnet是一种用于网络编程的非常强大的调试工具，你可以在命令shell中输入telnet来启动它。

*注意：在Windows中，需要激活telnet。要激活它，需要到“控制面板”，选择“程序”，点击“打开/关闭Windows特性”，然后选择“Telnet客户端”复选框。Windows防火墙将会阻止我们在本章中使用的很多网络端口，你可能需要管理员账户才能解除对它们的禁用。*

*注意：在网络术语中，端口并不是指物理设备，而是为了便于实现服务器与客户端之间的通信所使用的抽象概念。*

*注意：如果一台Web服务器用相同的IP地址为多个域提供宿主环境，那么在连接这台Web Server时，就必须提供Host键/值对。如果服务器只为单个域提供宿主环境，则可以忽略该键/值对。*

### 4.1.2 用Java连接到服务器

下面是这个简单程序的几行关键代码：

```java
Socket s = new Socket("time-a.nist.gov",13);
InputStream inStream = s.getInputStream();
```

第一行代码用于打开一个套接字，它也是网络软件中的一个抽象概念，负责启动该程序内部和外部之间的通信。我们将远程地址和端口号传递给套接字的构造器，如果连接失败，它将抛出一个UnknownHostException异常；如果存在其他问题，它将抛出一个IOException异常。UnknownHostException是IOException的一个子类。

一旦套接字被打开，java.net.Socket类中的getInputStream方法就会返回一个InputStream对象，该对象可以像其他任何对象流一样使用。	这个过程将一直持续到流发送完毕且服务器断开连接为止。

Socket类非常简单易用，因为Java库隐藏了建立网络连接和通过连接发送数据的复杂过程。实际上，java.net包提供的编程接口与操作文件时所使用的接口基本相同。

*注意：本节所介绍的内容仅覆盖了TCP（传输控制协议）网络协议。Java平台另外还支持UDP（用户数据报协议）协议，该协议可以用于发送数据包（也称为数据报），它所需付出的开销要比TCP少得多。UDP有一个重要的缺点：数据包无需按照顺序传递到接收应用程序，它们甚至可能在传输过程中全部丢失。UDP让数据包的接受者自己负责对它们进行排序，并请求发送者重新发送那些丢失的数据包。UDP比较适合于那些可以忍受数据包丢失的应用，例如用于音频流和视频流的传输，或者用于连续测量的应用领域。*

【API】java.net.Socket 1.0 :

- `Socket(String host, int port)`
  构建一个套接字，用来连接给定的主机和端口
- `InputStream getInputStream()`
- `OutputStream getOutputStream()`
  获取可以从套接字中读取数据的流，以及可以向套接字写出数据的流。

### 4.1.3 套接字超时

从套接字读取信息时，在有数据可供访问之前，读操作将会被阻塞。如果此时主机不可达，那么应用将要等待很长的时间，并且因为受低层操作系统的限制而最终会导致超时。

对于不同的应用，应该确定合理的超时值。然后调用setSoTimeout方法设置这个超时值（单位：毫秒）

如果已经为套接字设置了超时值，并且之后的读操作和写操作在没有完成之前就超过了时间限制，那么这些操作就会抛出SocketTimeoutException异常。你可以捕获这个异常，并对超时做出反应。

另外还有一个超时问题是必须解决的。下面这个构造器：`Socket(String host, int port)`会一直无限期地阻塞下去，直到建立了到达主机的初始连接为止。

可以通过先构建一个无连接的套接字，然后再使用一个超时来进行连接的方式解决这个问题。
`Socket s = new Socket();`
`s.connect(new InetSocketAddress(host, port), timeout);`

【API】java.net.Socket 1.0 :

- `Socket()` 1.1
  创建一个还未被连接的套接字
- `void connect(SocketAddress address)` 1.4
  将该套接字连接到给定的地址
- `void connect(SocketAddress address, int timeOutInMilliseconds)` 1.4
  将套接字连接到给定的地址。如果在给定的时间内没有相应，则返回。
- `void setSoTimeout(int timeoutInMilliseconds)` 1.1
  设置该套接字上读请求的阻塞时间。如果超出给定时间，则抛出一个InterruptedIOException异常。
- `boolean isConnected()` 1.4
  如果该套接字已被连接，则返回true。
- `boolean isClosed()` 1.4
  如果套接字已经被关闭，则返回true。

### 4.1.4 因特网地址

通常，不用过多考虑因特网地址的问题，它们是用一串数字表示的主机地址，一个因特网地址由4个字节组成（在IPv6中是16个字节），比如129.6.15.28。但是，如果需要在主机名和因特网地址之间进行转换，那么就可以使用InetAddress类。

只要主机操作系统支持IPv6格式的因特网地址，java.net包也将支持它。

静态的getByName方法可以返回代表某个主机的InetAddress对象。

【API】java.net.InetAddress 1.0 :

- `static InetAddress getByName(String host)`
- `static InetAddress[] getAllByName(String host)`
  为给定的主机名创建一个InetAddress对象，或者一个包含该主机名所对应的所有因特网地址的数组。
- `static InetAddress getLocalHost()`
  为本地主机创建一个InetAddress对象。
- `byte[] getAddress()`
  返回一个包含数字型地址的字节数组。
- `String getHostAddress()`
  返回一个由十进制数组成的字符串，各数字间用圆点符号隔开，例如，"129.6.15.28"。
- `String getHostName()`
  返回主机名

## 4.2 实现服务器

### 4.2.1 服务器套接字

一旦启动了服务器程序，它便会等待某个客户端连接到它的端口。	ServerSocket类用于建立套接字。

每一个服务器程序，比如一个HTTP Web服务器，都会不间断地执行下面这个循环：

1）通过输入数据流从客户端接受一个命令（“get me this information”）。

2）解码这个客户端命令。

3）收集客户端所请求的信息。

4）通过输出数据流发送信息给客户端。

【API】java.net.ServerSocket 1.0 :

- `ServerSocket(int port)`
  创建一个监听端口的服务器套接字。
- `Socket accept()`
  等待连接。该方法阻塞（即，使之空闲）当前进程直到建立连接为止。该方法返回一个Socket对象，程序可以通过这个对象与连接中的客户端进行通信。
- `void close()`
  关闭服务器套接字

### 4.2.2 为多个服务端服务

每当程序建立一个新的套接字连接，也就是说当调用accept()时，将会启动一个新的线程来处理服务器和该客户端的连接，而主程序将立即返回并等待下一个连接。

ThreadedEchoHandler类实现了Runnable接口，而且在它的run方法中包含了与客户端循环通信的代码。

*注意：在这个程序中，我们为每个连接生成一个单独的线程。这种方法并不能满足高性能服务器的要求。为使服务器实现更高的吞吐量，你可以使用java.nio包中的一些特性。*

### 4.2.3 半关闭

**半关闭（half-close）**提供了这样一种能力：套接字连接的一端可以终止其输出，同时仍旧可以接受来自另一端的数据。

可以通过关闭一个套接字的输出流来表示发送给服务器的请求数据已经结束，但是保持输入流处于打开状态。

如下代码演示了如何在客户端使用半关闭方法：

```java
try(Socket socket = new Socket(host, port)){
    Scanner in = new PrintWriter(socket.getInputStream(),"UTF-8");
    PrintWriter writer = new PrintWriter(socket.getOutputStream());
    //send request data
    writer.print(...);
    writer.flush();
    socket.shutdownOutput();
    //now socket is half-closed
    //read respond data
    while(in.hasNextLine()!=null){String line = in.nextLine(); ... }
}
```

服务器端将读取输入信息，直至到达输入流的结尾，然后它再发送响应。

当然，该协议只适用于一站式（one-shot）的服务，例如HTTP服务，在这种服务中，客户端连接服务器，发送一个请求，捕获响应信息，然后断开连接。

【API】java.net.Socket 1.0 :

- `void shutdownOutput()` 1.3
  将输出流设为"流结束"。
- `void shutdownInput()` 1.3
  将输入流设为“流结束”。
- `boolean isOutputShutdown()` 1.4
  如果输出已被关闭，则返回true。
- `boolean isInputShutdown()` 1.4
  如果输出已被关闭，则返回true。

## 4.3 可中断套接字

当连接到一个套接字时，当前线程将会被阻塞知道建立连接或产生超时为止。同样地，当通过套接字读写数据时，当前线程也会阻塞直到操作成功或产生超时为止。

为了中断套接字操作，可以使用java.nio包提供的一个特性——SocketChannel类。

通道（channel）并没有与之相关联的流。实际上，它所拥有的read和write方法都是通过使用Buffer对象来实现的（关于NIO缓冲区的相关信息请参见第2章）。ReadableByteChannel接口和WriterByteChannel接口都声明了这两个方法。

如果不想处理缓冲区，可以使用Scanner类从SocketChannel中读取信息，因为Scanner有一个带ReadableByteChannel参数的构造器。

通过调用静态方法Channels.newOutputStream，可以将通道转换成输出流。