最近看 Spring framework 6、Spring Boot 3、Elasticsearch 8.0 这些都得使用 Java 17 了，而 Kafka 3.0 开始也弃用了 Java 8。正好自己一直没有系统性地去了解一下 Java 9 以后各个新版本的特性，所以自己总结了一下各个 Java 版本的特性，整理好以供参考和学习。

> Spring framework 6 消息来源：https://spring.io/blog/2021/09/02/a-java-17-and-jakarta-ee-9-baseline-for-spring-framework-6
>
> Spring Boot 3 消息来源：https://spring.io/blog/2022/01/20/spring-boot-3-0-0-m1-is-now-available
>
> Elasticsearch 8.0 消息来源：https://www.elastic.co/guide/en/elasticsearch/reference/8.0/migrating-8.0.html
>
> Kafka 3.0 消息来源：https://kafka.apache.org/downloads
>
> 
>
> 不过，Spring 和 Kafka 的新版本文档截至目前（2022.2.21）还没更新过来，里面还有支持 Java 8 的语句没修改…… 不禁感叹论时效性的话，还是需要关注各个框架的官宣文稿啊
>
> Kafka 3.1.x 文档：https://kafka.apache.org/documentation/#java
>
> ![Java各版本_kafka文档](.\图片\Java各版本_kafka文档.png)
>
> Spring framework 6.0.0 文档：https://docs.spring.io/spring-framework/docs/6.0.0-SNAPSHOT/reference/html/overview.html#overview
>
> ![](C:\Users\zhuxi\IdeaProjects\BlogBackup\技术文章\图片\Java各版本_spring6文档.png)



# Java 各版本的简略新特性表

| 版本 | 发布时间 | 新特性                                                       |
| ---- | -------- | ------------------------------------------------------------ |
| 1.0  | 1996     | 语言本身、Java 虚拟机、Applet、AWT                           |
| 1.1  | 1997     | 内部类、反射、JAR 文件格式、JDBC、JavaBeans、RMI             |
| 1.2  | 1998     | strictfp 修饰符、Java 类库的 Collections 集合类、J2SE \ J2EE \ J2ME 体系、EJB、Java Plug-in、Java IDL、Swing、JIT 即时编译器 |
| 1.3  | 2000     | Java 类库的数学运算和新的 Timer API 等、JNDI 开始作为一项平台级服务、使用 CORBA IIOP 实现 RMI、提供大量新的 Java 2D API、新添加 Java Sound 类库 |
| 1.4  | 2002     | 断言、正则表达式、异常链、NIO、日志类、XML 解析器、XSLT 转换器 |
| 5.0  | 2004     | 泛型类、遍历循环（for each 循环）、可变长参数、自动装箱、元数据（动态注解）、枚举、静态导入、改进了 JMM（Java 内存模型）、提供了 java.util.concurrent（JUC）并发包 |
| 6    | 2006     | 启用Java EE 6 \ Java SE 6 \ Java ME 6的新命名来代替J2EE \ J2SE \ J2ME的产品线命名方式、提供初步的动态语言支持、提供编译期注解处理器、微型 HTTP 服务器 API、对 Java 虚拟机内部做了大量改进（锁与同步、垃圾收集、类加载等方面的实现） |
| 7    | 2011.7   | 基于字符串的 switch、钻石操作符、二进制字面量、异常处理改进、提供新的 G1 收集器、加强对非 Java 语言的调用支持、可并行的类加载架构 |
| 8    | 2014.3   | Lambda 表达式、包含默认方法的接口、流和日期/时间库、内置Nashorn JavaScript引擎的支持、彻底移除 HotSpot 的永久代 |
| 9    | 2017.9   | Jigsaw 模块化功能、接口私有方法、Try-With Resources、@SafeVarargs注释、集合工厂方法、Process API改进、流API改进、增强若干工具（JS Shell、JLink、JHSDB等）、整顿了 HotSpot 各个模块各自为战的日志系统、支持HTTP 2客户单API |
| 10   | 2018.3   | 局部变量的类型推断、应用类数据共享、向G1引入并行Full GC、线程局部管控、统一源仓库、统一垃圾收集器接口、统一即时编译器接口（引入新的Graal即时编译器） |
| 11   | 2018.9   | ZGC 垃圾收集器、本地变量类型推断、字符串加强、集合加强、Stream 加强、Optional 加强、InputStream 加强、HTTP Client API、一个命令编译运行源代码 |
| 12   | 2019.3   | Switch表达式、Java微测试套件（JMH）、Shenandoah垃圾收集器、JVM常量API、默认类数据共享归档文件、可终止的G1 Mixed GC、G1及时返回未使用的已分配内存 |
| 13   | 2019.9   | 增强 ZGC 释放未使用内存、Socket API 重构、Switch 表达式扩展（预览功能）、文本块（预览功能） |
| 14   | 2020.3   | 完全支持改进的 switch 表达式、instanceof 支持模式匹配、record 特性、NullPointerException 精确到行、加入了 Java 打包工具 jpackage 的预览版 |
| 15   | 2020.9   | ZGC 将从实验功能升级为产品、char 在 CharSequence 中添加了 isEmpty 默认方法、支持 Unicode 13.0、隐藏类、TreeMap 方法的专用实现、增加了为远程JMX配置第三个端口的能力 |
| 16   | 2021.3   | record 正式使用、jpackage 工具正式使用、instanceof 正式使用  |
| 17   | 2021.9   | 增强了伪随机数算法、移除 AOT 提前编译和 JIT 即时编译的功能、sealed修饰的类和接口限制其他的类或者接口的扩展和实现、进一步增强了switch语法的模式匹配 |

较早的版本特性参考了《Java 核心技术：卷I 基础知识（原书第11版）》和《深入理解 Java 虚拟机：JVM 高级特性与最佳实践（第3版）》

# 各个版本的重要新特性

简单翻译整理了一下 Oracle 官方文档和 OpenJDK 的文档，个人挑出感觉和自己相关比较重要的新特性做个整合。遇到我不太熟悉的领域的内容的话，翻译可能不准确，对于这种地方，我尽量在括号里标注了英文原文或者直接保留英文原文。

可能有的重要的特性因为我自己用的少或者不了解（简单来说就是安全库、macOS以及一些底层实现等相关的都不太懂），没有被我放到这一章节；如果想看这些其他的新特性，可以看后面“各个版本的其他新特性（部分）”章节。删除的和弃用的特性也在后续章节中介绍。



## JDK 17

### JEP 409：密封类

**【规范】**

密封类（Sealed Class）已添加到 Java 语言中。密封的类和接口限制了哪些其他类或接口可以扩展或实现它们。

### JEP 406：switch 的模式匹配（预览版）

**【规范】**

通过对 switch 表达式和语句的模式匹配以及对模式语言的扩展来增强 Java 编程语言。将模式匹配扩展到 switch 允许针对多个模式测试表达式，每个模式都有特定的操作，因此可以简洁安全地表达复杂的面向数据的查询。

### JEP 382：新的 macOS 渲染管道

**【客户端库 / 2d】**

Swing API 用于渲染的 Java 2D API 现在可以使用适用于 macOS 的新 Apple Metal 加速渲染 API。

目前默认情况下禁用此功能，因此渲染仍使用 OpenGL API，Apple 已弃用这些 API，但仍可用并受支持。

要启用 Metal，应用程序应通过设置系统属性来指定其用途：

```sh
-Dsun.java2d.metal=true
```

使用 Metal 或 OpenGL 对应用程序是透明的，因为这是内部实现的差异，并且对 Java API 没有影响。Metal 管道需要 macOS 10.14.x 或更高版本。在早期版本上设置它的尝试将被忽略。

### JEP 356：增强的伪随机数生成器

**【核心库 / java.util】**

为伪随机数生成器 (pseudorandom number generator, PRNG) 提供新的接口类型和实现，包括可跳转 PRNG 和另一类可拆分 PRNG 算法 (LXM)。

### 理想图可视化器的现代化

**【HotSpot / 编译器】**

理想图可视化器（Ideal Graph Visualizer，IGV）是一种以可视化和交互方式探索 HotSpot VM C2 即时（JIT）编译器中使用的中间表示的工具，它已实现现代化。增强功能包括：

- 支持在最高 JDK 15 上运行 IGV（IGV 的底层 NetBeans 平台支持的最新版本）
- 更快、基于 Maven 的 IGV 构建系统
- 稳定块形成、组移除和节点跟踪
- 默认过滤器中更直观的着色（coloring）和节点分类
- 具有更自然默认行为的排名快速节点搜索

现代化的 IGV与早期 JDK 版本生成的图*部分*兼容。它支持图形加载和可视化等基本功能，但可能会影响节点聚类和着色等辅助功能。

### 错误消息中的源详细信息

**【工具 / javadoc】**

当 JavaDoc 在输入源文件中报告问题时，它会显示问题的源代码行，以及该行将包含指向具体位置的插入符号 `^`，其方式类似于编译器 ( `javac`) 诊断消息。

此外，现在将日志记录和其他“信息”消息写入标准错误流，将标准输出流用于命令行选项特别要求的输出，例如命令行帮助。

### “New API”的新页面和改进的“Deprecated”页面

**【工具 / javadoc】**

JavaDoc 现在可以生成一个总结 API 最近更改的页面。要包含的最新版本列表使用 `--since` 命令行选项指定。这些值用于查找 `@since` 要包含在新页面上的具有匹配标签的声明。`--since-label` 命令行选项提供在“新 API”页面的标题中使用的文本。

在总结弃用项目的页面上，您可以查看按弃用的版本分组的项目。

### JEP 412：外部函数和内存API（孵化器）

**【核心库】**

引入一个 API，Java 程序可以通过该 API 与 Java 运行时之外的代码和数据进行互操作。通过有效地调用外部函数（即 JVM 之外的代码）和安全地访问外部内存（即不受 JVM 管理的内存），API 使 Java 程序能够调用本机库并处理本机数据，而不会受 JNI 的脆弱性和危险的影响。

### 控制台字符集 API

**【核心库】**

`java.io.Console` 已更新以定义一个返回`Charset`控制台的新方法。返回的字符集可能与 `Charset.defaultCharset()` 方法返回的字符集不同。例如，它在 Windows (en-US) 上返回 `IBM437` 时 `Charset.defaultCharset()` 返回。

### 用于反序列化的 JDK Flight Recorder 事件

**【核心库 / java.io：序列化】**

现在可以使用 JDK Flight Recorder (JFR) 监控对象的反序列化。当启用 JFR 并且 JFR 配置包含反序列化事件时，每当运行的程序尝试反序列化对象时，JFR 都会发出事件。反序列化事件名为 `jdk.Deserialization`，默认情况下是禁用的。反序列化事件包含序列化过滤机制使用的信息。此外，如果启用了过滤器，JFR 事件会指示过滤器是接受还是拒绝对象的反序列化。

### JEP 415：实现特定于上下文的反序列化过滤器

**【核心库 / java.io：序列化】**

特定于上下文的反序列化过滤器 允许应用程序通过 JVM 范围的过滤器工厂配置特定于上下文和动态选择的反序列化过滤器，该过滤器工厂被调用来为每个单独的反序列化操作选择一个过滤器。

### 添加 java.time.InstantSource

**【核心库 / java.time】**

引入了一个新接口 `java.time.InstantSource`。这个接口是 `java.time.Clock` 的一个抽象，只关注当前时刻，不涉及到时区。

### 十六进制格式和解析实用程序

**【核心库 / java.util】**

`java.util.HexFormat` 为原始类型和字节数组提供与十六进制的转换。分隔符、前缀、后缀和大写或小写的选项由返回 HexFormat 实例的工厂方法提供。

### 实验编译器黑洞支持

**【HotSpot / 编译器】**

添加了对编译器黑洞（Compiler Blackholes）的实验性支持。这是为了避免在关键路径上的死代码消除，从而不会影响基准性能，对于低级基准测试很有用。当前支持以 CompileCommand 的形式实现，可以通过 `-XX:CompileCommand=blackhole,<method>` 使用，并计划最终将其升级为公共 API。

JMH（Java Microbenchmark Harness，Java 微基准测试工具套件） 在被命令或者可用时，已经能够自动检测和使用此工具。

### HotSpot JVM中新的类层次分析实现

**【HotSpot / 编译器】**

HotSpot JVM 中引入了新的类层次结构分析实现。它增强了对抽象和默认方法的处理，从而改进了 JIT 编译器做出的内联决策。新的实现取代了原来的实现，默认开启。

`-XX:+UnlockDiagnosticVMOptions -XX:-UseVtableBasedCHA` 为了帮助诊断与新实现相关的可能问题，可以通过指定命令行标志来打开原始实现。

在未来的版本中可能会删除原始实现。

### JEP 391：macOS/AArch64 端口

**【HotSpot / 编译器】**

macOS 11.0 现在支持 AArch64 架构。这个 JEP 在 JDK 中实现了对 macos-aarch64 平台的支持。添加的功能之一是支持 W^X（write xor execute, 写异或执行）内存。它仅对 macos-aarch64 启用，并且可以在某些时候扩展到其他平台。JDK 既可以在 Intel 机器上交叉编译，也可以在基于 Apple M1 的机器上编译。

### 统一日志支持异步日志刷新

**【HotSpot / 编译器】**

为了避免在使用统一日志的线程中出现不希望的延迟，用户现在可以请求统一日志系统以异步模式运行。这是通过传递命令行选项来完成的 `-Xlog:async`。在异步日志模式下，日志站点将所有日志消息排入缓冲区。独立线程负责将它们刷新到相应的输出。中间缓冲区是有界的。在缓冲区耗尽时，排队的消息将被丢弃。用户可以使用命令行选项控制中间缓冲区的大小 `-XX:AsyncLogBufferSize=<bytes>`。

### 包摘要页面上的“相关包”

**【工具 / javadoc】**

包的摘要页面现在包括列出任何“相关包”的部分。相关包的集合是根据通用命名约定启发式确定的，可能包括以下内容：

- “父”包（即包是子包的包）
- 同级包（即具有相同父包的其他包）
- 任何子包

相关的包不必都在同一个模块中。



## JDK 16

### JEP 389：外部链接器 API（孵化器）

**【核心库】**

引入一个 API，该 API 提供对本机代码的静态类型纯 Java 访问。此 API 与 Foreign-Memory API (JEP 393) 一起，将大大简化绑定到本机库的其他容易出错的过程。

### JEP 396：默认情况下对 JDK 内部进行强封装

**【核心库】**

默认情况下强烈封装 JDK 的所有内部元素，除了关键的内部 API，例如`sun.misc.Unsafe`. 允许最终用户选择自 JDK 9 以来一直默认的宽松强封装。

通过此更改，启动器选项的默认值 `--illegal-access` 现在是 `deny` 而不是 `permit`. 因此，使用 JDK 的大多数内部类、方法或字段的现有代码将无法运行。这样的代码可以通过指定在 JDK 16 上运行 `--illegal-access=permit`。但是，该选项将在未来的版本中删除。

### JEP 393：Foreign-Memory Access API（第三个孵化器）

**【核心库】**

引入 API 以允许 Java 程序安全有效地访问 Java 堆之外的外部内存。

### JEP 390：基于值的类的警告

**【核心库】**

*标准库提供的基于值的类*的用户——尤其是原始包装类的用户——应该避免依赖类实例的标识。强烈建议程序员不要调用包装类构造函数，该函数现在已弃用以在未来将其删除。新 `javac` 警告不鼓励对基于值的类实例进行同步。也可以使用命令行选项激活有关同步的运行时警告 `-XX:DiagnoseSyncOnValueBasedClasses`。

### 添加 InvocationHandler::invokeDefault 方法用于 Proxy 的默认方法支持

**【核心库 / java.lang : reflect】**

`java.lang.reflect.InvocationHandler`接口中添加了一个新方法 `invokeDefault` 以允许调用代理接口中定义的默认方法。

### java.time 格式添加了每日时段(Day Period)支持

**【核心库 / java.time】**

新的格式化程序模式、字母“B”及其支持方法已添加到`java.time.format.DateTimeFormatter/DateTimeFormatterBuilder`类中。该模式和方法转换每日时段（在Unicode 联盟的 CLDR 中定义的`day periods`）。应用程序现在可以表示一天中的时段，例如“早上”或“晚上”，而不仅仅是上午/下午。以下示例演示了转换每日时段：

```
DateTimeFormatter.ofPattern("B").format(LocalTime.now()) 
```

此示例根据一天中的时间和区域设置生成每日时段文本。

### 添加 Stream.toList() 方法

**【核心库 / java.util.stream】**

`java.util.Stream` 接口中添加了一个新方法 `toList`。因此，实现 `Stream` 接口的类或扩展 `Stream` 接口的接口从其他地方静态导入 `toList` 方法会有潜在源不兼容性，例如`Collectors.toList`. 对此类方法的引用必须更改为使用限定名称而不是静态导入。

### JEP 338：矢量 API（孵化器）

**【HotSpot / 编译器】**

提供孵化器模块 `jdk.incubator.vector` 的初始迭代，以表达向量计算，这些计算在运行时可靠地编译为支持的 CPU 架构上的最佳向量硬件指令，从而实现优于等效标量计算的性能。

### 改进的编译命令标志

**【HotSpot / 编译器】**

CompileCommand 标志具有已用于子命令集合的选项类型。这些命令的有效性没有经过验证，因此拼写错误会导致命令被忽略。他们有以下形式：

```sh
-XX:CompileCommand=option,<method pattern>,<option name>,<value type>,<value> 
```

现在所有选项命令都作为普通命令以这种形式存在：

```sh
-XX:CompileCommand=<option name>,<method pattern>,<value> 
```

验证选项名称并推断类型。如果命令名称不存在，或者值与命令的类型不匹配，则会给出有用的错误消息。所有命令名称都不区分大小写。

选项命令的旧语法仍然可以使用。已添加验证选项名称、值类型和值是否一致。

所有可用选项都可以列出：

```sh
-XX:CompileCommand=help 
```

### JEP 376：ZGC 并发堆栈处理

**【HotSpot / gc】**

Z 垃圾收集器（Z Garbage Collector，ZGC）现在同时处理线程堆栈。这允许 ZGC 在并发阶段处理 JVM 中的所有根，而不是停止世界暂停。ZGC 暂停中完成的工作量现在变得恒定，通常不超过几百微秒。

### 在 G1 中同时取消提交内存

**【HotSpot / gc】**

此新功能始终启用并改变了 G1 将 Java 堆内存返回给操作系统的时间。G1 仍然在 GC 暂停期间做出 sizing 决定，但将耗时的工作交给与 Java 应用程序并行的线程。

### JEP 387：弹性元空间

**【HotSpot / 运行时】**

JEP 387 “弹性元空间”彻底检查了 VM 内部元空间和类空间的实现。更少的内存用于类的元数据。在涉及大量小粒度类加载器的场景中，节省效果最为明显。类卸载后，内存及时返回给操作系统。

添加了一个开关来微调元空间回收：`-XX:MetaspaceReclaimPolicy=(balanced|aggressive|none)`。 `balanced`，作为默认设置，使 VM 回收内存，同时保持最小的计算开销；`aggressive` 以稍微昂贵的簿记为代价适度提高回收率；`none` 完全关闭回收。

开关 `InitialBootClassLoaderMetaspaceSize` 和 `UseLargePagesInMetaspace` 已被弃用。

### JEP 397：密封类（第二次预览）

**【工具 / javac】**

密封（sealed）类和接口在 JDK 16 中再次预览，最初在 JDK 15 中添加到 Java 语言中。密封类和接口限制了哪些其他类或接口可以扩展或实现它们。

### JEP 395：记录

**【工具 / javac】**

记录（record）已添加到 Java 语言中。记录是 Java 语言中的一种新类。它们充当不可变数据的透明载体，其惯例代码比普通类少。

自从嵌套类首次被引入 Java 以来，除了由常量表达式初始化的静态 final 字段外，内部的嵌套类声明已被禁止声明静态成员。此限制适用于非静态成员类、本地类和匿名类。

JEP 384: Records (Second Preview) 添加了对本地接口、枚举类和记录类的支持，所有这些都是静态定义。这是一个广受欢迎的增强功能，允许将某些声明的范围缩小到本地上下文的编码样式。

虽然 JEP 384 允许静态本地类和接口，但它并没有放松对静态成员类和内部类接口的限制。内部类可以在其方法体之一内声明静态接口，但不能作为类成员。

作为自然的下一步，JEP 395 进一步放宽了嵌套限制，并允许在内部类中声明静态类、方法、字段等。

### JEP 394：instanceof 的模式匹配

**【工具 / javac】**

运算符的模式匹配 `instanceof` 已成为 JDK 16 中 Java 语言的最终和永久特性。模式匹配允许更简洁和安全地表达 Java 程序中的通用逻辑，即从对象中条件提取组件。

### JEP 392：打包工具

**【工具 / jpackage】**

提供 `jpackage `工具，用于打包自包含的 Java 应用程序。JEP 343 在 JDK 14 中将其 `jpackage tool` 作为孵化工具引入。它仍然是 JDK 15 中的孵化工具，以便有时间获得额外的反馈。它已在 JDK 16 中从孵化升级为生产就绪功能。由于这种转变，`jpackage` 模块的名称已从 `jdk.incubator.jpackage` 更改为`jdk.jpackage`。



## JDK 15

### 为 CharSequence 添加了 isEmpty 默认方法

**【核心库 / java.lang】**

`java.lang.CharSequence` 已在此版本中更新定义 `isEmpty` 默认方法来测试一个字符序列是否为空。代码中常见的实例是用来测试和过滤空 `String` 和其他 `CharSequence` ，并且 `CharSequence::isEmpty` 可以用作方法引用。实现 `java.lang.CharSequence` 的类和其他定义 `isEmpty` 方法的接口应该注意这个新增特性，因为它们可能需要修改以重写该 `isEmpty` 方法。

### 支持 Unicode 13.0

**【核心库 / java.lang】**

此版本将 Unicode 支持升级到 13.0，其中包括以下内容：

-  `java.lang.Character` 支持 13.0 级别的 Unicode 字符数据库，13.0 增加了 5,930 个字符，总共 143,859 个字符。这些新增内容包括 4 个新脚本（script），总共 154 个脚本，以及 55 个新的表情符号字符。
- 和 `java.text.Bidi` 类 `java.text.Normalizer` 分别支持 13.0 级别的 Unicode 标准附件 #9 和 #15。
-  `java.util.regex` 包支持基于 Unicode 标准附件 #29 的 13.0 级别的扩展字素集群（Extended Grapheme Clusters）。

### JEP 371 隐藏类

**【核心库 / java.lang.invoke】**

JEP 371 在 Java 15 中引入了隐藏类（hidden class）。隐藏类对既有代码有以下影响：

1. `Class::getName` 传统上返回二进制名称，但对于隐藏类，它返回包含 ASCII 正斜杠 ( `/`) 的字符串，因此不是二进制名称。假设返回的字符串是二进制名称的程序可能需要更新以处理隐藏类。也就是说，`Unsafe::defineAnonymousClass` 长期以来的实践是用来定义名称不是二进制名称的类，因此某些程序可能已经可以成功处理这些名称。

2. `Class::descriptorString` 和 `MethodType::descriptorString` 为隐藏类返回一个内容为 ASCII 点 (`.`) 的字符串，因此不是符合 JVMS 4.3 的类型描述符。假设返回的字符串是符合 JVMS 4.3 的类型描述符的程序可能需要更新以处理隐藏类。

3. `Class::getNestMembers` 更改为：在无法验证 `NestMembers` 属性中列出的任何成员的嵌套成员身份（nest membership）时，不引发异常。相反，`Class::getNestMembers` 返回嵌套主（nest host）和主（host's） `NestMembers` 属性中列出的成功解析并确定与此类具有相同嵌套主的成员（这意味着可能它返回的成员比 `NestMembers` 属性中所列出的成员更少）。预期在存在错误嵌套成员资格时收到 `LinkageError` 的现有代码可能会受到影响。

4. JVM 中的 nestmate 测试改为仅 `IllegalAccessError` 在 nest 成员资格无效时才抛出。

   一些历史理解是必要的：

   - 在 Java 8 中，每个访问控制失败都用 `IllegalAccessError`(IAE) 发出信号。此外，如果一次给定的访问检查使用 IAE 失败，那么每次使用 IAE 的相同检查都会失败。
   - 在 Java 11 中，嵌套伙伴 (nest mates, JEP 181) 的引入意味着访问控制失败可以通过 `IllegalAccessError` 或者 `LinkageError`（如果嵌套成员资格无效时）. 尽管如此，如果给定的访问检查因特定异常而失败，那么相同的检查将始终因相同的异常而失败。
   - 在 Java 15 中，`Lookup::defineHiddenClass` 的引入意味着当隐藏类被定义为查找类（lookup class）的嵌套对象时，必须快速地确定查找类的嵌套主。两者 `Lookup::defineHiddenClass` 都 `Class::getNestHost` 以比 Java 11 中的 JVM 更具弹性的方式确定类的嵌套主；也就是说，如果一个类声称的嵌套成员资格无效，API 只会将其视为自托管（self-hosted）类。为了与 API 保持一致，当类的嵌套成员资格无效时，JVM 不再抛出异常 `LinkageError`，而是将类视为自托管。这意味着 JVM 只从访问控制中抛出 IAE（因为自托管类不允许任何其他类访问其私有成员）。这是绝大多数用户代码所期望的行为。

5. JVM TI `GetClassSignature ` 为隐藏类返回一个内容为 ASCII 点 (`.`)的字符串。如果 JVM TI 代理假定从 `GetClassSignature` 返回的字符串是符合 JVMS 4.3 的类型描述符，则它们可能需要更新隐藏类。

### TreeMap 方法的特殊实现

**【核心库 / java.util : collections】**

TreeMap 类现在提供了 `putIfAbsent`、`computeIfAbsent`、`computeIfPresent`、`compute` 和 `merge` 方法的重写实现。新的实现提供了性能改进。但是，如果供 `compute-` 开头的方法或者 `merge` 方法调用的函数修改了映射，则可能会抛出 ConcurrentModificationException，因为禁止供这些方法的函数修改映射。如果发生 ConcurrentModificationException，则必须更改函数以避免修改映射，或者应重写周围的代码以将 `compute-` 开头的方法和 `merge` 方法的使用替换为常规 Map 方法，例如`get` 和 `put`。

### JEP 378 文本块

**【工具 / javac】**

文本块已添加到 Java 语言中。文本块是一个多行字符串文字，它避免了大多数转义序列的需要，以可预测的方式自动格式化字符串，并在需要时让开发人员控制格式。



# 各个版本删除的功能和选项



## JDK 17

### JEP 403：强封装 JDK 内部

**【核心库】**

强封装（Strongly encapsulate） JDK 的所有内部元素，除了关键的内部 API，例如 `sun.misc.Unsafe`.

通过此更改，`java` 启动器选项`--illegal-access` 已过时。如果在命令行上使用，则会发出警告消息，否则无效。必须使用 JDK 的内部类、方法或字段的现有代码仍然可以通过使用`--add-opens` 启动器选项或 `Add-Opens` 文件清单属性来打开特定包来工作。

### JEP 407：删除 RMI 激活

**【核心库 / java.rmi】**

远程方法调用 (RMI) 激活机制已被删除。RMI 激活是 RMI 的一个过时部分，自 Java SE 8 以来一直是可选的。 RMI 激活已被 Java SE 15 中的 JEP 385弃用，并且已被 JEP 407 从此版本中删除。该 `rmid` 工具也已被删除。有关背景、基本原理、风险和替代方案，请参阅 JEP 385。RMI 的其余部分保持不变。

### JEP 410：删除实验性 AOT 和 JIT 编译器

**【HotSpot / 编译器】**

HotSpot VM 中的 AOT 编译器相关代码已被删除。使用 JEP295 定义的 HotSpot VM 选项会在 VM 初始化时产生“无法识别的 VM 选项”错误。

### 删除 sun.misc.Unsafe::defineAnonymousClass

**【核心库】**

### 删除 Telia 公司的 Sonera Class2 CA 证书

**【安全库/java.security】**



## JDK 16

### 删除 java.awt.PeerFixer

**【客户端库 / java.awt】**

非公共类 `java.awt.PeerFixer` 在此版本中已删除。此类用于为在 JDK 1.1.1 之前创建的 ScrollPane 对象提供反序列化支持。

### 去除实验特征 AOT 和 Graal JIT

**【HotSpot / 编译器】**

Java Ahead-of-Time 编译实验工具 `jaotc` 已被删除。使用 JEP295 定义的 HotSpot VM 选项会产生不支持的选项警告，否则将被忽略。

实验性的基于 Java 的 JIT 编译器 Graal JEP317 已被删除。尝试使用它会产生 JVMCI 错误：`JVMCI compiler 'graal' not found`.

### 已弃用的跟踪标志已过时，必须用统一日志等效项替换

**【HotSpot / 运行时】**

在 Java 9 中添加统一日志记录时，许多跟踪标志被弃用并映射到它们的统一日志记录等效项。这些标志现在已过时，将不再自动转换以启用统一日志记录。要继续获得相同的日志输出，您必须将这些标志的使用明确替换为它们的统一日志等效项。

| 过时的选项               | 统一日志替换            |
| ------------------------ | ----------------------- |
| -XX:+TraceClassLoading   | -Xlog:class+load=info   |
| -XX:+TraceClassUnloading | -Xlog:class+unload=info |
| -XX:+TraceExceptions     | -Xlog:exceptions=info   |

### 删除了 1024 位密钥的根证书

**【安全库 / java.security】**

一些具有弱 1024 位 RSA 公钥的根证书已从 `cacerts` 密钥库中删除

### 去除传统的椭圆曲线

**【安全库 / javax.crypto】**

SunEC 提供程序不再支持一些已过时或未使用现代公式和技术实现的椭圆曲线。

## JDK 15

### 删除最终弃用的 Solaris 特定 SO_FLOW_SLA 套接字选项

**【核心库 / java.net】**

在此版本中，随着 JEP381 ( JDK-8241787 ) 中 Solaris 端口的删除，JDK 特定的套接字选项 `jdk.net.ExtendedSocketOptions.SO_FLOW_SLA`（仅与 Solaris 上的套接字相关）及其支持类 `SocketFlow` 和 `SocketFlow.Status` 已被删除。

### 删除 RMI 静态存根编译器 (rmic)

**【核心库 / java.rmi】**

RMI 静态存根编译器（RMI Static Stub Compiler）`rmic` 已被删除。该 `rmic` 工具已过时，自 JDK 13 起为了在未来将其删除已标为弃用。

### 删除已弃用的常量 RMIConnectorServer.CREDENTIAL_TYPES

**【核心svc / javax.management】**

最终弃用的常量 `javax.management.remote.rmi.RMIConnectorServer.CREDENTIAL_TYPE` 已被删除。可以使用 `RMIConnectorServer.CREDENTIALS_FILTER_PATTERN` 指定过滤器模式。

### 移除 Nashorn JavaScript 引擎

**【核心库 / jdk.nashorn】**

Nashorn JavaScript 脚本引擎、其 API 和 `jjs` 工具已被删除。该引擎、API 和该工具在 Java 11 中已被弃用，以便在未来的版本中删除它们

### 已过时 -XXUseAdaptiveGCBoundary

**【HotSpot / gc】**

VM 选项 `UseAdaptiveGCBoundary` 已过时。使用此选项将产生过时选项警告，否则将被忽略。

此选项以前默认禁用，启用它仅在使用 `-XX:+UseParallelGC`. 启用它旨在为某些应用程序提供性能优势。但是，由于崩溃和性能下降，它已默认禁用很长时间。

### 移除 Comodo 根 CA 证书

**【安全库 / java.security】**

### 删除 DocuSign 根 CA 证书

**【安全库 / java.security】**

### 弃用了已弃用的 SSLSession.getPeerCertificateChain() 方法实现

**【安全库 / javax.net.ssl】**

`SSLSession.getPeerCertificateChain() `已从 SunJSSE 提供程序和 HTTP 客户端实现中的 JDK 中删除了已弃用方法的实现。此方法的默认实现已更改为抛出 UnsupportedOperationException。

`SSLSession.getPeerCertificateChain()` 是一个不推荐使用的方法，将在未来的版本中删除。为了减轻删除兼容性的影响，应用程序应该改用该 `SSLSession.getPeerCertificates()` 方法。对于服务提供商，请从现有实现中删除此方法，并且在任何新实现中不支持此方法。

### 删除 com.sun.net.ssl.internal.ssl.Provider 名称

**【安全库 / javax.net.ssl】**

旧的 SunJSSE 提供程序名称 “com.sun.net.ssl.internal.ssl.Provider” 已被删除，不应再使用。应改为使用 “SunJSSE” 名称。例如， `SSLContext.getInstance("TLS", "SunJSSE")`。



# 各个版本弃用的功能和选项



## JDK 17

### JEP 398：弃用 Applet API 以进行删除

**【客户端库 / java.awt】**

### JEP 411：弃用安全管理器以进行删除

**【安全库 / java.security】**

### 在 Kerberos 中弃用 3DES 和 RC4

**【安全库 / org.ietf.jgss : krb5】**

### 弃用 Socket 实现工厂机制

**【核心库 / java.net】**

以下用于设置系统范围的套接字实现工厂的静态方法已被弃用：

- `static void ServerSocket.setSocketFactory(SocketImplFactory fac)`
- `static void Socket.setSocketImplFactory(SocketImplFactory fac)`
- `static void DatagramSocket.setDatagramSocketImplFactory(DatagramSocketImplFactory fac)`

这些 API 点用于为 `java.net` 包中的相应套接字类型静态配置系统范围的工厂。自 Java 1.4 以来，这些方法大多已过时。

### 弃用 JVM TI 堆函数 1.0

**【HotSpot / jvmti】**



## JDK 16

### 最终弃用的线程组停止、销毁、isDestroyed、setDaemon 和 isDaemon

**【核心库 / java.lang】**

`java.lang.ThreadGroup` 定义的 `stop`、`destroy`、`isDestroyed`、`setDaemon` 和 `isDaemon` 方法在此版本中已被最终弃用（terminally deprecated）。

`ThreadGroup::stop` 本质上是不安全的，并且自 Java 1.2 起已被弃用。该方法现在已被最终弃用，并将在未来的版本中删除。

用于销毁 ThreadGroup 的 API 和机制存在固有缺陷。支持显式或自动销毁线程组的方法已被最终弃用，并将在未来的版本中删除。

### 部分 Signal-Chaining API 已弃用

**【HotSpot / 运行时】**

信号链工具是在 JDK 1.4 中引入的，它支持三种不同的 Linux 信号处理 API： `sigset`、`signal` 和 `sigaction`。只有 `sigaction` 是用于多线程进程的跨平台、受支持的 API。`signal` 和 `sigset` 两者在仍然定义它们的那些平台上都被认为是过时的。因此，现在  `signal` 和 `sigset` 与信号链工具一起使用已被弃用，并且在未来的版本中将删除对其使用的支持。

### 弃用了将 DN 表示为主体或字符串对象的 java.security.cert API

**【安全库 / java.security】**

这些 API 采用或返回可分辨名称（Distinguished Names）作为 `Principal` 或 `String` 对象，并且在比较不同主体（Principal）实现之间的名称时，可能会由于编码信息的丢失或差异而导致问题。它们都有使用 `X500Principal` 对象的替代 API。



## JDK 15

###  已弃用的 RMI 激活删除

**【核心库 / java.rmi】**

RMI 激活机制已被弃用，可能会在平台的未来版本中删除。RMI 激活是 RMI 的一个过时部分，自 Java 8 以来一直是可选的。它允许 RMI 服务器 JVM 在收到来自客户端的请求时启动（“激活, activated”），而不是要求 RMI 服务器 JVM 连续运行。RMI 的其他部分并未弃用。

### 已弃用的 NSWindowStyleMaskTexturedBackground

**【文档】**

升级用于构建 JDK 的 macOS SDK 后，`apple.awt.brushMetalLook` 和 `textured` Swing 属性的行为发生了变化。设置这些属性后，框架的标题仍然可见。建议将该 `apple.awt.transparentTitleBar ` 属性设置为 `true` 使框架的标题再次不可见。`apple.awt.fullWindowContent` 也可以使用该属性。

请注意，`Textured window` 支持是通过使用 `NSWindowStyleMask` 的 `NSTexturedBackgroundWindowMask`值实现的。但是，这在 macOS 10.12 中已和在 macOS 10.14 中已被弃用的`NSWindowStyleMaskTexturedBackground` 一起被弃用。

### 不推荐使用 -XXForceNUMA 选项

**【HotSpot / gc】**

不推荐使用VM 选项 `ForceNUMA`。使用此选项将产生弃用警告。此选项将在未来的版本中删除。

默认情况下始终禁用此选项。它的存在是为了支持在单节点 / UMA 平台上运行时测试与 NUMA 相关的代码路径。

### 禁用偏向锁定和不推荐使用的偏向锁定标志

**【HotSpot / 运行时】**

在此版本中，默认情况下已禁用偏向锁定。此外，VM 选项 `UseBiasedLocking` 以及 VM 选项 `BiasedLockingStartupDelay`、`BiasedLockingBulkRebiasThreshold`、`BiasedLockingBulkRevokeThreshold`、`BiasedLockingDecayTime` 和 `UseOptoBiasInlining` 已被弃用。这些选项将继续按预期工作，但在使用时会生成弃用警告。

偏向锁定可能会影响表现出大量非竞争同步的应用程序的性能，例如依赖于每次访问时同步的旧 Java Collections API 的应用程序。这些 API 例如 `Hashtable ` 和 `Vector` 。在命令行上使用 `-XX:+BiasedLocking` 可重新启用偏向锁定。在禁用偏向锁定的情况下有显着的性能回归请向 Oracle 报告。

### 默认禁用本机 SunEC 实现

**【安全库 / javax.crypto】**

SunEC 加密提供商不再宣传未使用现代公式和技术实现的曲线。本说明底部列出的任意曲线和命名曲线均已禁用。SunEC 仍然支持和启用常用的命名曲线 secp256r1、secp384r1、secp521r1、x25519 和 x448，因为它们使用现代技术。仍需要 SunEC 提供程序禁用的曲线的应用程序可以通过将 System 属性 `jdk.sunec.disableNative` 设置为 `false` 来重新启用它们。例如：`java -Djdk.sunec.disableNative=false ...`。

如果此属性设置为任何其他值，则曲线将保持禁用状态。禁用曲线时引发的异常将包含消息 `Legacy SunEC curve disabled`，后跟曲线名称。受更改影响的方法是`KeyPair.generateKeyPair()`、`KeyAgreement.generateSecret()`、`Signature.verify()` 和 `Signature.sign()`. 当曲线不受支持时，这些方法会抛出与之前相同的异常类。

### 为先前弃用的 ContentSigner API 添加了 forRemoval=true

**【安全库 / jdk.security】**

`com.sun.jarsigner` 包中的 `ContentSigner` 和 `ContentSignerParameters` 类支持替代签名者，并且已和 `forRemoval=true` 一起被弃用。当 `-altsigner` 或者 `-altsignerpath` 选项被指定时，`jarsigner` 工具会发出警告，指出这些选项已弃用并将被删除。



# 各个版本的其他新特性



## JDK 17

### 用于访问大图标的新 API

**【客户端库 / javax.swing】**

JDK 17 中提供了一种新方法，`javax.swing.filechooser.FileSystemView.getSystemIcon(File, int, int)` 可以在可能的情况下访问更高质量的图标。这个方法完全针对 Windows 平台实现，其他平台上的结果可能会有所不同但以后会得到增强。例如，通过使用以下代码：

```java
FileSystemView fsv = FileSystemView.getFileSystemView();
Icon icon = fsv.getSystemIcon(new File("application.exe"), 64, 64);
JLabel label = new JLabel(icon);
```

用户可以获得更高质量的“application.exe”文件图标。此图标适用于创建可以在 HighDPI 环境中更好地缩放的标签。

### DatagramSocket 可以直接用于加入多播组

**【核心库 / java.net】**

`java.net.DatagramSocket` 已在此版本中更新以添加对加入多播组（multicast groups）的支持。它现在定义了加入和离开多播组的方法 `joinGroup`。`leaveGroup` 的类级别 API 文档 `java.net.DatagramSocket` 已更新，以解释如何 `DatagramSocket` 配置并使用普通文本加入和离开多播组。

此更改意味着`DatagramSocket` API 可用于多播应用程序，而无需使用旧版 `java.net.MulticastSocket` API。尽管它的 `MulticastSocket` 大多数方法都已弃用，但 API 像以前一样工作。

### 在 macOS 上添加对 UserDefinedFileAttributeView 的支持

**【核心库 / java.nio】**

macOS 上的文件系统提供程序实现已在此版本中更新，以支持扩展属性。`java.nio.file.attribute.UserDefinedFileAttributeView` API 现在可用于获取文件扩展属性的视图（view）。以前的 JDK 版本不支持此（可选）视图。

###  ARM 上的 macOS 早期访问（Early Access）可用

**【基础设施 / 建设】**

新的 macOS 现在可用于 ARM 系统。ARM 端口的行为应与 Intel 端口类似。没有已知的功能差异。在 macOS 上报告问题时，请指定是使用 ARM 还是 x64。

### 支持在 Keytool -genkeypair 命令中指定签名者

**【安全库 / java.security】**

`-signer` 和 `-signerkeypass` 选项已添加到 `keytool` 功能的 `-genkeypair` 命令中。`-signer` 选项指定签名者的 `PrivateKeyEntry` 密钥库别名，`-signerkeypass` 选项指定用于保护签名者私钥的密码。这些选项允许 `keytool -genkeypair` 使用签名者的私钥对证书进行签名。这对于使用密钥协商算法作为其公钥算法生成证书特别有用。

### SunJCE Provider 使用 AES 密码支持 KW 和 KWP 模式

**【安全库 / javax.crypto】**

SunJCE 提供程序已得到增强，以支持 AES 密钥包装算法 (RFC 3394) 和 AES 带填充算法的密钥包装 (RFC 5649)。在早期版本中，SunJCE 提供程序在“AESWrap”密码算法下支持 RFC 3394，该算法只能用于包装和解包密钥。通过此增强功能，添加了两种分组密码模式 KW 和 KWP，它们支持使用 AES 进行数据加密/解密和密钥打包/解包。

### 新的 SunPKCS11 配置属性

**【安全库 / javax.crypto : pkcs11】**

SunPKCS11 提供程序添加了新的提供程序配置属性，以更好地控制本机资源的使用。SunPKCS11 提供程序使用本机资源以使用本机 PKCS11 库。为了管理和更好地控制原生资源，增加了额外的配置属性来控制清除原生引用的频率以及注销后是否销毁底层PKCS11 Token。

SunPKCS11 提供程序配置文件的 3 个新属性是：

1. `destroyTokenAfterLogout`（布尔值，默认为 false）如果设置为 true，`java.security.AuthProvider.logout()` 则在调用 SunPKCS11 提供程序实例时，将销毁底层 Token 对象并释放资源。这实质上使 SunPKCS11 提供程序实例在 logout() 调用后无法使用。请注意，不应将此属性设置为 true 的 PKCS11 提供程序添加到系统提供程序列表中，因为提供程序对象在 logout() 方法调用后不可用。
2. `cleaner.shortInterval`（整数，默认为2000，以毫秒为单位）这定义了在繁忙期间清除本地引用的频率，即清理线程应该多久处理一次队列中不再需要的本地引用以释放本地内存。请注意，清洁线程将在 200 次失败尝试后切换到 “longInterval” 频率，即在队列中没有找到引用时。
3. `cleaner.longInterval`（整数，默认为 60000，以毫秒为单位）这定义了在非忙碌期间检查原生引用的频率，即清理线程应该多久检查一次队列中的原生引用。请注意，如果检测到用于清理的本机 PKCS11 引用，则清理线程将切换回 “shortInterval” 值。

### 如果 PKCS11 库支持，SunPKCS11 提供程序支持 ChaCha20-Poly1305 Cipher 和 ChaCha20 KeyGenerator

**【安全库 / javax.crypto : pkcs11】**

当底层 PKCS11 库支持相应的 PKCS#11 机制时，SunPKCS11 提供程序得到增强以支持以下加密服务和算法：

- ChaCha20 KeyGenerator <=> CKM_CHACHA20_KEY_GEN 机制
- CHACHA20-POLY1305 密码 <=> CKM_CHACHA20_POLY1305 机制
- CHACHA20-POLY1305 算法参数 <=> CKM_CHACHA20_POLY1305 机制
- CHACHA20 SecretKeyFactory <=> CKM_CHACHA20_POLY1305 机制

### 具有系统属性的可配置扩展

**【安全库 / javax.net.ssl】**

添加了两个新的系统属性。系统属性 `jdk.tls.client.disableExtensions` 用于禁用客户端中使用的 TLS 扩展。系统属性 `jdk.tls.server.disableExtensions` 用于禁用服务器中使用的 TLS 扩展。如果扩展被禁用，它将不会在握手消息中生成或处理。

属性字符串是逗号分隔的标准 TLS 扩展名列表，在 IANA 文档中注册（例如，server_name、status_request 和 signature_algorithms_cert）。请注意，扩展名区分大小写。未知、不受支持、拼写错误和重复的 TLS 扩展名令牌将被忽略。

请注意，阻止 TLS 扩展的影响是复杂的。例如，如果禁用强制扩展，则可能无法建立 TLS 连接。请不要禁用强制扩展，也不要使用此功能，除非您清楚了解其影响。

### 如果 default_tkt_enctypes 或 default_tgs_enctypes 不存在，则使用 allowed_enctypes

**【安全库 / org.ietf.jgss : krb5】**

使用 allowed_enctypes 作为 default_tkt_enctypes 或 default_tgs_enctypes 的默认值（如果它们中的任何一个未在 krb5.conf 中定义）。



## JDK 16

### JEP 380：Unix 域套接字

**【核心库 / java.nio】**

`java.nio.channels`在、`SocketChannel`和`ServerSocketChannel`类中提供对 Unix 域套接字 (AF_UNIX) 的支持。

### 默认启用新的 jdk.ObjectAllocationSample 事件

**【HotSpot / jfr】**

引入了一个新的 JFR 事件 ，`jdk.ObjectAllocationSample`，以允许始终在线（always-on）、低开销（low-overhead）的分配分析。该事件在`default` 和 `profile` 配置中都被启用，最大速率分别为 150 和 300 个事件/秒。事件 `jdk.ObjectAllocationInNewTLAB` 以及 `jdk.ObjectAllocationOutsideTLAB` 详细描述了内存分配，但开销相对较高。它们以前在 `default` 配置中被禁用，但现在也在 `profile` 配置中被禁用以进一步减少开销。

此更改的影响可以在 JDK Mission Control (JMC) 8.0 或更早版本中看到，其中“TLAB 分配”页面将丢失数据。建议在 `jdk.ObjectAllocationSample` 支持可用时升级到更高版本的 JMC 。同时，可以在 JMC 录制向导（recording wizard）中启用 `jdk.ObjectAllocationInNewTLAB` 和 `jdk.ObjectAllocationOutsideTLAB` 事件，或手动编辑 .jfc 文件。

### 对 RSASSA-PSS 和 EdDSA 的签名 JAR 支持

**【安全库 / java.security】**

此增强功能包括两个主要更改：

1. JarSigner API 和 `jarsigner` 工具现在支持使用 RSASSA-PSS 或 EdDSA 密钥对 JAR 文件进行签名。
2. `.SF `不是直接对文件进行签名，而是 `jarsigner` 创建一个 SignerInfo signedAttributes 字段，包含 ContentType、MessageDigest、SigningTime 和 CMSAlgorithmProtection 。如果 `jarsigner -altsigner` 选项指定了替代签名机制，则不会生成该字段。请注意，虽然此字段在此代码更改之前并不是由 `jarsigner` 生成，但此前在解析签名时一直支持。这意味着带有该字段的新签名的 JAR 文件可以通过早期的 JDK 版本进行验证。

### SUN、SunRsaSign 和 SunEC 提供程序支持基于 SHA-3 的签名算法

**【安全库 / java.security】**

SUN、SunRsaSign 和 SunEC 提供程序（provider）已得到增强，以支持基于 SHA-3 的签名算法。现在通过这些提供程序支持具有 SHA-3 系列摘要的 DSA 签名、RSA 和 ECDSA 签名实现。此外，当在签名参数中指定时，来自 SunRsaSign 提供程序的 RSASSA-PSS 签名实现可以识别 SHA-3 系列摘要。

### jarsigner 保留 POSIX 文件权限和符号链接属性

**【安全库 / java.security】**

在对包含 POSIX 文件权限或符号链接属性的文件进行签名时，`jarsigner` 现在将这些属性保留在新签名的文件中，但会警告这些属性未签名且不受签名保护。 `jarsigner -verify` 在此类文件的操作过程中会打印相同的警告。

请注意，该 `jar `工具不会读取/写入这些属性。`unzip` 对于保留这些属性的工具，这种变化更加明显。

### 在 keytool -printcert 和 -printcrl 命令中添加了 -trustcacerts 和 -keystore 选项

**【安全库 / java.security】**

`-trustcacerts` 和 `-keystore` 选项已添加到 `keytool` 实用程序的 `-printcert` 和 `-printcrl` 命令中。如果证书是用户密钥库或 `cacerts` 密钥库中的受信任证书，`-printcert` 命令不会检查证书签名算法的弱点。 `-printcrl` 命令使用来自用户密钥库或 `cacerts` 密钥库的证书验证 CRL，如果无法验证，将打印出警告。

### SunPKCS11 Provider 支持 SHA-3 相关算法

**【安全库 / javax.crypto】**

SunPKCS11 提供程序已更新支持 SHA-3 算法。还添加了对使用 SHA-3 以外的消息摘要的 Hmac 的附加 KeyGenerator 支持。当底层 PKCS11 库支持相应的 PKCS11 机制时，SunPKCS11 提供程序现在支持以下附加算法：

- 消息摘要：SHA3-224、SHA3-256、SHA3-384、SHA3-512
- Mac：HmacSHA3-224、HmacSHA3-256、HmacSHA3-384、HmacSHA3-512
- 签名：SHA3-224withDSA，SHA3-256withDSA，SHA3-384withDSA，SHA3-512withDSA，SHA3-224withDSAinP1363Format，SHA3-256withDSAinP1363Format，SHA3-384withDSAinP1363Format，SHA3-512withDSAinP1363Format，SHA3-224withECDSA，SHA3-256withECDSA，SHA3-384withECDSA，SHA3-512withECDSA， SHA3-224withECDSAinP1363Format，SHA3-256withECDSAinP1363Format，SHA3-384withECDSAinP1363Format，SHA3-512withECDSAinP1363Format，SHA3-224withRSA，SHA3-256withRSA，SHA3-384withRSA，SHA3-512withRSA，SHA3-224withRSASSA-PSS，SHA3-256withRSASSA-PSS，SHA3-384withRSASSA-PSS， SHA3-512 和 RSASSA-PSS。
- 密钥生成器：HmacMD5、HmacSHA1、HmacSHA224、HmacSHA256、HmacSHA384、HmacSHA512、HmacSHA512/224、HmacSHA512/256、HmacSHA3-224、HmacSHA3-256、HmacSHA3-384、HmacSHA3-512。

### 改进证书链处理

**【安全库 / javax.net.ssl】**

添加了一个新的系统属性 ，`jdk.tls.maxHandshakeMessageSize ` 以设置 TLS/DTLS 握手中握手消息的最大允许大小。系统属性的默认值为 32768（32 KB）。

添加了一个新的系统属性 ，`jdk.tls.maxCertificateChainLength` 用于设置 TLS/DTLS 握手中证书链的最大允许长度。系统属性的默认值为 10。

### 改进 TLS 应用层协议协商 (ALPN) 值的编码

**【安全库 / javax.net.ssl】**

SunJSSE 提供程序无法正确读取或写入某些 TLS ALPN 值。这是由于选择字符串作为 API 接口以及未记录的内部使用 UTF-8 字符集，它将大于 U+00007F（7 位 ASCII）的字符转换为多字节数组，这可能不是同行。

ALPN 值现在使用对等方期望的网络字节表示来表示，对于标准的 7 位基于 ASCII 的字符串应该不需要修改。但是，SunJSSE 现在将字符串字符编码/解码为 8 位 ISO_8859_1/LATIN-1 字符。这意味着使用 U+000007F 以上字符且之前使用 UTF-8 编码的应用程序可能需要修改以执行 UTF-8 转换，或者将 Java 安全属性设置 *`jdk.tls.alpnCharset`* 为 “UTF-8” 恢复行为。

### EdDSA 签名算法的 TLS 支持

**【安全库 / javax.net.ssl】**

SunJSSE 提供程序现在支持使用 EdDSA 签名算法。具体来说，SunJSSE 可以使用包含 EdDSA 密钥的证书进行服务器端和客户端身份验证，并且可以使用用 EdDSA 算法签名的证书。此外，需要数字签名的 TLS 握手消息支持 EdDSA 签名。



## JDK 15

### 添加了对 SO_INCOMING_NAPI_ID 的支持

**【核心库 / java.net】**

此版本中 `SO_INCOMING_NAPI_ID` 添加了一个新的特定于 JDK 的套接字选项。`jdk.net.ExtendedSocketOptions` 套接字选项是 Linux 特定的，允许应用程序查询与其套接字连接关联的底层设备队列的 NAPI（新 API）ID，并利用高性能网络接口卡 (NIC) 设备的应用程序设备队列 (ADQ) 功能.

### 添加了为远程 JMX 配置第三个端口的功能

**【核心svc / javax.management】**

通过设置以下属性，JMX 通过配置两个网络端口（从命令行或在属性文件中）支持（显式）远程网络访问：

```properties
com.sun.management.jmxremote.port=<port#>
com.sun.management.jmxremote.rmi.port=<port#>
```

注意：如果未指定，则第二个端口将默认为第一个。

还打开了第三个本地端口以接受（本地）JMX 连接。该端口之前是随机选择的，这可能会导致端口冲突。

但是，现在可以使用以下方法配置第三个 JMX 端口（仅限本地）：

~~~properties
com.sun.management.jmxremote.local.port=<port#>
~~~

### 为 jstatd 添加了用于指定 RMI 连接器端口号的新选项

**【核心svc / 工具】**

该命令中添加了一个新 `-r <port>` 选项，`jstatd` 用于指定 RMI 连接器端口号。如果未指定端口号，则使用随机可用端口。

### 为 jcmd 添加了用于编写 gzipped 堆转储的新选项

**【核心svc / 工具】**

诊断命令 `gz` 中添加了一个新的整数选项。`GC.heap_dump` 如果指定，它将启用写入堆转储的 gzip 压缩。提供的值是压缩级别。它的范围可以从 1（最快）到 9（最慢，但最好的压缩）。推荐级别为 1。

### 为调试模式添加到 jhsdb 的新选项

**【HotSpot / svc代理】**

`jhsdb` 调试模式的命令中添加了三个新选项：

1. `--rmiport <port> `用于指定 RMI 连接器端口号。如果未指定端口号，则使用随机可用端口。
2. `--registryport <port> `用于指定 RMI 注册表端口号。此选项覆盖系统属性 `sun.jvm.hotspot.rmi.port`。如果未指定端口号，则使用系统属性。如果未设置系统属性，则使用默认端口 1099。
3. `--hostname <hostname>` 用于指定 RMI 连接器主机名。该值可以是主机名或 IPv4/IPv6 地址。此选项覆盖系统属性 `java.rmi.server.hostname`。如果未指定主机名，则使用系统属性。如果未设置系统属性，则使用系统主机名。

### 适用于 Windows 的 Oracle JDK 安装程序在可从任何命令提示符访问的路径中提供可执行文件（javac 等）

**【安装 / 安装】**

适用于 Windows 的 Oracle JDK 安装程序在系统位置提供 `java.exe`、`javaw.exe`、`javac.exe` 和 `jshell.exe` 命令，以便用户可以运行 Java 应用程序，而无需提供 Oracle JDK 安装文件夹的路径。

### 为 jarsigner 添加了撤销检查

**【安全库 / java.security】**

该命令中添加了一个新 `-revCheck` 选项，`jarsigner` 以启用证书的吊销检查。

### 如果使用了弱算法，工具会发出警告

**【安全库 / java.security】**

和工具已更新，可在密钥、证书和签名 JAR 中使用弱加密算法时警告用户，然后再禁用它们 `keytool`。`jarsigner` 弱算法在配置文件的 `jdk.security.legacyAlgorithms` 安全属性中设置。`java.security` 在此版本中，这些工具会针对 SHA-1 哈希算法和 1024 位 RSA/DSA 密钥发出警告。

### SunJCE Provider 支持基于 SHA-3 的 Hmac 算法

**【安全库 / javax.crypto】**

SunJCE 提供程序已得到增强，以支持 HmacSHA3-224、HmacSHA3-256、HmacSHA3-384 和 HmacSHA3-512。这些算法的实现在 Mac 和 KeyGenerator 服务下可用。Mac 服务生成 keyed-hash，而 KeyGenerator 服务为 Mac 生成密钥。

### 用于配置 TLS 签名方案的新系统属性

**【安全库 / javax.net.ssl】**

添加了两个新的系统属性来自定义 JDK 中的 TLS 签名方案。`jdk.tls.client.SignatureSchemes` 已为 TLS 客户端添加，并 `jdk.tls.server.SignatureSchemes` 已为服务器端添加。

每个系统属性都包含一个以逗号分隔的受支持签名方案名称列表，指定可用于 TLS 连接的签名方案。

### 支持 certificate_authorities 扩展

**【安全库 / javax.net.ssl】**

“certificate_authorities” 扩展是 TLS 1.3 中引入的可选扩展。它用于指示端点支持的证书颁发机构 (CA)，接收端点应使用它来指导证书选择。

在此 JDK 版本中，客户端和服务器端都支持 TLS 1.3 的 “certificate_authorities” 扩展。此扩展始终存在于客户端证书选择中，而对于服务器证书选择是可选的。

`jdk.tls.client.enableCAExtension` 应用程序可以通过将系统属性设置为 来启用此扩展以进行服务器证书选择 `true`。该属性的默认值为 `false`。

请注意，如果客户端信任的 CA 超过扩展的大小限制（小于 2^16 字节），则不会启用扩展。此外，一些服务器实现不允许握手消息超过 2^14 字节。因此，当 `jdk.tls.client.enableCAExtension` 设置为 `true` 并且客户端信任的 CA 多于服务器实现限制时，可能会出现互操作性问题。

### 支持 krb5.conf 中的规范化

**【安全库 / org.ietf.jgss : krb5】**

JDK Kerberos 实现现在支持 krb5.conf 文件中的 “canonicalize” 标志。当设置为 *true* 时，客户端在对 KDC 服务（AS 协议）的 TGT 请求中请求 RFC 6806名称规范化。否则，默认情况下，不请求它。

新的默认行为不同于 JDK 14 和以前的版本，在这些版本中，客户端始终在对 KDC 服务的 TGT 请求中请求名称规范化（前提是未使用 *sun.security.krb5.disableReferrals* 系统或安全属性明确禁用对 RFC 6806 的支持）。

### 支持跨域MSSFU

**【安全库 / org.ietf.jgss : krb5】**

对 Kerberos MSSFU 扩展 [1] 的支持现在扩展到跨领域环境。

通过利用在 JDK-8215032 的上下文中引入的 Kerberos 跨领域引用增强功能，可以使用 “S4U2Self” 和 “S4U2Proxy” 扩展来模拟位于不同领域的用户和服务主体。



# 一些原始资料的链接

## Oracle

Oracle Java 的各个版本新特性文档：

简单来说，就是在 Oracle 的 Java SE 页面（https://docs.oracle.com/en/java/javase/index.html）里面点各个版本，然后找到“What's New”的链接。11 版本前的需要特别找一下，页面不一样。

| 版本 | 文档链接                                                     |
| ---- | ------------------------------------------------------------ |
| 5.0  | https://docs.oracle.com/javase/1.5.0/docs/relnotes/features.html |
| 6    | https://www.oracle.com/java/technologies/javase/features.html |
| 7    | https://www.oracle.com/java/technologies/javase/jdk7-relnotes.html |
| 8    | https://www.oracle.com/java/technologies/javase/8-whats-new.html |
| 9    | https://docs.oracle.com/javase/9/whatsnew/toc.htm            |
| 10   | https://www.oracle.com/java/technologies/javase/10-relnote-issues.html |
| 11   | https://www.oracle.com/java/technologies/javase/11-relnote-issues.html#NewFeature |
| 12   | https://www.oracle.com/java/technologies/javase/12-relnote-issues.html#NewFeature |
| 13   | https://www.oracle.com/java/technologies/javase/13-relnote-issues.html#NewFeature |
| 14   | https://www.oracle.com/java/technologies/javase/14-relnote-issues.html#NewFeature |
| 15   | https://www.oracle.com/java/technologies/javase/15-relnote-issues.html#NewFeature |
| 16   | https://www.oracle.com/java/technologies/javase/16-relnotes.html#NewFeature （这个地址好像有点bug，没跳到新特性介绍） |
| 16   | https://www.oracle.com/java/technologies/javase/16all-relnotes.html （16 版本含有全部子版本 release note 的地址，里面才有新特性介绍） |
| 17   | https://www.oracle.com/java/technologies/javase/17-relnote-issues.html#NewFeature |

## OpenJDK

关于 OpenJDK  各个版本（JDK 6 以后）支持的 JEP（Java 增强提案），可以在 OpenJDK 网站上查到（版本 6 ~ 9 查看具体 JEP 特性需要在地址后面加features， 例如：http://openjdk.java.net/projects/jdk8/features/，或者在页面上点那个 features 链接）。

而 JEP 的索引（实现的版本与 JEP 编号的对应）可以在这个地址看到：http://openjdk.java.net/jeps/0

| 版本    | 文档链接                               |
| ------- | -------------------------------------- |
| 6       | http://openjdk.java.net/projects/jdk6/ |
| 7       | http://openjdk.java.net/projects/jdk7/ |
| 8       | http://openjdk.java.net/projects/jdk8/ |
| 9       | http://openjdk.java.net/projects/jdk9/ |
| 10 ~ 19 | http://openjdk.java.net/projects/jdk/  |

