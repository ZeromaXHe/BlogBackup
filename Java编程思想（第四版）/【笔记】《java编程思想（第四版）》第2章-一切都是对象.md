# 第2章 一切都是对象

尽管Java是基于C++的，但是相比之下，Java是一种更“纯粹”的面向对象程序设计语言。

C++和Java都是混合/杂合型语言。但是Java的设计者认为这种杂合性并不像C++中那么重要。杂合型语言允许多种编程风格。

## 2.1 用引用操纵对象

每种编程语言都有自己的操纵内存中元素的方式。有时候，程序员必须注意将要处理的数据是什么类型。你是直接操纵元素，还是用某种基于特殊语法的间接表示（例如C/C++里的指针）来操纵对象？

所有这一切在Java里都得到了简化。一切都被视为对象，因此可采用单一固定的语法。尽管一切都看做对象，但操纵的标识符实际上是对象的一个“引用”（reference）（这可能会引起争论。有人认为：“很明显，它是指针”但是这种说法是基于底层实现的某种假设。并且，Java中的引用，在语法上更接近C++的引用而不是指针。）。

## 2.2 必须由你创建所有对象

### 2.2.1 存储到什么地方

有五个不同的地方可以存储数据：

1）寄存器

2）堆栈。某些java数据存储于堆栈中——特别是对象引用，但是Java对象并不存储于其中。

3）堆

4）常量存储

5）非RAM存储。如果数据完全存活于程序之外，那么它可以不受程序任何控制，在程序没有运行时也可以存在。其中两个基本的例子是**流对象**和**持久化对象**。在流对象中，对象转化成字节流，通常被发送给另一台机器。在“持久化对象”中，对象被存放于磁盘上。Java提供了对轻量级持久化的支持，而诸如JDBC和Hibernate这样的机制提供了更加复杂的对在数据库中存储和读取对象信息的支持。

### 2.2.2 特例：基本类型

new将对象存储在“堆”里，故用new创建一个对象——特别是小的、简单的变量，往往不是很有效。因此，对于这些类型，Java采取与C和C++相同的方法。也就是说，不用new来创建变量，而是创建一个并非是引用的“自动”变量。这个变量直接存储“值”，并置于堆栈中，因此更加高效。

| 基本类型 | 大小 |  最小值   |     最大值     | 包装器类型 |
| :------: | :--: | :-------: | :------------: | :--------: |
| boolean  |  -   |     -     |       -        |  Boolean   |
|   char   |  16  | Unicode 0 | Unicode 2^16-1 | Character  |
|   byte   |  8   |   -128    |      127       |    Byte    |
|  short   |  16  |   -2^15   |     2^15-1     |   Short    |
|   int    |  32  |   -2^31   |     2^31-1     |  Integer   |
|   long   |  64  |   -2^63   |     2^63-1     |    Long    |
|  float   |  32  |  IEEE754  |    IEEE754     |   Float    |
|  double  |  64  |  IEEE754  |    IEEE754     |   Double   |
|   void   |  -   |     -     |       -        |    Void    |

所有数值类型都有正负号，所以不要去寻找无符号的数值类型。

基本类型具有的包装器类，使得可以在堆中创建一个非基本对象，用来表示对应的基本类型。

Java SE5的自动包装功能能将自动地将基本类型转换为包装器类型，并可以反向转换。

**高精度数字**

Java提供了两个用于高精度计算的类：BigInteger和BigDecimal。虽然它们大体上属于“包装器类”的范畴，但两者都没有对应的基本类型。

不过，这两个类包含的方法，提供的操作与对基本类型所能执行的操作相似。也就是说，能作用于int或float的操作，也同样能作用于BigInteger或BigDecimal。只不过必须以方法调用方式取代运算符方式来实现。由于这么做复杂了许多，所以运算速度会比较慢。（速度换取精度）

BigInteger支持任意精度的整数。

BigDecimal支持任何精度的定点数，例如，可以用它进行精确的货币计算。

### 2.2.3 Java中的数组

C和C++中使用数组是很危险的，因为C和C++中的数组就是内存块。程序要访问其自身内存块之外的数组，或在数组初始化前使用内存，都会产生难以预料的后果。

Java的主要目标之一是安全性，所以许多在C和C++里困扰程序员的问题在Java里不会再出现。Java确保数组会被初始化，而且不能在它的范围之外被访问。这种范围检查，是以每个数组上少量内存开销及运行时的下标检查为代价的。但由此换来的是安全性和效率的提高，因此付出的代价是值得的（并且Java有时可以优化这些操作）。

当创建一个数组对象时，实际上就是创建了一个引用数组，并且每个引用都会自动被初始化为一个特定值，该值拥有自己的关键字**null**。一旦Java看到null，就知道这个引用还没有指向某个对象。在使用任何引用前，必须为其制定一个对象；如果识图使用一个还是null的引用，在运行时将会报错。因此，常犯的数组错误在Java中就可以避免。

还可以创建用来存放基本数据类型的数组。同样，编译器也能确保这种数组的初始化，因为它会将这种数组所占的内存全部置零。

## 2.3 永远不需要销毁对象

### 2.3.1 作用域

大多数过程型语言都有作用域（scope）的概念。作用域决定了在其内定义的变量名的可见性和生命周期。在C、C++和Java中，作用域由花括号的位置决定。

在作用域里定义的变量只可用于作用域结束之前。

缩排格式使Java代码更易于阅读。由于Java是一种自由格式（free-form）的语言，所以，空格、制表符、换行都不会影响程序的执行结果。

尽管以下代码在C和C++中是合法的，但是在Java中却不能这样书写：

```java
{
    int x=12;
    {
        int x=96;//Illegal
    }
}
```

编译器将会报告变量x已经定义过。所以，在C和C++里将一个较大作用域的变量“隐藏”起来的做法，在Java里是不允许的。因为Java设计者认为这样做会导致程序混乱。

### 2.3.2 对象的作用域

Java对象不具备和基本类型一样的生命周期。当用new创建一个Java对象时，它可以存活于作用域之外。所以加入你采用代码

```java
{
    String s = new String("a string");
}//End of scope
```

引用s在作用域终点就消失了。然而，s指向的String对象仍继续占据内存空间。在这一小段代码中，我们无法在这个作用域之后访问这个对象，因为对它唯一的引用已超出了作用域的范围。在后续章节中，读者将会看到：在程序执行过程中，怎样传递和复制对象引用。

事实证明，由new创建的对象，只要你需要，就会一直保留下去。这样，许多C++编程问题在Java中就完全消失了。在C++中，你不仅必须要确保对象的保留时间与你需要这些对象的时间一样长，而且还必须在你使用完它们之后，将其销毁。

如果Java让对象继续存在，那么靠什么才能防止这些对象占满内存空间，进而阻塞你的程序呢？这正是C++里可能会发生的问题。这也是Java神奇之所在。Java有个垃圾回收器，用来监视用new创建的所有对象，并辨别那些不会再被引用的对象。随后，释放这些对象的内存空间，以便供其他新的对象使用。也就是说，你根本不必担心内存回收的问题。你只需要创建对象，一旦不再需要，它们就会自行消失。

## 2.4 创建新的数据类型：类

### 2.4.1 字段和方法

一旦定义了一个类（在Java中你所作的全部工作就是定义类，产生那些类的对象，乙级发送消息给这些对象），就可以在类中设置两种类型的元素：**字段**（有时被称作**数据成员**）和**方法**（有时被称作**成员函数**）。字段可以是任何类型的对象，可以通过其引用与其进行通信；也可以是基本类型中的一种。如果字段是对某个对象的引用，那么必须初始化该引用，以便使其与一个实际的对象（如前所述，使用new来实现）相关联。

每个对象都有用来存储其字段的空间；普通字段不能在对象间共享。

**基本成员默认值**

若类的某个成员是基本数据类型，即使没有进行初始化，Java也会确保它获得一个默认值，如表所示：

| 基本类型 |     默认值     |
| :------: | :------------: |
| boolean  |     false      |
|   char   | '\u0000'(null) |
|   byte   |    (byte)0     |
|  short   |    (short)0    |
|   int    |       0        |
|   long   |       0L       |
|  float   |      0.0f      |
|  double  |      0.0d      |

当变量作为类的成员使用时，Java才确保给定其默认值，以确保那些是基本类型的成员变量得到初始化（C++没有此功能），防止产生程序错误。但是这些初试值对于你的程序来说，可能是不正确的，甚至是不合法的。所以最好明确地对变量进行初始化。

然而上述确保初始化的方法并不适用于“局部”变量（即非某个类的字段）。因此，如果在某个方法定义中有

```java
int x;
```

那么变量x得到的可能是任意值（与C和C++中一样），而不会被自动初始化为零。所以在使用x前，应先对其赋一个适当的值。如果忘记了这么做，Java会在编译时返回一个错误，告诉你此变量没有初始化，这正是Java优于C++的地方。（许多C++编译器会对未初始化变量给予警告，而Java则视为是错误）。

## 2.5 方法、参数和返回值

许多程序设计语言（像C和C++）用**函数**这个术语来描述命名子程序；而在Java里却常用**方法**这个术语来表示“做某些事情的方式”。实际上，继续把它看作是函数也无妨。尽管这只是用词上的差别，但本书将沿用Java的惯用法，即用术语“方法”而不是“函数”来描述。

Java的方法决定了一个对象能够接收什么样的消息。方法的基本组成部分包括：名称、参数、返回值和方法体。

返回类型描述的是在调用方法之后从方法返回的值。参数列表给出了要传给方法的信息的类型和名称。方法名和参数列表（它们合起来被称为“**方法签名**”）唯一地标识出某个方法。

Java中的方法只能作为类的一部分来创建。方法只有通过对象才能被调用（static它是针对类调用的，并不依赖于对象的存在），且这个对象必须能执行这个方法调用。

### 2.5.1 参数列表

因此，在参数列表中必须指定每个所传递对象的类型及名字。像Java中任何传递对象的场合一样，这里传递的实际上也是引用（对于前面所提到的特殊数据类型boolean，char，byte，short，int，long，float和double来说是个例外。通常，尽管传递的是对象，而实际上传递的是对象的引用。），并且引用的类型必须正确。

String s的length()方法被调用，它是String类提供的方法之一，会返回字符串包含的字符数。

return关键字

void关键字

## 2.6 构建一个Java程序

### 2.6.1  名字可见性

程序的某个模块里使用了一个名字，而其他人在这个程序的另一个模块里也使用了相同的名字，那么怎样才能区分这两个名字并防止两者互相冲突呢？这个问题在C语言中尤其严重，因为程序往往包含许多难以管理的名字。C++类（Java类基于此）将函数包于其内，从而避免了与其他类中的函数名相冲突。然而，C++仍允许全局数据和全局函数的存在，所以还是有可能发生冲突。为了解决这个问题，C++通过几个关键字引入了**名字空间**的概念。

Java采用了一种全新的方法来避免上述所有问题。为了给一个类库生成不会与其他名字混淆的名字，Java设计者希望程序员反过来使用自己的Internet域名，因为这样可以保证它们肯定是独一无二的。比如我的域名是MindView.net，所以我的各种奇奇怪怪的应用工具类库就被命名为net.mindview.utility.foibles。反转域名后，句点就用来代表子目录的划分。

这种机制意味着所有的文件能够自动存活于它们自己的名字空间内，而且同一个文件内的每个类都有唯一的标识符——Java语言本身已经解决了这个问题。

### 2.6.2 运用其他构件

如果想在自己的程序里使用预先定义好的类，那么编译器就必须知道怎么定位它们。当然，这个类可能就在发出调用的那个源文件中；在这种情况下，就可以直接使用这个类——即使这个类在文件的后面才会被定义（Java消除了所谓的“向前引用”问题）。

如果那个类位于其他文件中，又会怎样呢？为了解决这个问题，必须消除所有可能的混淆情况。为实现这个目的，可以使用关键字import来准确地告诉编译器你想要的类是什么。import指示编译器导入一个包，也就是一个类库（在其他语言中，一个库不仅包含类，还可能包括方法和数据；但是Java中所有的代码都必须写在类里）。

如果不想明确地逐一声明，那么你很容易使用通配符“*”来达到这个目的：

```java
import java.util.*
```

### 2.6.3 static关键字

有两种情形四new无法解决的：一种情形是，只想为某特定域分配单一存储空间，而不去考虑究竟要创建多少对象，甚至根本就不创建任何对象。另一种情形是，希望某个方法不与包含它的类的任何对象关联在一起。也就是说，即使没有创建对象，也能够调用这个方法。

通过static关键字可以满足这两方面的需要。当声明一个事物是static时，就意味着这个域或方法不会与包含它的那个类的任何对象实例关联在一起。所以，即使从未创建某个类的任何对象，也可以调用其static方法或访问其static域。通常，你必须创建一个对象，并用它来访问数据或方法。因为非static域和方法必须知道它们一起运作的特定对象。

有些面向对象语言采用**类数据**和**类方法**两个术语，代表那些数据和方法只是作为整个类，而不是类的某个特定对象而存在的。有时，一些Java文献里也用到这两个术语。

```java
class StaticTest{
    static int i = 47;
}
```

现在即使你创建了两个StaticTest对象，StaticTest.i也只有一份存储空间，这两个对象共享同一个i。

```java
StaticTest st1 = new StaticTest();
StaticTest st2 = new StaticTest();
```

在这里，st1.i和st2.i指向同一存储空间，因此它们具有相同的值。

引用static变量有两种方法。如前例所示，可以通过一个对象去定位它，如st2.i；也可以通过其类名直接引用，而这对于非静态成员则不行。

```java
StaticTest.i++;
```

此时，st1.i和st2.i仍具有相同的值48.

类似逻辑也应用于静态方法。既可以像其他方法一样，通过一个对象来引用某个静态方法，也可以通过特殊的语法形式ClassName.method()加以引用。定义静态方法的方式也与定义静态变量的方式相似：

```java
class Incrementable{
    static void increment(){
        StaticTest.i++;
    }
}
```

可以看到，Incrementable的increment()方法通过++运算符将静态数据 i 递加。可以采用典型的方式，通过对象来调用increment();

```java
Incrementable sf = new Incrementable();
sf.increment();
```

或者，因为increment()是一个静态方法，所以也可以通过它的类直接调用：

Incrementable.increment();

尽管当static作用于某个字段时，肯定会改变数据创建的方式（因为一个static字段对每个类来说都只有一份存储空间，而非static字段则是对每个对象有一个存储空间），但是static作用于某个方法，差别却没有那么大。static方法的一个重要用法就是在不创建任何对象的前提下就可以调用它。正如我们将会看到的那样，这一点对定义main()方法很重要，这个方法是运行一个应用时的入口点。

和其他任何方法一样，static方法可以创建或使用与其类型相同的被命名对象，因此，static方法常常拿来做“牧羊人”的角色，负责看护与其隶属同一类型的实例群。

## 2.7 你的第一个Java程序

```java
//HelloDate.java
import java.util.*;

public class HelloDate{
    public static void main(String[] args){
        System.out.println("Hello, it's: ");
        System.out.println(new Date());
    }
}
```

在每个程序文件的开头，必须声明import语句，以便引入在文件代码中需要用到的额外类。注意，在这里说它们“额外”，是因为有一个特定类会自动被导入到每一个Java文件中：java.lang。

system类有许多属性，其中out是一个静态PrintStream对象。因为是静态的，所以不需要创建任何东西，out对象便已经存在了，只须直接使用即可。

public关键字意指这是一个可由外部调用的方法。main()方法的参数是一个String对象的数组。在这个程序中并未用到args，但是Java编译器要求必须这样做，因为args要用来存储命令行参数。

### 2.7.1 编译和运行

## 2.8 注释和嵌入式文档

Java里有两种注释风格。一种是传统的C语言风格的注释——C++也继承了这种风格。此种注释以` /*`开始，随后是注释内容，并可跨越多行，最后以` */`结束。注意，许多程序员在连续的注释内容的每一行都以一个“*”开头。

第二种风格的注释也源于C++。这种注释是“单行注释”，以一个“//”起头，直到句末。这种风格的注释因为书写容易，所以更方便、更常用。

### 2.8.1 注释文档

javadoc便是用于提取注释的工具，它是JDK安装的一部分。它采用了Java编译器的某些技术，查找程序内的特殊注释标签。

javadoc输出的是一个HTML文件，可以用Web浏览器查看。

### 2.8.2 语法

所有的javadoc命令都只能在` “/**” `注释中出现，和通常一样，注释结束于` "*/" `。使用javadoc的方式主要有两种：嵌入HTML，或使用"文档标签"。**独立文档标签**是一些以"@"字符开头的命令，且要置于注释行的最前面（但是不算前导` "*" `之后的最前面）。而**"行内文档标签"**则可以出现在javadoc注释中的任何地方，它们也是以"@"字符开头，但要括在花括号内。

javadoc只能为public（公共）和protected（受保护）成员进行文档注释。private（私有）和包内可访问成员的注释会被忽略掉，所以输出结果中看不到它们（不过可以用-private进行标记，以便把private成员的注释也包括在内）。这样做是有道理的，因为只有public和protected成员才能在文件之外被使用，这是客户端程序员所期望的。

上述代码的输出结果是一个HTML文件，它与其他JAVA文档具有相同的标准格式。

### 2.8.3 嵌入式HTML

```java
//:object/Documentation2.java
/**
* <pre>
* System.out.println(new Date());
* </pre>
* / 
///:~
```

也可以像在其他Web文档中那样运用HTML，对普通文本按照你自己所描述的进行格式化：

```java
//:object/Documentation3.java
/**
* You can <em>even<em> insert a list:
*<ol>
*<li>Item one
*<li>Item two
*<li>Item three
*</ol>
*/
///:~
```

注意，在文档注释中，位于每一行开头的星号和前导空格都会被javadoc丢弃。javadoc会对所有内容重新格式化，使其与标准的文档外观一致。不要在嵌入式HTML中使用标题标签，例如` <hl> `或` <hr> `，因为javadoc会插入自己的标题，而你的标题可能同它们发生冲突。

所有类型的注释文档——类、域和方法——都支持嵌入式HTML。

### 2.8.4 一些标签示例

1.@see：引用其他类

@see标签允许用户引用其他类的文档。javadoc会在其生成的HTML文件中，通过@see标签链接到其他文档。格式如下：

```
@see classname
@see fully-qualified-classname
@see fully-qualified-classname#method-name
```

每种格式都会在生成的文档中加入一个具有超链接的"See Also"（参见）条目。但是javadoc不会检查你所提供的超链接是否有效。

2.｛@link package.class#member label｝

该标签与@see极其相似，只是它用于行内，并且是用"label"作为超链接文本而不用"See Also"。

3.｛@docRoot｝

该标签产生到文档根目录的相对路径，用于文档树页面的显式超链接。

4.{@inheritDoc}

该标签从当前这个类的最直接的基类中继承相关文档到当前的文档注释中。

5.@version

该标签的格式如下：

```
@version version-information
```

其中，"version-information"可以是任何你认为适合包含在版本说明中的重要信息。如果javadoc命令行使用了"-version"标记，那么就从生成的HTML文档中特别提取出版本信息。

6.@author

该标签的格式如下：

```
@author author-information
```

其中，author-information一看便知是你的姓名，但是也可以包括电子邮件地址或者其他任何适宜的信息。如果javadoc命令行使用了-author标记，那么就从生成的HTML文档中特别提取作者信息。

可以使用多个标签，以便列出所有作者，但是它们必须连续放置。全部作者信息会合并到同一段落，置于生成的HTML中。

7.@since

该标签允许你指定程序代码最早使用的版本，可以在HTML Java文档中看到它被用来指定所用的JDK版本的情况。

8.@param

该标签用于方法文档中，形式如下：

```
@param parameter-name description
```

其中，parameter-name是方法的参数列表中的标签符，description是可延续数行的文本，终止于新的文档标签出现之前。可以使用任意多个这种标签，大约每个参数都有一个这样的标签。

9.@return

该标签用于方法文档，格式如下：

```
@return description
```

其中，description用来描述返回值的含义，可以延续数行。

10.@throws

"异常"将在第9章论述。简言之，它们是由于某个方法调用失败而"抛出"的对象。尽管在调用一个方法时，只出现一个异常对象，但是某个特殊方法可能会产生任意多个不同类型的异常，所有这些异常都需要进行说明。所以，异常标签的格式如下：

```
@throws fully-qualified-class-name description
```

其中，fully-qualified-class-name给出一个异常类的无歧义的名字，而该异常类在别处定义。description（同样可以延续数行）告诉你为什么此特殊类型的异常会在方法调用中出现。

11.@deprecated

该标签用于指出一些旧特性已由改进的新特性所取代，建议用户不要再使用这些旧特性，因为在不久的将来它们很可能会被删除。如果使用一个标记为@deprecated的方法，则会引起编译器发布警告。

在Java SE5中，Javadoc标签@deprecated已经被@Deprecated注解所替代

### 2.8.5 文档示例

```java
//:object/HelloDate.java
import java.util.*;
/** The first Thinking in Java example program.
* Displays a string and today's date.
* @author Bruce Eckel
* @author www.MindView.net
* @version 4.0
*/
public class HelloDate{
    /** Entry point to class & application.
    * @param args array of string arguments
    * @throws exceptions No exceptions thrown
    */
    public static void main(String[] args){
        System.out.println("Hello, it's: ");
        System.out.println(new Date());
    }
}/* Output:(55% match)
Hello, it's:
Wed Oct 05 14:39:36 MDT 2005
*///:~
```

第一行采用我自己独特的方法，用一个"："作为特殊记号说明这是包含源文件的注释行。该行包含文件的路径信息（此时，object代表本章），随后是文件名。最后一行也是一行注释，这个“///:~”标志源代码清单的结束。自此，在通过编译器和执行检查后，文档就可以自动更新成本书的文本。

/*Output 标签表示输出的开始部分将由这个文件生成，通过这种形式，它会被自动地测试以验证其准确性。在本例中，（55% match）在向测试系统说明程序的每一次运行和下一次运行的输出存在着很大的差异，因此它们与这里列出的输出预期只有55%的相关性。

## 2.9 编码风格

驼峰风格。类名的首字母要大写；如果类名由几个单词构成，那么把它们并在一起（也就是说，不要用下划线来分隔名字），其中每个内部单词的首字母都采用大写形式。

几乎其他所有内容——方法、字段（成员变量）以及对象引用名称等，公认的风格与类方格一样，只是标识符的第一个字母采用小写。

