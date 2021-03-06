# 第12章 通过异常处理错误

## 12.1 概念

C++的异常处理机制基于Ada，Java的异常处理则建立在C++的基础之上

## 12.2 基本异常

**异常情形（exceptional condition）**是指阻止当前方法或作用域继续执行的问题。

**异常处理程序**，它的任务是将程序从错误状态中恢复，以使程序能要么换一种方式运行，要么继续运行下去。

### 12.2.1 异常参数

所有标准异常类都有两个构造器：一个默认构造器；另一个是接受字符串作为参数，以便把相关信息放入异常对象的构造器：

```java
throw new NullPointerException("t=null");
```

Throwable对象，异常类型的根类。

## 12.3 捕获异常

**监控区域（guarded region）**

### 12.3.1 try块

try关键字

### 12.3.2 异常处理程序

catch关键字

标识符不可以省略

try块内部，许多不同的方法调用可能会产生类型相同的异常，而你只需要提供一个针对此类型的异常处理程序。

### 终止与恢复

**终止模型**：错误无法挽回，也不能回来继续运行。

**恢复模型**：异常处理程序的工作是修正错误

恢复模型不是很实用，主要原因可能是它所导致的耦合。

## 12.4 创建自定义异常

要定义异常类，必须从已有的异常类继承，最好是选择意思相近的异常类继承。

System.err将错误发送给标准错误流。System.out也许会被重定向。如果把结果送到System.err，它就不会随System.out一起被重定向，这样更容易被用户注意。

Throwable类的printStackTrace()方法，默认输出到标准错误流。

### 12.4.1 异常与记录日志

静态的Logger.getLogger()方法	severe()

Throwable.getMessage()方法

## 12.5 异常说明

throws关键字

被检查的异常

## 12.6 捕获所有异常

Exception是与编程有关的所有异常类的基类，可以调用它从基类Throwable继承的方法：

String getMessage()

String getLocalizedMessage()

用来获取详细信息，或用本地语言表示的详细信息。

String toString()

返回对Throwable的简单描述，要是有详细信息的话，也会把它包含在内。

void printStackTrace()

void printStackTrace(PrintStream)

void printStackTrace(java.io.PrintWriter)

打印Throwable和Throwable的调用栈轨迹。第一个版本输出到标准错误，后两个版本允许选择要输出的流。

Throwable fillInStackTrace()

用于在Throwable对象的内部记录栈帧的当前状态。

此外，也可以使用Throwable从其基类Object继承的方法。	getClass()	getName()	getSimpleName()

### 12.6.1 栈轨迹

元素0是栈顶元素	数组中最后一个元素和栈底是调用序列中的第一个方法调用

stackTraceElement

### 12.6.2 重新抛出异常

重抛异常会把异常抛给上一级环境中的异常处理程序，同一个try块的后续catch子句将会被忽略。

如果只是把当前异常重新抛出，那么printStackTrace()方法显示的将是原来异常抛出点的调用栈信息，而并非重新抛出点的信息。	fillInStackTrace()方法，返回一个把当前调用栈信息填入原来异常对象而建立的Throwable对象

### 12.6.3 异常链

JDK1.4之后，Throwable的子类在构造器中都可以接受一个cause（因由）对象作为参数。

有趣的是，在Throwable子类中，只有三个基本的异常类提供了带cause参数的构造器。它们是Error、Exception以及RuntimeException。如果要把其他类型的异常连接起来，应该使用InitCause()方法而不是构造器。

## 12.7 Java标准异常

Throwable这个Java类被用来表示任何可以作为异常被抛出的类。Throwable对象可分为两种类型（指从Throwable继承而得到的类型）：Error用来表示编译时和系统错误，Exception是可以被抛出的基本类型。

异常并非全是在java.lang包里定义的。

### 12.7.1 特例：RuntimeException

属于运行时异常的类型有很多，它们自动被Java虚拟机抛出，所以不必在异常说明中把它们列出来。	不受检查的异常，这种异常属于错误，将被自动捕获。

如果RuntimeException没有被捕获而直达main()，那么在程序退出前将调用异常的printStackTrace()方法。

不应把Java的异常处理机制当成是用途单一用途的工具。它被设计用来处理一些烦人的运行时错误，这些错误往往是由代码能力之外的因素导致的，然而，它对于发现某些编译器无法检测到的编译错误也是非常重要的。

## 12.8 使用finally进行清理

对于一些代码，可能希望无论try块中的异常是否抛出，它们都能得到执行。可以在异常处理程序后面加上finally子句。

### 12.8.1 finally用来作什么

要把除内存之外的资源恢复到它们初始状态时，就要用到finally子句。

如果把finally子句和标签的break和continue配合使用，在Java里就没必要使用goto语句了。

### 12.8.2 在return中使用finally

在finally类内部，从何处返回无关紧要

### 12.8.3 缺憾：异常丢失

用某些特殊方式使用finally子句，就可能会导致异常被忽略。

相比之下，C++把"前一个异常还没处理就抛出下一个异常"的情形看成是糟糕的编程错误。

一种更简单的丢失异常的方法是从finally子句中返回。

## 12.9 异常的限制

当覆盖方法时，只能抛出基类方法的异常说明里列出的那些异常。

异常限制对构造器不起作用。然而因为基类构造器必须以这样或那样的方式被调用，派生类构造器的异常说明必须包含基类构造器的异常说明。

派生类的构造器不能捕获基类构造器抛出的异常。

通过强制派生类遵守基类方法的异常说明，对象的可替换性得到了保证。

尽管在继承过程中，编译器会对异常说明做强制要求，但异常说明本身并不属于方法类型的一部分，方法类型是由方法的名字与参数的类型组成的。因此，不能基于异常说明来重载方法。	换句话说，在继承和覆盖的过程中，某个特定方法的“异常说明接口”不是变大了而是变小了——这恰恰和类接口在继承时的情形相反。

## 12.10 构造器

对于在构造阶段可能抛出异常，并且需要清理的类，最安全的使用方式是使用嵌套的try子句

这种通用的清理惯用法在构造器不抛出任何异常时也应该运用，其基本规则是：在创建需要清理的对象之后，立即进入try-finally语句块。

## 12.11 异常匹配

异常处理系统会按照代码的书写顺序找出“最近”的处理程序。找到匹配的处理程序之后，它就认为异常将得到处理，然后就不再寻找。

派生类对象也可以匹配其基类的处理程序。

## 12.12 其他可选方式

异常处理的一个重要目标就是把错误处理的代码同错误发生的地点相分离。

**吞食则有害（harmful if swallowed）**

我们使用的工具已经不是ANSI标准出台前的像C那样弱类型的语言，而是C++和Java这样的“强静态类型语言”（也就是编译时就做类型检查的语言）

### 12.12.1 历史

异常处理起源于PL/1和Mesa之类的系统中，后来又出现在CLU、Smalltalk、Modula-3、Ada、Eiffel、C++、Python、Java以及后Java语言Ruby和C#中。

C++异常模型主要借鉴了CLU的做法。然而，当时其他语言已经支持异常处理了：包括Ada、Smalltalk（两者都有异常处理，但是都没有异常说明），以及Modula-3（它既有异常处理也有异常说明）。

### 12.12.2 观点

首先，Java无谓地发明了“被检查的异常”，没有别的语言采用这种做法

其次，程序变大的时候，“被检查的异常”会带来一些微妙的问题。	过多的类型检查

### 12.12.3 把异常传递给控制台

### 12.12.4 把“被检查的异常”转换为“不检查的异常”

JDK1.4的异常链提供了一种新的思路来解决这个问题，可以直接把“被检查的异常”包装进RuntimeException里面。

这种技巧给了你一种选择，可以不写try-catch子句和/或异常说明，直接忽略异常，让它自己沿着调用栈往上“冒泡”。同时还可以用getCause()捕获和处理特定异常。

## 12.13 异常使用指南

在下列情况中使用异常：

1. 在恰当的级别处理问题（在知道该如何处理的情况下才捕获异常）
2. 解决问题并且重新调用产生异常的方法
3. 进行少许修补，然后绕过异常发生的地方继续执行
4. 用别的数据进行计算，以代替方法预计会返回的值
5. 把当前运行环境下能做的事情尽量做完，然后把相同的异常重抛到更高层
6. 把当前运行环境下能做的事情尽量做完，然后把不同的异常抛到更高层
7. 终止程序
8. 进行简化（如果你的异常模式使问题变得太复杂，那用起来会非常痛苦也很烦人）
9. 让类库和程序更安全（这既是在为调试做短期投资，也是在为程序的健壮性做长期投资）

