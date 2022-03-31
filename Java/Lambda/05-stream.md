## 4. Stream流编程

Stream流编程是一种函数式编程。

### 4.1 Stream概述

**(1) 函数式编程简介**

函数式编程是一种编程范式，是如何编写程序的一种方法论。它属于结构化编程的一种，主要思想是把对数据的一连串操作通过链式函数调用的方式来实现。其属于Fluent风格编程的范畴。

**(2) Stream简介**

在JDK8中，Stream是一个接口，其中包含了很多方法，这些方法可以对流中的数据进行操作。这些方法共分为三类：

- 创建Stream接口对象的方法，这些方法一般都需要通过输入原始操作数据，然后创建数据池

- 返回Stream类型的中间操作方法

- 返回其它类型的方法，这些方法即终止操作方法

**(3) Stream与函数式编程**

Stream流编程是一种函数式编程，或者说函数式编程是Stream流编程的实现方式。Stream流编程的具体实现步骤如下：

- 输入要操作的数据，将数据放入到数据池，而Stream就是这个数据池

- Stream接连调用中间操作函数，这些函数均返回Stream类型

- Steam调用终止操作函数，使链式调用结束，让其返回最终的操作结果，即数据池中的最终数据

Stream流编程需要注意的问题：

- 中间操作返回的都是Stream，可以保证链式调用

- 这些函数都有一个共同的任务，对Stream流中的每一个数据都进行操作

- 一个中间函数对于数据池的操作结果将作为下一个中间操作函数的数据输入

- 所有操作都是不可逆的，一旦操作就会影响数据池中的数据

**(4) 惰性求值**

Stream流编程中对于中间方法的执行，存在一个惰性求值问题：多个中间操作可以连接起来形成一个链式调用，除非链式调用的最后执行了终止操作，否则中间操作是不会执行任何处理的。即只有当终止操作执行时才会触发链式调用的执行，这种方法调用方式称为惰性求值。

### 4.2 Stream流的创建

**(1) 使用数组创建流**

使用Arrays的静态方法stream()可以创建流，即数据池中存放的是数组中的数据。

```java
@Test
    public void test01() {
        int[] nums = {1, 2, 3, 4, 5};
        IntStream stream1 = Arrays.stream(nums);
        IntStream stream2 = IntStream.of(nums);
    }
}
```

**(2) 使用集合创建流**

使用java.util.Collection接口的方法可以创建集合流，即数据池中存放的是集合中的数据。

```java
@Test
public void test02() {
    List<String> list = new ArrayList<>();
    Stream<String> stream1 = list.stream();

    Set<String> set = new HashSet<>();
    Stream<String> stream2 = set.stream();
}
```

**(3) 创建数字流**

通过“数字类型Stream”类的of()或rangeClosed()方法可以创建包含指定数字的Stream，即数据池中存放的是指定的数据。

需要注意的是这些数字流接口，例如IntStream、LongStream、DoubleStream等与Stream接口并无继承关系。但它们可以通过装箱方法boxed()转换为Stream类型。

也可以通过Random的方法创建包含指定个数随机数的流。

```java
@Test
public void test03() {
    //1, 2, 3, 4, 5
    IntStream stream1 = IntStream.of(1, 2, 3, 4, 5);
    //[1, 5)
    IntStream stream2 = IntStream.range(1, 5);
    //[1, 5]
    IntStream stream3 = IntStream.rangeClosed(1, 5);
    //创建一个无限流并限制数量
    IntStream stream4 = new Random().ints().limit(5);
    //上一种流的等价写法
    IntStream stream5 = new Random().ints(5);
}
```

**(4) 自定义流**

通过Stream类的generate()方法自定义流，其中generate()方法中的参数为提供者Supplier，该流中的数据池中的数据就来自于这个提供者。

```java
@Test
public void test04() {
    Stream<Double> stream = Stream.generate(() -> Math.random()).limit(5);
}
```

### 4.3 并行处理与串行处理

### 4.4 Stream流的中间操作

### 4.5 Stream流的终止操作

### 4.6 收集器

