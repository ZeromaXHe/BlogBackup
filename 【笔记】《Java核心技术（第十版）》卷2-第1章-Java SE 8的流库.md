# 第1章 Java SE 8的流库

在本章中，你将学习如何使用Java的流库，它是在Java SE 8中引入的，用来以“做什么而非怎么做”的方式处理集合。

## 1.1 从迭代到流的操作

流表明上看起来和集合很类似，都可以让我们转换和获取数据。但是，它们之间存在着显著的差异：

1. 流并不存储其元素。这些元素可能存储在底层的集合中，或者是按需生成的。
2. 流的操作不会修改其数据源。例如，filter方法不会从新的流中移除元素，而是会生成一个新的流，其中不包含被过滤掉的元素。
3. 流的操作是尽可能惰性执行的。这意味着直至需要其结果时，操作才会执行。

【API】java.util.stream.Stream\<T> 8 ：

- `Stream<T> filter(Predicate <? super T> p)`
  产生一个流，其中包含当前流中满足P的所有元素。
- `long count()`
  产生当前流中元素的数量。这是一个终止操作。

【API】java.util.Collection\<E> 1.2 :

- `default Stream<E> stream()`
- `default Stream<E> parallelStream()`
  产生当前集合中所有元素的顺序流或并行流。

## 1.2 流的创建

静态的stream.of方法

of方法具有可变长参数，因此我们可以构建具有任意数量引元的流

使用Array.stream(array,from,to)可以从数组中位于from(包括)和to(不包括)的元素中创建一个流。

为了创建不包含任何元素的流，可以使用静态的Stream.empty方法

Stream接口有两个用于创建无限流的静态方法。generate方法会接受一个不包含任何引元的函数（或者从技术上来讲，是一个Supplier\<T>接口的对象）。

*注意：Java API中有大量方法都可以产生流。例如，Pattern类有一个splitAsStream方法*

【API】java.util.stream.Stream 8 :

- `static <T> Stream<T> of(T... values)`
  产生一个元素为给定值的流。
- `static <T> Stream<T> empty()`
  产生一个不包含任何元素的流
- `static <T> Stream<T> generate(Supplier<T> s)`
  产生一个无限流，它的值是通过反复调用函数s而构建的。
- `static <T> Stream<T> iterate(T seed, UnaryOperator<T> f)`
  产生一个无限流，它的元素包含种子、在种子上调用f产生的值、在前一个元素上调用f产生的值，等等。

【API】java.util.Arrays 1.2 :

- `static <T> Stream<T> stream(T[] array, int startInclusive, int endExclusive) 8`
  产生一个流，它的元素是由数组指定范围内的元素构成的。

【API】java.util.regex.Pattern 1.4 :

- `Stream<String> splitAsStream(CharSequence input) 8`
  产生一个流，它的元素是输入中由该模式界定的部分。

【API】java.nio.file.Files 7：

- `static Stream<String> lines(Path path)` 8
- `static Stream<String> lines(Path path, Charset cs)` 8
  产生一个流，它的元素是指定文件中的行，该文件的字符集为UTF-8，或者为指定的字符集。

【API】java.util.function.Supplier\<T> 8 :

- `T get()`
  提供一个值。

## 1.3 filter、map和flatMap方法

filter转换会产生一个流，它的元素与某种条件相匹配。

filter的引元是Predicate\<T>,即从T到boolean的函数。

通常，我们想要按照某种方式来转换流中的值，此时，可以使用map方法并传递执行该转换的函数。

我们得到一个包含流的流，为了将其摊平为字母流，可以使用flatMap方法而不是map方法。

【API】java.util.stream.Stream 8 :

- `Stream<T> filter(Predicate<? super T> predicate)`
  产生一个流，它包含当前流中所有满足断言条件的元素。
- `<R> Stream<R> map(Function<? super T, ? extends R> mapper)`
  产生一个流，它包含将mapper应用于当前流中所有元素所产生的结果。
- `<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)`
  产生一个流，它是通过将mapper应用于当前流中所有元素所产生的结果连接到一起而获得的。（注意，这里的每一个结果都是一个流。）

## 1.4 抽取子流和连接流

调用stream.limit(n)会返回一个新的流，它在n个元素之后结束（如果原来的流更短，那么就会在流结束时结束）。这个方法对于裁剪无限流的尺寸会显得特别有用。

调用stream.skip(n)正好相反：它会丢弃前n个元素。

我们可以用Steam类的静态的concat方法将两个流连接起来。

当然，第一个流不应该是无限的，否则第二个流永远都不会得到处理的机会。

【API】java.util.stream.Stream 8 :

- `Stream<T> limit(long maxSize)`
  产生一个流，其中包含了当前流中最初的maxSize个元素。
- `Stream<T> skip(long n)`
  产生一个流，它的元素是当前流中除了前n个元素之外的所有元素。
- `static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)`
  产生一个流，它的元素是a的元素后面跟着b的元素。

## 1.5 其他的流转换

distinct方法会返回一个流，它的元素是从原有流中产生的，即原来的元素按照同样的顺序剔除重复元素后产生的。这个流显然能够记住它已经看到过的元素。

对于流的排序，有多种sorted方法的变体可用。其中

