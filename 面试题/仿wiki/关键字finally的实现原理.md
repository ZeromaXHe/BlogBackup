# 导航表

| key          | value                |
| ------------ | -------------------- |
| **题目**     | finally 的实现原理？ |
| **同义题目** | （暂无）             |
| **主题**     | Java                 |
| **上推问题** | （暂无）             |
| **平行问题** | （暂无）             |
| **下切问题** | （暂无）             |

关于 Java 虚拟机是如何编译 finally 语句块的问题，有兴趣的读者可以参考《The Java(TM) Virtual Machine Specification, Second Edition》中 7.13 节 Compiling finally。那里详细介绍了 Java 虚拟机是如何编译 finally 语句块。

实际上，Java 虚拟机会把 finally 语句块作为 subroutine 直接插入到 try 语句块或者 catch 语句块的控制转移语句之前。但是，还有另外一个不可忽视的因素，那就是在执行 subroutine（也就是 finally 语句块）之前，try 或者 catch 语句块会保留其返回值到本地变量表（Local Variable Table）中。待 subroutine 执行完毕之后，再恢复保留的返回值到操作数栈中，然后通过 return 或者 throw 语句将其返回给该方法的调用者（invoker）。

# 1. try catch 的机制：异常表

Java 中的 finally 是不可以单独使用的，只能结合 try 使用，所以有必要先弄明白 try catch 的原理。

源代码如下

```java
public static void tryCatch() {
    try {
        test();
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

使用命令 `javap -v -p Test.class` 反编译得到方法的字节码

```java
public static void tryCatch();
	descriptor: ()V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
    	stack=1, locals=1, args_size=0
    		0: invokestatic #5 // Method test:()V
    		3: goto 11
    		6: astore_0
    		7: aload_0
    		8: invokevirtual #7 // Method java/lang/Exception.printStackTrace:()V
    		11: return
    	Exception table:
    		from	to	target	type
    		0		3	6		Class java/lang/Exception
```

从字节码可以看出，方法 tryCatch 有一个**异常表**（Exception table），异常表解释为，从位置 from 的指令开始，此处 from = 0，表示 Code 中偏移量为 0 的指令，也就是 `invokestatic #5` 这个指令，到位置 to（不包括 to）的指令为止，这段指令执行期间如果发生异常且异常的类型 type 为 Class java.lang.Exception，程序就跳转到 target 处指令，这里 target = 6 处指令是 `astore_0`。异常表的一行数据，表示一个 catch 块，这里只有一个行，也就是只有一个 catch 块。

下面逐行解释一下该方法的指令：

- 偏移量 0 `invokestatic #5`，该指令则是调用静态方法 `test()`。
- 偏移量 3 `goto 11`，如果上一条指令发生异常，就执行该条指令，直接跳转到偏移量11的指令。
- 偏移量 6 `astore_0`，如果第一条指令发⽣异常，根据异常表，程序就会跳转到这条指令，6 ~ 8 三条指令就是 catch 块代码对应的指令了，astore_0 指令是将第⼀条指令的异常对象存储到局部变量表 slot = 1 的位置。
- 偏移量 7 `aload_0`，将局部变量表 slot = 1 的位置异常对象压入操作数栈。
- 偏移量 8 `invokevirtual #7`，调⽤ `java.lang.Excetion.printStackTrace()` 方法，调⽤对象就是上⼀个指令压入操作数栈的异常对象。
- 偏移量 9 `return`，不管是从 goto 11 这条指令跳转到这的，还是发生了异常，执行了 catch 块代码后到这的，方法最后需要执行的都是 return 指令，方法结束。

# 2. finally 机制：复制 finally 代码块到每个分支



# 参考文档

- [完全解析Java编程中finally语句的执行原理](https://max.book118.com/html/2021/0602/5130043204003234.shtm)
- [字节码层面理解--java中的finally是如何执行的](https://wenku.baidu.com/view/5246e922eb7101f69e3143323968011ca300f778.html)
