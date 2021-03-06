# 第6章 接口、Lambda表达式与内部类

## 6.1 接口

### 6.1.1 接口概念

Comparable接口：都需要包含compareTo方法

*注释：在Java SE 5.0中，Comparable接口已经改进为泛型类型。*

接口的所有方法自动属于public。因此，在接口中声明方法时不必提供关键字public。

接口绝不能含有实例域，在Java SE 8之前，也不能在接口中实现方法。（6.1.4节和6.1.5节可以看到，现在已经可以在接口中提供简单方法了。当然，这些方法不能引用实例域）

提供实例域和方法实现的任务应该由实现接口的那个类来完成。

为了让类实现一个接口，通常需要下面两个步骤：

1）将类声明为实现给定接口

2）对接口中的所有方法进行定义

关键字implements

Double.compare静态方法，如果第一个参数小于第二个参数，它会返回一个负值；如果二者相等则返回0；否则返回一个正值。

*警告：在实现接口时，必须把方法声明为public；否则编译器将认为这个方法的访问属性是包可见性，即类的默认访问属性，之后编译器就会给出试图提供更严格的访问权限的警告信息。*

*提示：Comparable接口中的compareTo方法将返回一个整型数值。如果两个对象不相等，则返回一个正值或者一个负值。*

主要原因在于 Java 程序设计语言是一种**强类型 （strongly typed）** 语言。在调用方法的时候， 编译器将会检查这个方法是否存在。 

*注释： 有人认为， 将 Arrays 类中的 sort 方法定义为接收一个 Comparable[ ] 数组就可以在使用元素类型没有实现 Comparable 接口的数组作为参数调用 sort 方法时， 由编译器给出错误报告。但事实并非如此。 在这种情况下， sort 方法可以接收一个 Object[ ] 数组， 并对其进行笨拙的类型转换*

【API】java.lang.Comparable\<T> 1.0 :

- `int compareTo(T other)` 用这个对象与 other 进行比较。 如果这个对象小于 other 则返回负值； 如果相等则返回0；否则返回正值。 

【API】java.util.Arrays 1.2 :

- `static void sort(Object[] a)` 使用 mergesort 算法对数组 a 中的元素进行排序。要求数组中的元素必须属于实现了Comparable 接口的类， 并且元素之间必须是可比较的 。

【API】java.lang.Integer 1.0 :

- `static int compare(int x, int y)` 7   如果 x < y 返回一个负整数；如果 x 和 y 相等，则返回 0; 否则返回一个负整数。 

【API】java.lang.Double 1.0 :

- `static int compare(double x, double y)` 1.4   如果 x < y 返回一个负数；如果 x 和 y 相等则返回 0; 否则返回一个负数 

*注释：语 言标准规定：对于任意的x和y,实现必须能够保证 sgn(x.compareTo(y)) = -sgn(y.compareTo(x)。) （也就是说， 如果 y.compareTo(x) 抛出一个异常， 那么 x.compareTo(y) 也应该抛出一个异常。）这里的“ sgn” 是一个数值的符号： 如果n是负值， sgn（n） 等于 -1 ; 如果 n 是 0, sgn(n) 等于 0 ; 如果 n 是正值， sgn（n）等于 1 简单地讲， 如果调换compareTo 的参数， 结果的符号也应该调换 （而不是实际值）*

*与 equals 方法一样， 在继承过程中有可能会出现问题。*

*这种情况与第5章中讨论的 equals 方法一样， 修改的方式也一样 ，有两种不同的情况*

### 6.1.2 接口的特性

接口不是类，尤其不能使用 new 运算符实例化一个接口 

然而， 尽管不能构造接口的对象，却能声明接口的变量 

接口变量必须引用实现了接口的类对象

接下来， 如同使用 instanceof 检查一个对象是否属于某个特定类一样， 也可以使用instanceof 检查一个对象是否实现了某个特定的接口 

与可以建立类的继承关系一样，接口也可以被扩展。这里允许存在多条从具有较高通用性的接口到较高专用性的接口的链 。

虽然在接口中不能包含实例域或静态方法，但却可以包含常量。 

与接口中的方法都自动地被设置为 public—样，接口中的域将被自动设为 public static final。 

*注释：可以将接口方法标记为 public, 将域标记为 public static final。有些程序员出于习惯或提高清晰度的考虑， 愿意这样做。但 Java 语言规范却建议不要书写这些多余的关键字， 本书也采纳了这个建议。*

有些接口只定义了常量， 而没有定义方法。例如， 在标准库中有一个 SwingConstants就是这样一个接口， 其中只包含 NORTH、 SOUTH 和 HORIZONTAL 等常量。 任何实现SwingConstants 接口的类都自动地继承了这些常量， 并可以在方法中直接地引用 NORTH,而不必采用 SwingConstants.NORTH 这样的繁琐书写形式。然而，这样应用接口似乎有点偏离了接口概念的初衷， 最好不要这样使用它。 

尽管每个类只能够拥有一个超类， 但却可以实现多个接口。 	如果某个类实现了这个 Cloneable 接口，Object 类中的 clone 方法就可以创建类对象的一个拷贝。 如果希望自己设计的类拥有克隆和比较的能力， 只要实现这两个接口就可以了: 使用逗号将实现的各个接口分隔开。 

### 6.1.3 接口和抽象类

使用抽象类表示通用属性存在这样一个问题： 每个类只能扩展于一个类。 

有些程序设计语言允许一个类有多个超类， 例如 C++。我们将此特性称为**多重继承( multiple inheritance) **。而 Java 的设计者选择了不支持多继承，其主要原因是多继承会让语言本身变得非常复杂（如同 C++，) 效率也会降低 （如同 Eiffel。) 

*C++注释：C++ 具有多重继承特性， 随之带来了一些诸如虚基类、控制规则和横向指针类型转换等复杂特性 ， 很少有 C++ 程序员使用多继承， 甚至有些人说： 就不应该使用多继承。 也有些程序员建议只对“ 混合” 风格的继承使用多继承。 在“ 混合” 风格中， 一个主要的基类描述父对象， 其他的基类 （因此称为混合）扮演辅助的角色这种风格类似于 Java 类中从一个基类派生， 然后实现若干个辅助接口 。*

### 6.1.4 静态方法

在 Java SE 8 中，允许在接口中增加静态方法。理论上讲，没有任何理由认为这是不合法的。 只是这有违于将接口作为抽象规范的初衷。 

目前为止， 通常的做法都是将静态方法放在伴随类中。在标准库中， 你会看到成对出现的接口和实用工具类， 如 Collection/Collections 或 Path/Paths。 

不过整个 Java 库都以这种方式重构也是不太可能的， 但是实现你自己的接口时，不再需要为实用工具方法另外提供一个伴随类。 

### 6.1.5 默认方法

可以为接口方法提供一个默认实现。 必须用 default 修饰符标记这样一个方法。 

当然， 这并没有太大用处， 因为 Comparable 的每一个实际实现都要覆盖这个方法。不过有些情况下， 默认方法可能很有用。 例如， 在第 11 章会看到， 如果希望在发生鼠标点击事件时得到通知， 就要实现一个包含 5 个方法的接口 。大多数情况下， 你只需要关心其中的 1、2 个事件类型。在 Java SE 8 中， 可以把所有方法声明为默认方法， 这些默认方法什么也不做。 

默认方法可以调用任何其他方法。 

*注释：在 JavaAPI 中，你会看到很多接口都有相应的伴随类，这个伴随类中实现了相应接口的部分或所有方法， 如 CoUection/AbstractCollectkm 或 MouseListener/MouseAdapter 。在JavaSE 8 中， 这个技术已经过时。现在可以直接在接口中实现方法。*

默认方法的一个重要用法是“‘ 接口演化” （interface evolution)。  以 Collection 接口为例，这个接口作为 Java 的一部分已经有很多年了。 假设很久以前你提供了这样一个类：

`public class Bag implements Collection`

假设 stream 方法不是一个默认方法。那么 Bag 类将不能编译， 因为它没有实现这个新方法。 后来， 在 JavaSE 8 中， 又为这个接口增加了一个 stream 方法。 为接口增加一个非默认方法不能保证“ 源代码兼容 ”（source compatible）。

不过， 假设不重新编译这个类， 而只是使用原先的一个包含这个类的 JAR 文件。这个类仍能正常加载，尽管没有这个新方法。程序仍然可以正常构造 Bag 实例， 不会有意外发生。( 为接口增加方法可以保证“ 二进制兼容”）。不过， 如果程序在一个 Bag 实例上调用 stream方法，就会出现一个 AbstractMethodError。 

将方法实现为一个默认方法就可以解决这两个问题。 Bag 类又能正常编译了。另外如果没有重新编译而直接加载这个类， 并在一个 Bag 实例上调用 stream 方法， 将调用 Collection.stream 方法。 

### 6.1.6 解决默认方法冲突

如果先在一个接口中将一个方法定义为默认方法， 然后又在超类或另一个接口中定义了同样的方法， 会发生什么情况？ 诸如 Scala 和 C++ 等语言对于解决这种二义性有一些复杂的规则。幸运的是， Java 的相应规则要简单得多。规则如下：

1 ) ==超类优先==。如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略。

2 ) ==接口冲突==。 如果一个超接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型 （不论是否是默认参数）相同的方法， 必须覆盖这个方法来解决冲突。 

下面来看第二个规则。 

不过，Java 设计者更强调一致性。两个接口如何冲突并不重要。 ==如果至少有一个接口提供了一个实现， 编译器就会报告错误， 而程序员就必须解决这个二义性==。 

*注释：当然，如果两个接口都没有为共享方法提供默认实现， 那么就与 Java SE 8之前的情况一样，这里不存在冲突。 实现类可以有两个选择： 实现这个方法， 或者干脆不实现。如果是后一种情况， 这个类本身就是抽象的。*

我们只讨论了两个接口的命名冲突。现在来考虑另一种情况， 一个类扩展了一个超类，同时实现了一个接口，并从超类和接口继承了相同的方法。 

==在这种情况下， 只会考虑超类方法， 接口的所有默认方法都会被忽略==。 	这正是“ 类优先” 规则。 

“ 类优先” 规则可以确保与 Java SE 7 的兼容性。 如果为一个接口增加默认方法，这对于
有这个默认方法之前能正常工作的代码不会有任何影响。 

*警告：千万不要让一个默认方法重新定义 Object 类中的某个方法。 例如， 不能为 toString或 equals 定义默认方法， 尽管对于 List 之类的接口这可能很有吸引力， 由于“ 类优先”规则， 这样的方法绝对无法超越 Object.toString 或 Objects.equals。*

## 6.2 接口示例

### 6.2.1 接口和回调

**回调（ callback)** 是一种常见的程序设计模式。在这种模式中， 可以指出某个特定事件发生时应该采取的动作。  

【API】javax.swing.JOptionPane 1.2 :

- `static void showMessageDialog(Component parent, Object message)` 显示一个包含一条消息和 OK 按钮的对话框。 这个对话框将位于其 parent 组件的中央。如果 parent 为 null , 对话框将显示在屏幕的中央。

【API】javax.swing.Timer 1.2 :

- `Timer(int interval, ActionListener listener)` 构造一个定时器， 每隔 interval 毫秒通告 listener—次 。
- `void start()` 启动定时器。一旦启动成功， 定时器将调用监听器的 actionPerformed。 
- `void stop()` 停止定时器。一旦停止成功， 定时器将不再调用监听器的 actionPerformed。

【API】java.awt.Toolkit 1.0 :

- `static Toolkit getDefaultToolKit()` 获得默认的工具箱。 工具箱包含有关 GUI 环境的信息。 
- `void beep()` 发出一声铃响 。

### 6.2.2 Comparator接口

现在假设我们希望按长度递增的顺序对字符串进行排序，而不是按字典顺序进行排序。肯定不能让 String 类用两种不同的方式实现 compareTo 方法—更何况，String 类也不应由我们来修改。 

要处理这种情况，Arrays.Sort 方法还有第二个版本， 有一个数组和一个比较器 ( comparator ) 作为参数， 比较器是实现了 Comparator 接口的类的实例。 

将这个调用与 words[i].compareTo(words[j]) 做比较。这个 compare 方法要在比较器对象上调用， 而不是在字符串本身上调用。 

*注释：尽管 LengthComparator 对象没有状态， 不过还是需要建立这个对象的一个实例。我们需要这个实例来调用 compare 方法——它不是一个静态方法*

在 6.3 节中我们会了解， 利用 lambda 表达式可以更容易地使用 Comparator。 

### 6.2.3 对象克隆

如果希望 copy 是一个新对象，它的初始状态与 original 相同， 但是之后它们各自会有自己不同的状态， 这种情况下就可以使用 clone 方法。 

不过并没有这么简单。 clone 方法是 Object 的一个 protected 方法， 这说明你的代码不能直接调用这个方法。 

可以看到， 默认的克隆操作是“ 浅拷贝”，并没有克隆对象中引用的其他对象。 

浅拷贝会有什么影响吗？ 这要看具体情况。如果原对象和浅克隆对象共享的子对象是不可变的， 那么这种共享就是安全的。如果子对象属于一个不可变的类， 如 String, 就是这种情况。或者在对象的生命期中， 子对象一直包含不变的常量， 没有更改器方法会改变它， 也没有方法会生成它的引用，这种情况下同样是安全的。 

不过， 通常子对象都是可变的， 必须重新定义 clone 方法来建立一个深拷贝， 同时克隆所有子对象。 

对于每一个类，需要确定：

1 ) 默认的 clone 方法是否满足要求；
2 ) 是否可以在可变的子对象上调用 clone 来修补默认的 clone 方法；
3 ) 是否不该使用 clone 。

实际上第 3 个选项是默认选项。 如果选择第 1 项或第 2 项，类必须：

1 ) 实现 Cloneable 接口；
2 ) 重新定义 clone 方法，并指定 public 访问修饰符 。

*注释：Cloneable 接口是 Java 提供的一组标记接口 ( tagging interface ) 之一。（有些程序员称之为记号接口 ( marker interface))。 应该记得， Comparable 等接口的通常用途是确保一个类实现一个或一组特定的方法。 标记接口不包含任何方法； 它唯一的作用就是允许在类型查询中使用 instanceof。建议你自己的程序中不要使用标记接口。*

即使 clone 的默认（浅拷贝）实现能够满足要求， 还是需要实现 Cloneable 接口， 将 clone重新定义为 public， 再调用 super.clone(）。 

*注释：在 Java SE 1.4 之前， clone 方法的返回类型总是 Object, 而现在可以为你的 clone 方法指定正确的返回类型。这是协变返回类型的一个例子（见第 5 章）。 *

与 Object.clone 提供的浅拷贝相比， 前面看到的 clone 方法并没有为它增加任何功能。这里只是让这个方法是公有的。 要建立深拷贝， 还需要做更多工作，克隆对象中可变的实例域。 

如果在一个对象上调用 clone, 但这个对象的类并没有实现 Cloneable 接口， Object 类的 clone 方法就会拋出一个 CloneNotSupportedException。

必须当心子类的克隆。 

要不要在自己的类中实现 clone 呢？ 如果你的客户需要建立深拷贝，可能就需要实现这个方法。有些人认为应该完全避免使用 clone, 而实现另一个方法来达到同样的目的。 clone相当笨拙， 这一点我们也同意，不过如果让另一个方法来完成这个工作， 还是会遇到同样的问题。毕竟， 克隆没有你想象中那么常用。标准库中只有不到 5% 的类实现了 done。 

*注释：所有数组类型都有一个 public 的 clone 方法， 而不是 protected: 可以用这个方法建立一个新数组， 包含原数组所有元素的副本。 *

*注释：卷 II 的第 2 章将展示另一种克隆对象的机制， 其中使用了 Java 的对象串行化特性。这个机制很容易实现， 而且很安全，但效率不高。*

## 6.3 lambda表达式

### 6.3.1 为什么引入lambda表达式

lambda 表达式是一个可传递的代码块， 可以在以后执行一次或多次。 

这两个例子有一些共同点， 都是将一个代码块传递到某个对象 （一个定时器， 或者一个sort 方法。) 这个代码块会在将来某个时间调用。 

到目前为止，在 Java 中传递一个代码段并不容易， 不能直接传递代码段 。Java 是一种面向对象语言， 所以必须构造一个对象，这个对象的类需要有一个方法能包含所需的代码 。

就现在来说，问题已经不是是否增强 Java 来支持函数式编程， 而是要如何做到这一点。设计者们做了多年的尝试，终于找到一种适合 Java 的设计。 下一节中， 你会了解 Java SE 8中如何处理代码块。 

### 6.3.2 lambda表达式的语法

```java
(String first,String second)->first.length()-second.length()
```

lambda 表达式就是一个代码块， 以及必须传人代码的变量规范。 

*注释：为什么是字母 λ? Church 已经把字母表里的所有其他字母都用完了吗？ 实际上，权威的《数学原理》一书中就使用重音符 \^ 来表示自由变量， 受此启发， Church 使用大写 lambda ( Λ ) 表示参数。 不过， 最后他还是改为使用小写的 lambda( λ) 。从那以后，带参数变量的表达式就被称为 lambda 表达式。 *

你已经见过 Java 中的一种 lambda 表达式形式：参数， 箭头（->) 以及一个表达式。如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在 { }中，并包含显式的 return 语句。 

```java
(String first,String second)->{
    if(first.length()<second.length())return -1;
    else if(first.length()>second.length())return 1;
    else return 0;
}
```

即使 lambda 表达式没有参数， 仍然要提供空括号，就像无参数方法一样 。

如果可以推导出一个 lambda 表达式的参数类型，则可以忽略其类型。 例如：

```java
Comparator<String> comp = (first, second)->first.length()-second.length();
```

在这里， 编译器可以推导出 first 和 second 必然是字符串， 因为这个 lambda 表达式将赋给一个字符串比较器。（下一节会更详细地分析这个赋值。） 

如果方法只有一个参数， 而且这个参数的类型可以推导得出，那么甚至还可以省略小括号 。

无需指定 lambda 表达式的返回类型。 lambda 表达式的返回类型总是会由上下文推导得出。 

*注释：如果一个 lambda 表达式只在某些分支返回一个值， 而在另外一些分支不返回值，这是不合法的。 例如，（int x)-> { if (x >= 0) return 1; } 就不合法。 *

### 6.3.3 函数式接口

对于只有一个抽象方法的接口， 需要这种接口的对象时， 就可以提供一个 lambda 表达式。这种接口称为**函数式接口 （functional interface )**。 

*注释：你可能想知道为什么函数式接口必须有一个抽象方法。不是接口中的所有方法都是抽象的吗？ 实际上， 接口完全有可能重新声明 Object 类的方法， 如 toString 或 clone,这些声明有可能会让方法不再是抽象的。（Java API 中的一些接口会重新声明 Object 方法来附加 javadoc 注释 Comparator API就是这样一个例子。） 更重要的是， 正如 6.1.5 节所述， 在 JavaSE 8 中， 接口可以声明非抽象方法。 *

为了展示如何转换为函数式接口， 下面考虑 Arrays.sort 方法。它的第二个参数需要一个Comparator 实例， Comparator 就是只有一个方法的接口， 所以可以提供一个 lambda 表达式 。

在底层， Arrays.sort 方法会接收实现了 Comparator\<String> 的某个类的对象。 在这个对象上调用 compare 方法会执行这个 lambda 表达式的体。这些对象和类的管理完全取决于具体实现， 与使用传统的内联类相比， 这样可能要高效得多。最好把 lambda 表达式看作是一个函数， 而不是一个对象， 另外要接受 lambda 表达式可以传递到函数式接口。 

lambda 表达式可以转换为接口， 这一点让 lambda 表达式很有吸引力。具体的语法很简短。下面再来看一个例子： 

```java
Timer t = new Timer(1000, event->{
    System.out.println("At the tone, the time is"+new Date());
    Toolkit.getDefaultToolkit().beep();
});
```

与使用实现了ActionListener接口的类相比，这个代码可读性要好得多。

实际上，在 Java 中， 对 lambda 表达式所能做的也只是能转换为函数式接口。在其他支持函数字面量的程序设计语言中，可以声明函数类型（如（String, String) -> int )、 声明这些类型的变量，还可以使用变量保存函数表达式。不过， Java 设计者还是决定保持我们熟悉的接口概念， 没有为 Java 语言增加函数类型。 

*注释：甚至不能把lambda 表达式赋给类型为 Object 的变量，Object 不是一个函数式接口。 *

Java API 在 java.util.function 包中定义了很多非常通用的函数式接口。其中一个接口BiFunction<T, U, R> 描述了参数类型为 T 和 U 而且返回类型为 R 的函数。可以把我们的字符串比较 lambda 表达式保存在这个类型的变量中 。

不过， 这对于排序并没有帮助。没有哪个 Arrays.sort 方法想要接收一个 BiFunction。 如果你之前用过某种函数式程序设计语言，可能会发现这很奇怪。不过， 对于 Java 程序员而言，这非常自然。类似 Comparator 的接口往往有一个特定的用途， 而不只是提供一个有指定参数和返回类型的方法。Java SE 8 沿袭了这种思路。想要用 lambda 表达式做某些处理，还是要谨记表达式的用途， 为它建立一个特定的函数式接口。 

java.util.function 包中有一个尤其有用的接口 Predicate 。

ArrayList 类有一个 removeIf 方法， 它的参数就是一个 Predicate。这个接口专门用来传递lambda 表达式。 

### 6.3.4 方法引用

表达式 System.out::println 是一个方法引用（ method reference ), 它等价于 lambda 表达式x -> System.out.println(x) 

再来看一个例子， 假设你想对字符串排序， 而不考虑字母的大小写。可以传递以下方法表达式： 

```java
Arrays.sort(strings, String::compareToIgnoreCase)
```

从这些例子可以看出， 要用 :: 操作符分隔方法名与对象或类名。 主要有3种情况：

- object::instanceMethod
- Class::staticMethod
- Class::instanceMethod

在前 2 种情况中， 方法引用等价于提供方法参数的 lambda 表达式。前面已经提到，System.out::println 等价于 x -> System.out.println(x) 。类似地， Math::pow 等价于（x，y) ->Math.pow(x, y)。 

对于第 3 种情况， 第 1 个参数会成为方法的目标。例如，String::compareToIgnoreCase 等
同于 (x, y)-> x.compareToIgnoreCase(y) 。

*注释：如果有多个同名的重载方法， 编译器就会尝试从上下文中找出你指的那一个方法。例如， Math.max 方法有两个版本， 一个用于整数， 另一个用于 double 值。选择哪一个版本取决于 Math::max 转换为哪个函数式接口的方法参数。 类似于 lambda 表达式， 方法引用不能独立存在，总是会转换为函数式接口的实例。 *

可以在方法引用中使用 this 参数。 例如， this::equals 等同于 x-> this.equals(x)。 使用super 也是合法的。`Super::instanceMethod`使用this为目标，会调用给定方法的超类版本。

###  6.3.5 构造器引用

构造器引用与方法引用很类似，只不过方法名为 new。例如， Person::new 是 Person 构造器的一个引用。哪一个构造器呢？ 这取决于上下文。 

可以用数组类型建立构造器引用。例如， int[]::new 是一个构造器引用，它有一个参数：即数组的长度。这等价于 lambda 表达式 x-> new int[x] 。

Java 有一个限制，无法构造泛型类型 T 的数组。数组构造器引用对于克服这个限制很有用。表达式 new T[n] 会产生错误，因为这会改为 new Object[n] 。对于开发类库的人来说，这是一个问题。例如，假设我们需要一个 Person 对象数组。 Stream 接口有一个 toArray 方法可以返回 Object 数组。

不过，这并不让人满意。用户希望得到一个 Person 引用数组，而不是 Object 引用数组。流库利用构造器引用解决了这个问题。可以把 Person[]::new 传入 toArray 方法 

toArray方法调用这个构造器来得到一个正确类型的数组。然后填充这个数组并返回。 

### 6.3.6 变量作用域

通常， 你可能希望能够在 lambda 表达式中访问外围方法或类中的变量。 

要了解到底会发生什么，下面来巩固我们对 lambda 表达式的理解 lambda 表达式有 3个部分：

1 ) 一个代码块；
2 ) 参数;
3 ) 自由变量的值， 这是指非参数而且不在代码中定义的变量。 

在我们的例子中， 这个 lambda 表达式有 1 个自由变量 text。 表示 lambda 表达式的数据结构必须存储自由变量的值， 在这里就是字符串 "Hello"。 我们说它被 lambda 表达式捕获（captured）（下面来看具体的实现细节。 例如， 可以把一个 lambda 表达式转换为包含一个方法的对象，这样自由变量的值就会复制到这个对象的实例变量中。） 

*注释：关于代码块以及自由变量值有一个术语： 闭包（closure。) 如果有人吹嘘他们的语言有闭包，现在你也可以自信地说 Java 也有闭包。 在 Java 中， lambda 表达式就是闭包。 *

可以看到， lambda 表达式可以捕获外围作用域中变量的值。 在 Java 中， 要确保所捕获的值是明确定义的，这里有一个重要的限制。在 lambda 表达式中， 只能引用值不会改变的变量。 

之所以有这个限制是有原因的。 如果在 lambda 表达式中改变变量， 并发执行多个动作时就会不安全。对于目前为止我们看到的动作不会发生这种情况，不过一般来讲，这确实是一个严重的问题。 

另外如果在 lambda 表达式中引用变量， 而这个变量可能在外部改变，这也是不合法的。 

这里有一条规则：lambda 表达式中捕获的变量必须实际上是最终变量 ( effectively final)。实际上的最终变量是指， 这个变量初始化之后就不会再为它赋新值。 

lambda 表达式的体与嵌套块有相同的作用域。这里同样适用命名冲突和遮蔽的有关规则。在 lambda 表达式中声明与一个局部变量同名的参数或局部变量是不合法的。 

在方法中，不能有两个同名的局部变量， 因此， lambda 表达式中同样也不能有同名的局部变量。

在一个 lambda 表达式中使用 this 关键字时， 是指创建这个 lambda 表达式的方法的 this参数。 	在 lambda 表达式中， this 的使用并没有任何特殊之处。lambda 表达式的作用域嵌套在 init 方法中，与出现在这个方法中的其他位置一样， lambda 表达式中 this 的含义并没有变化 。

### 6.3.7 处理lambda表达式

使用 lambda 表达式的重点是延迟执行（ deferred execution ）。	之所以希望以后再执行代码， 这有很多原因， 如：

- 在一个单独的线程中运行代码；
- 多次运行代码；
- 在算法的适当位置运行代码 （例如， 排序中的比较操作；)
- 发生某种情况时执行代码 （如， 点击了一个按钮， 数据到达， 等等；)
- 只在必要时才运行代码。 

Runnable接口

表6-1 常用函数式接口：

| 函数式接口         | 参数类型 | 返回类型 | 抽象方法名 | 描述                         | 其他方法                   |
| ------------------ | -------- | -------- | ---------- | ---------------------------- | -------------------------- |
| Runnable           | 无       | void     | run        | 作为无参数或返回值的动作运行 |                            |
| Suppier\<T>        | 无       | T        | get        | 提供一个T类型的值            |                            |
| Consumer\<T>       | T        | void     | accept     | 处理一个T类型的值            | andThen                    |
| BiConsumer\<T,U>   | T,U      | void     | accept     | 处理T和U类型的值             | andThen                    |
| Function<T,R>      | T        | R        | apply      | 有一个T类型参数的函数        | compose, andThen, identity |
| BiFunction\<T,U,R> | T,U      | R        | apply      | 有T和U类型参数的函数         | andThen                    |
| UnaryOperator\<T>  | T        | T        | apply      | 类型T上的一元操作符          | compose, andThen, identity |
| BinaryOperator\<T> | T,T      | T        | apply      | 类型T上的二元操作符          | andThen, maxBy, minBy      |
| Predicate\<T>      | T        | boolean  | test       | 布尔值函数                   | and, or, negate, isEqual   |
| BiPredicate\<T,U>  | T,U      | boolean  | test       | 有两个参数的布尔值函数       | and, or, negate            |

表 6-2 列出了基本类型 int、 long 和 double 的 34 个可能的规范。 最好使用这些特殊化规范来减少自动装箱。 出于这个原因， 我在上一节的例子中使用了 IntConsumer 而不是Consumer\<lnteger>。 

表6-2 基本类型的函数式接口：

| 函数式接口         | 参数类型 | 返回类型 | 抽象方法名   |
| ------------------ | -------- | -------- | ------------ |
| BooleanSupplier    | none     | boolean  | getAsBoolean |
| PSupplier          | none     | p        | getAsP       |
| PConsumer          | p        | void     | accept       |
| ObjPConsumer\<T>   | T,p      | void     | accept       |
| PFunction\<T>      | p        | T        | apply        |
| PToQFunction       | p        | q        | applyAsQ     |
| ToPFunction\<T>    | T        | p        | applyAsP     |
| ToPBiFunction<T,U> | T,U      | p        | applyAsP     |
| PUnaryOperator     | p        | p        | applyAsP     |
| PBinaryOperator    | p,p      | p        | applyAsP     |
| PPredicate         | p        | boolean  | test         |

注：p, q为int, long, double; P, Q为Int、Long、Double。

*提示：最好使用表 6-1 或表 6-2 中的接口。 例如， 假设要编写一个方法来处理满足某个特定条件的文件。 对此有一个遗留接口 java.io.FileFilter, 不过最好使用标准的Predicate\<File> , 只有一种情况下可以不这么做， 那就是你已经有很多有用的方法可以生成 FileFilter 实例。*

*注释：大多数标准函数式接口都提供了非抽象方法来生成或合并函数。 例如， Predicate.isEqual(a) 等同于 a::equals, 不过如果 a 为 null 也能正常工作。 已经提供了默认方法 and、or 和 negate 来合并谓词。 例如， Predicate.isEqual(a).or(Predicate.isEqual(b)) 就等同于 x->a.equals(x) || b.equals(x)。*

*注释：如果设计你自己的接口，其中只有一个抽象方法，可以用 @FunctionalInterface 注解来标记这个接口。 这样做有两个优点。 如果你无意中增加了另一个非抽象方法， 编译器会产生一个错误消息。 另外 javadoc 页里会指出你的接口是一个函数式接口。*
*并不是必须使用注解根据定义， 任何有一个抽象方法的接口都是函数式接口。不过使用 @FunctionalInterface 注解确实是一个很好的做法。*

### 6.3.8 再谈Comparator

Comparator 接口包含很多方便的静态方法来创建比较器。 这些方法可以用于 lambda 表达式或方法引用。 

静态 comparing 方法取一个“ 键提取器” 函数， 它将类型 T 映射为一个可比较的类型( 如 String )。 对要比较的对象应用这个函数， 然后对返回的键完成比较。 例如， 假设有一个Person 对象数组，可以如下按名字对这些对象排序： 

```java
Arrays.sort(people, Comparator.comparing(Person::getName));
```



可以把比较器与 thenComparing 方法串起来。 

```java
Arrays.sort(people, 
            Comparator.comparing(Person::getLastName)
            .thenComparing(Person::getFirstName));
```

如果两个人的姓相同， 就会使用第二个比较器。 

这些方法有很多变体形式。 可以为 comparing 和 thenComparing 方法提取的键指定一个比较器。 

另外， comparing 和 thenComparing 方法都有变体形式，可以避免 int、 long 或 double 值的装箱。 

如果键函数可以返回 null, 可能就要用到 nullsFirst 和 nullsLast适配器。 这些静态方法会修改现有的比较器，从而在遇到 null 值时不会抛出异常， 而是将这个值标记为小于或大于正常值。 

nullsFirst 方法需要一个比较器， 在这里就是比较两个字符串的比较器。 naturalOrder 方法可以为任何实现了 Comparable 的类建立一个比较器。在这里， Comparator.\<String>naturalOrder() 正是
我们需要的。 

静态 reverseOrder 方法会提供自然顺序的逆序。要让比较器逆序比较， 可以使用 reversed实例方法 。

## 6.4 内部类

**内部类（inner class）**是定义在另一个类中的类。为什么需要使用内部类呢？ 其主要原因有以下三点：

- 内部类方法可以访问该类定义所在的作用域中的数据， 包括私有的数据。
- 内部类可以对同一个包中的其他类隐藏起来。
- 当想要定义一个回调函数且不想编写大量代码时，使用匿名 （anonymous) 内部类比较便捷。 

*C++注释：C++ 有嵌套类。一个被嵌套的类包含在外围类的作用域内。	嵌套是一种类之间的关系， 而不是对象之间的关系。	嵌套类有两个好处： 命名控制和访问控制。 由于名字 Iterator 嵌套在 LinkedList 类的内部， 所以在外部被命名为 LinkedList::Iterator， 这样就不会与其他名为 Iterator 的类发生冲突。在 Java 中这个并不重要， 因为 Java 包已经提供了相同的命名控制。 需要注意的是， Link 类位于 LinkedList 类的私有部分， 因此， Link 对其他的代码均不可见。鉴于此情况， 可以将 Link 的数据域设计为公有的， 它仍然是安全的。 这些数据域只能被LinkedList 类 （具有访问这些数据域的合理需要）中的方法访问， 而不会暴露给其他的代码。在 Java 中， 只有内部类能够实现这样的控制。*
*然而， Java 内部类还有另外一个功能， 这使得它比 C++ 的嵌套类更加丰富， 用途更加广泛。 内部类的对象有一个隐式引用， 它引用了实例化该内部对象的外围类对象。通过这个指针， 可以访问外围类对象的全部状态。在本章后续内容中， 我们将会看到有关这个 Java 机制的详细介绍*
*在 Java 中，static 内部类没有这种附加指针， 这样的内部类与 C++ 中的嵌套类很相似。*

### 6.4.1 使用内部类访问对象状态

内部类既可以访问自身的数据域，也可以访问创建它的外围类对象的数据域 

为了能够运行这个程序， 内部类的对象总有一个隐式引用， 它指向了创建它的外部类对象。 	这个引用在内部类的定义中是不可见的。 

外围类的引用在构造器中设置。编译器修改了所有的内部类的构造器， 添加一个外围类引用的参数。 

*注释：只有内部类可以是私有类，而常规类只可以具有包可见性，或公有可见性。*

### 6.4.2 内部类的特殊语法规则

事实上，使用外围类引用的正规语法还要复杂一些。表达式 `OuterClass.this`表示外围类引用。

反过来，可以采用下列语法格式更加明确地编写内部对象的构造器 ：`outerObject.new InnerClass(construction parameters)`。

在这里，最新构造的 TimePrinter 对象的外围类引用被设置为创建内部类对象的方法中的this 引用。这是一种最常见的情况。通常， this 限定词是多余的。不过，可以通过显式地命名将外围类引用设置为其他的对象。 

需要注意， 在外围类的作用域之外，可以这样引用内部类：`OuterClass.InnerClass `

*注释： 内部类中声明的所有静态域都必须是 final。原因很简单。我们希望一个静态域只有一个实例， 不过对于每个外部对象， 会分别有一个单独的内部类实例。如果这个域不是 final, 它可能就不是唯一的。
内部类不能有 static 方法。Java 语言规范对这个限制没有做任何解释。也可以允许有静态方法，但只能访问外围类的静态域和方法。 显然， Java 设计者认为相对于这种复杂性来说， 它带来的好处有些得不偿失。* 

### 6.4.3 内部类是否有用、必要和安全

当在 Java 1.1 的 Java语言中增加内部类时， 很多程序员都认为这是一项很主要的新特性，但这却违背了 Java 要比 C++ 更加简单的设计理念。 内部类的语法很复杂（可以看到，稍后介绍的匿名内部类更加复杂 )。它与访问控制和安全性等其他的语言特性的没有明显的关联 。

我们并不打算就这个问题给予一个完整的答案。 内部类是一种编译器现象， 与虚拟机无关。编译器将会把内部类翻译成用 $ (美元符号）分隔外部类名与内部类名的常规类文件， 而虚拟机则对此一无所知。 

javap -private ClassName

*注释：如果使用 UNIX， 并以命令行的方式提供类名， 就需要记住将 $ 字符进行转义*

可以清楚地看到， 编译器为了引用外围类， 生成了一个附加的实例域 this\$0 (名字this\$0 是由编译器合成的，在自己编写的代码中不能够引用它）。另外，还可以看到构造器的TalkingClock（外围类） 参数 。

由于内部类拥有访问特权， 所以与常规类比较起来功能更加强大。 

请注意编译器在外围类添加静态方法 access\$0。它将返回作为参数传递给它的对象域beep。（方法名可能稍有不同， 如 access\$000, 这取决于你的编译器）。 

这样做不是存在安全风险吗？ 这种担心是很有道理的。任何人都可以通过调用 access\$0方法很容易地读取到私有域 beep。当然， access\$0 不是 Java 的合法方法名。但熟悉类文件结构的黑客可以使用十六进制编辑器轻松地创建一个用虚拟机指令调用那个方法的类文件。由于隐秘地访问方法需要拥有包可见性，所以攻击代码需要与被攻击类放在同一个包中。 

*注释： 合成构造器和方法是复杂令人费解的（如果过于注重细节， 可以跳过这个注释）。假设将 TimePrinter 转换为一个内部类。在虚拟机中不存在私有类， 因此编译器将会利用私有构造器生成一个包可见的类：`private TalkingClock$TimePrinter(TalkingClock);`
当然，没有人可以调用这个构造器， 因此， 存在第二个包可见构造器`TalkingClock$TimePrinter(TalkingGock, TalkingClock$1);`它将调用第一个构造器。
编译器将 TalkingClock 类 start 方法中的构造器调用翻译为：`new TalkingClock$TimePrinter(this, null)`*

### 6.4.4 局部内部类

如果仔细地阅读一下 TalkingClock 示例的代码就会发现， TimePrinter 这个类名字只在start 方法中创建这个类型的对象时使用了一次。
当遇到这类情况时， 可以在一个方法中定义局部类。 

局部类不能用 public 或 private 访问说明符进行声明。它的作用域被限定在声明这个局部类的块中。 

局部类有一个优势， 即对外部世界可以完全地隐藏起来。 即使 TalkingClock 类中的其他代码也不能访问它。除 start 方法之外， 没有任何方法知道 TimePrinter 类的存在 。

### 6.4.5 由外部方法访问变量

与其他内部类相比较， 局部类还有一个优点。它们不仅能够访问包含它们的外部类， 还可以访问局部变量。不过， 那些局部变量必须事实上为 final。这说明， 它们一旦赋值就绝不会改变。 

从程序员的角度看， 局部变量的访问非常容易。它减少了需要显式编写的实例域， 从而使得内部类更加简单。 

*注释：在 JavaSE 8 之前， 必须把从局部类访问的局部变量声明为 final。*

有时， final 限制显得并不太方便。 补救的方法是使用一个长度为 1 的数组 

在内部类被首次提出时， 原型编译器对内部类中修改的局部变量自动地进行转换。不过， 后来这种做法被废弃。毕竟， 这里存在一个危险。同时在多个线程中执行内部类中的代码时， 这种并发更新会导致竞态条件—有关内容参见第 14 章。 

### 6.4.6 匿名内部类

将局部内部类的使用再深人一步。 假如只创建这个类的一个对象，就不必命名了。这种类被称为**匿名内部类（anonymous inner class)**。 

由于构造器的名字必须与类名相同， 而匿名类没有类名， 所以， 匿名类不能有构造器。取而代之的是，将构造器参数传递给超类 （ superclass) 构造器。尤其是在内部类实现接口的时候， 不能有任何构造参数。 

如果构造参数的闭小括号后面跟一个开大括号， 正在定义的就是匿名内部类 。

==多年来， Java 程序员习惯的做法是用匿名内部类实现事件监听器和其他回调。 如今最好还是使用 lambda 表达式。==

*注释： 下面的技巧称为“ 双括号初始化” （double brace initialization), 这里利用了内部类语法。假设你想构造一个数组列表， 并将它传递到一个方法：
ArrayList\<String> friends = new ArrayList\<>()；
friends.add("Harry");
friends.add("Tony");
invite(friends);
如果不再需要这个数组列表， 最好让它作为一个匿名列表。不过作为一个匿名列表，该如何为它添加元素呢？ 方法如下：
invite(new ArrayList\<String>() {{ add("Harry"); add("Tony"); }});
注意这里的双括号。 外层括号建立了 ArrayList 的一个匿名子类。 内层括号则是一个对象构造块（见第 4 章)。*

*警告： 建立一个与超类大体类似（但不完全相同）的匿名子类通常会很方便。不过， 对于 equals 方法要特别当心。 第 5 章中， 我们曾建议 equals 方法最好使用以下测试：`if (getClass() != other.getClass()) return false;`
但是对匿名子类做这个测试时会失败。*

*提示：生成曰志或调试消息时， 通常希望包含当前类的类名， 如：`Systen.err.println("Something awful happened in " + getClass());`
不过， 这对于静态方法不奏效。毕竟， 调用 getClass 时调用的是 this.getClass(), 而静态方法没有 this 。所以应该使用以下表达式：`new Object0{}.getClass().getEnclosingClass() // gets class of static method`
在这里，newObject0{} 会建立 Object 的一个匿名子类的一个匿名对象，getEnclosingClass
则得到其外围类， 也就是包含这个静态方法的类。*

### 6.4.7 静态内部类

有时候， 使用内部类只是为了把一个类隐藏在另外一个类的内部，并不需要内部类引用外围类对象。为此，可以将内部类声明为 static, 以便取消产生的引用。 

当然， 只有内部类可以声明为 static。静态内部类的对象除了没有对生成它的外围类对象的引用特权外， 与其他所冇内部类完全一样。在我们列举的示例中， 必须使用静态内部类，这是由于内部类对象是在静态方法中构造的 

*注释： 在内部类不需要访问外围类对象的时候， 应该使用静态内部类。 有些程序员用嵌套类 （nested class ) 表示静态内部类*

*注释： 与常规内部类不同，静态内部类可以有静态域和方法。*

*注释： 声明在接口中的内部类自动成为 static 和 public 类*

## 6.5 代理

代理 （ proxy)。 利用代理可以在运行时创建一个实现了一组给定接口的新类 : 这种功能只有在编译时无法确定需要实现哪个接口时才有必要使用。 

### 6.5.1 何时使用代理

假设有一个表示接口的 Class 对象（有可能只包含一个接口，) 它的确切类型在编译时无法知道。这确实有些难度。要想构造一个实现这些接口的类， 就需要使用 newlnstance 方法或反射找出这个类的构造器。但是， 不能实例化一个接口，需要在程序处于运行状态时定义一个新类。

为了解决这个问题， 有些程序将会生成代码；将这些代码放置在一个文件中；调用编译器；然后再加载结果类文件。很自然， 这样做的速度会比较慢，并且需要将编译器与程序放在一起。而代理机制则是一种更好的解决方案。代理类可以在运行时创建全新的类。这样的代理类能够实现指定的接口。尤其是，它具有下列方法：

- 指定接口所需要的全部方法。
- Object 类中的全部方法， 例如， toString、 equals 等。

然而，不能在运行时定义这些方法的新代码。而是要提供一个调用处理器（invocation handler)。调用处理器是实现了 InvocationHandler 接口的类对象。在这个接口中只有一个方法：
`Object invoke(Object proxy, Method method, Object[] args)`
无论何时调用代理对象的方法， 调用处理器的 invoke 方法都会被调用， 并向其传递Method 对象和原始的调用参数。 调用处理器必须给出处理调用的方式。 

### 6.5.2 创建代理对象

要想创建一个代理对象， 需要使用 Proxy 类的 newProxylnstance 方法。 这个方法有三个参数：

- 一个类加载器（class loader)。作为 Java 安全模型的一部分， 对于系统类和从因特网上下载下来的类，可以使用不同的类加载器。有关类加载器的详细内容将在卷 II 第 9章中讨论。目前， 用 null 表示使用默认的类加载器。
- 一个 Class 对象数组， 每个元素都是需要实现的接口。
- 一个调用处理器。

还有两个需要解决的问题。 如何定义一个处理器？ 能够用结果代理对象做些什么？ 当然， 这两个问题的答案取决于打算使用代理机制解决什么问题。 使用代理可能出于很多原因， 例如：

- 路由对远程服务器的方法调用。
- 在程序运行期间，将用户接口事件与动作关联起来。
- 为调试， 跟踪方法调用 。

*注释： 前面已经讲过， 在 Java SE 5.0 中， Integer 类实际上实现了 Comparable\<Integer>。 然而， 在运行时， 所有的泛型类都被取消， 代理将它们构造为原 Comparable 类的类对象。*

### 6.5.3 代理类的特性

需要记住， 代理类是在程序运行过程中创建的。 然而， 一旦被创建， 就变成了常规类， 与虚拟机中的任何其他
类没有什么区别。 

所有的代理类都扩展于 Proxy 类。一个代理类只有一个实例域—调用处理器，它定义在 Proxy 的超类中。 为了履行代理对象的职责， 所需要的任何附加数据都必须存储在调用处理器中。  

所有的代理类都覆盖了 Object 类中的方法 toString、 equals 和 hashCode。如同所有的代理方法一样，这些方法仅仅调用了调用处理器的 invoke。Object 类中的其他方法（如 clone和 getClass) 没有被重新定义。 

没有定义代理类的名字，Sun 虚拟机中的 Proxy类将生成一个以字符串 $Proxy 开头的类名。 

对于特定的类加载器和预设的一组接口来说， 只能有一个代理类。 也就是说， 如果使用同一个类加载器和接口数组调用两次 newProxylnstance 方法的话， 那么只能够得到同一个类的两个对象，也可以利用 getProxyClass方法获得这个类 

代理类一定是 public 和 final。 如果代理类实现的所有接口都是 public， 代理类就不属于某个特定的包；否则， 所有非公有的接口都必须属于同一个包，同时，代理类也属于这个包。

可以通过调用 Proxy 类中的 isProxyClass 方法检测一个特定的 Class 对象是否代表一个代理类。 

【API】java.Iang.reflect.InvocationHandler 1.3 :

-  Object invoke(Object proxy,Method method,0bject[] args)
  定义了代理对象调用方法时希望执行的动作。

【API】 java.Iang.reflect.Proxy 1.3 :

- `static Class<?> getProxyClass(Cl assLoader loader, Class<?>...
  interfaces)` 返回实现指定接口的代理类。
- `static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handler)` 构造实现指定接口的代理类的一个新实例。所有方法会调用给定处理器对象的 invoke 方法。
- `static boolean isProxyClass(Class<?> cl)`
  如果 cl 是一个代理类则返回 true。 

