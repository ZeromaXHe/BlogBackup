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
> 不过，Spring 和 Kafka 的新版本文档截至目前（2022.2.21）还没更新过来，里面还有支持 Java 8 的语句没修改……
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

# 具体各个版本的新特性

## JDK 17



# 一些原始资料的链接

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
| 16   | https://www.oracle.com/java/technologies/javase/16-relnotes.html#NewFeature （这个地址好像有点问题，没找到新特性介绍） |
| 17   | https://www.oracle.com/java/technologies/javase/17-relnote-issues.html#NewFeature |

关于 OpenJDK  各个版本（JDK 6 以后）支持的 JEP（Java 增强提案），可以在 OpenJDK 网站上查到（版本 6 ~ 9 查看具体 JEP 特性需要在地址后面加features， 例如：http://openjdk.java.net/projects/jdk8/features/，或者在页面上点那个 features 链接）。

而 JEP 的索引（实现的版本与 JEP 编号的对应）可以在这个地址看到：http://openjdk.java.net/jeps/0

| 版本    | 文档链接                               |
| ------- | -------------------------------------- |
| 6       | http://openjdk.java.net/projects/jdk6/ |
| 7       | http://openjdk.java.net/projects/jdk7/ |
| 8       | http://openjdk.java.net/projects/jdk8/ |
| 9       | http://openjdk.java.net/projects/jdk9/ |
| 10 ~ 19 | http://openjdk.java.net/projects/jdk/  |

