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

流的操作默认下都是由一个线程以串行的方式逐个对流中的元素进行处理，为了提高处理效率，Stream支持以并行的方式对流中的元素进行处理。

使用Stream流编程对于并行操作非常简单，无需自己创建Thread多线程，只需调用parallel方法即可实现多线程环境。

**(1) 串行处理**

```java
@Test
public void test01() {
    IntStream.range(1, 101).peek(Test01::print).count();
}
```

**(2) 并行处理**

parallel方法的位置不会影响其执行。

```java
@Test
public void test02() {
    IntStream.range(1, 101).parallel().peek(Test01::print).count();
}
```

```java
@Test
public void test02() {
    IntStream.range(1, 101).peek(Test01::print).parallel().count();
}
```

**(3) 串并行混合处理**

先并行后串行，最终以串行进行处理

```java
@Test
public void test03() {
    IntStream.range(1, 101)
            .parallel() //并行处理
            .peek(Test01::print)
            .sequential() // 串行处理
            .peek(Test01::printRed)
            .count();
}
```

先串行后并行，最终以并处进行处理

```java
@Test
public void test03() {
    IntStream.range(1, 101)
            .sequential() // 串行处理
            .peek(Test01::print)
            .parallel() //并行处理
            .peek(Test01::printRed)
            .count();
}
```

**(4) 线程数修改**

串行处理时仅有一个main线程

```java
@Test
public void test04() {
    IntStream.range(1, 100).peek(Test01::printThread).count();
}
```

并行处理的默认线程数量为当前操作系统的最大线程数量

```java
@Test
public void test05() {
    IntStream.range(1, 100)
            .parallel()
            .peek(Test01::printThread)
            .count();
}
```

**修改默认线程数**

parallel多线程默认使用的线程池为ForkJoinPool.commonPool，其默认线程数可以修改

```java
@Test
public void test06() {
    //指定默认线程池中的数量为16，其中包含指定的15个与main线程
    String key = "java.util.concurrent.ForkJoinPool.common.parallelism";
    System.setProperty(key, "15");
    IntStream.range(1, 100)
            .parallel()
            .peek(Test01::printThread)
            .count();
}
```

**使用自定义线程池**

线程池中的线程在接到“多于线程池数量的”任务时会让任务进入阻塞队列进行排队，若当前系统中多个进程同时使用这个默认的线程池，则很大情况下会形成任务阻塞的状况。为了防止这种情况的发生，parallel可以指定使用自定义的线程池，以避免与其他进行争抢。

```java
@Test
public void test07() {
    //创建线程池
    ForkJoinPool pool = new ForkJoinPool(4);
    //定义并行任务
    Callable<Long> task = () -> IntStream.range(1, 100)
            .parallel()
            .peek(Test01::printThread)
            .count();
    //向线程池提交并行任务
    pool.submit(task);
    //将pool阻塞，即会阻塞main线程的执行
    synchronized (pool) {
        try {
            pool.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

wait()，notify()和notifyAll()方法必须在同步方法或同步代码块中被调用，且哪个对象调用这些方法，哪个对象就要充当同步锁。

### 4.4 Stream流的中间操作

**(1) 中间操作分类**

返回Stream接口类型的方法称为中间操作，根据“这些操作对当前数据池中的某一元素进行操作时是否需要了解对之前元素操作的结果”标准，可以将中间操作划分为无状态操作与有状态操作。

**(2) 无状态操作**

**map(Function<T,R> action)**

这是一个无状态操作，其功能是将流中的元素映射为另一个值。由于其参数为Function，有一个输入和一个输出，所以map操作会对流中的元素产生影响。

```java
@Test
public void test08() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .peek(System.out::print)
            .map(word -> word.length())
            .forEach(System.out::println);
}
```

**mapToXxx()**

其功能是将流中的元素映射为指定类型的元素，不同的流其可映射为的元素类型是不同的，即其所拥有的mapToXxx()方法是不同的。

```java
@Test
public void test09() {
    IntStream.range(1, 10).mapToObj(i -> "No." + i)
            .forEach(System.out::println);
}
```

```java
@Test
public void test10() {
    Stream.of("111", "222", "333")
            .mapToInt(Integer::valueOf)
            .forEach(System.out::println);
}
```

**flatMap(Function<T,Stream> action)**

该方法的功能是将流中的元素映射为多个值，即偏平化map，其适用场景为流中原来的每个元素为集合，该方法用于将每个集合元素全部打散，然后添加到流中。

由于其参数为Function，有输入和输出，所以flatMap()操作会对流中元素产生影响。需要注意的是，该Function的输出类型为Stream。

```java
@Test
public void test11() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> word.chars().boxed())
            .forEach(ch -> System.out.println((char) ch.intValue()));
}
```

```java
@Test
public void test12() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .forEach(System.out::print);
}
```

**filter(Predicate\<T\> action)**

该方法用于过滤掉不适合指定条件的流中的元素，其参数为Predicate断言，用于设置过滤条件。

```java
@Test
public void test13() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .filter(word -> word.length() > 4)
            .forEach(System.out::println);
}
```

**(3) 有状态操作**

**distinct()**

过滤掉流中的重复元素，无参数

```java
@Test
public void test14() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .forEach(System.out::print);
    System.out.println();
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .distinct()
            .forEach(System.out::print);
}
```

**sorted() 或 sorted(Comparator c)**

对流中的元素进行排序，没有参数的sorted()默认是按照字典序排序的，即按照ASCII排序。可以使用带参方法指定排序规则。

```java
@Test
public void test15() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .distinct()
            .sorted()
            .forEach(System.out::println);
}
```

```java
@Test
public void test16() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .distinct()
            .sorted(Comparator.reverseOrder())
            .forEach(System.out::println);
}
```

**skip(long)**

从流中去除指定个数的元素

```java
@Test
public void test16() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(4)
            .forEach(System.out::println);
}
```

**(4) 有状态与无状态操作共存的执行流程**

已有状态和无状态为分水岭，先执行完一个状态再执行另一个状态

```java
@Test
public void test17() {
    String words = "Hello chongqing I will go";
    Stream.of(words.split(" "))
            .flatMap(word -> Stream.of(word.split("")))
            .distinct()
            .peek(System.out::print)
            .sorted()
            .forEach(System.err::print);
}
```

### 4.5 Stream流的终止操作

**(1) 终止操作分类**

终止操作的结束意味着Stream流操作结束，根据操作是否需要遍历流中的所有元素，可以将终止操作划分为短路操作与非短路操作。

**(2) 非短路操作**

**forEachOrdered(Consumer\<T\> action)**

遍历流中的元素，若流中的元素原本具有顺序，则按照原顺序排序。该方法与forEach方法的区别主要体现在并行操作中，forEach在并行操作下会打乱原顺序。

```java
@Test
public void test01() {
    String words = "I love chongqing and changsha";
    // 对于流的并行操作，forEach()处理结果是无序的
    Stream.of(words.split(" "))
            .parallel()
            .forEach(System.out::println);
}
```

```java
@Test
    public void test03() {
        String words = "I love chongqing and changsha";
        // 对于流的并行操作，forEach()处理结果是无序的
        Stream.of(words.split(" "))
                .parallel()
                .forEachOrdered(System.out::println);
    }
```

**collect(Collector col)**

将流中的最终数据收集到集合中，其参数为Collector收集器，通过Collectors的静态方法toList、toSet等可以获取到Collector对象。

```java
@Test
public void test03() {
    String words = "I love chongqing and changsha";
    // 将流中的元素收集到一个集合中
    List<String> list = Stream.of(words.split(" "))
            .collect(Collectors.toList());
    System.out.println(list);
}
```

**toArray()**

将流中的最终数据收集到数组中

```java
@Test
public void test04() {
    String words = "I love chongqing and changsha";
    // 将流中的元素收集到一个数组中
    Object[] array = Stream.of(words.split(" "))
            .toArray();
    System.out.println(Arrays.toString(array));
}
```

**count()**

统计流中的元素个数

```java
@Test
public void test05() {
    String words = "I love chongqing and changsha";
    // 统计流中的元素个数
    long count = Stream.of(words.split(" "))
            .count();
    System.out.println(count);
}
```

**reduce(BinaryOperator\<T\> bo)**

该方法的作用是将集合流最终转换为一个指定类型的数据，其参数为二元接口BinaryOperator，即两个输入一个输出，且类型相同。由两个输入最终变成一个输出，就达到了缩减的效果了。

```java
@Test
public void test06() {
    String words = "I love chongqing and changsha";
    Optional<Integer> reduce = Stream.of(words.split(" "))
            .map(word -> word.length())
            .reduce((s1, s2) -> s1 + s2);
    // Optional的get()方法在其封装的对象为空时会抛出异常
    System.out.println(reduce.get());
}
```

```java
@Test
public void test07() {
    String words = "I love chongqing and changsha";
    Optional<Integer> reduce = Stream.of(words.split(" "))
            .map(word -> word.length())
            .filter(len -> len > 200)
            .reduce((s1, s2) -> s1 + s2);
    // Optional的orElse()方法在正常情况下会返回正常的值
    // 当其封装的对象为空时会返回参数指定的值
    System.out.println(reduce.orElse(-1));
}
```

reduce()方法还有一个包含两个参数的重载方法，其中第一个参数为默认值，即在流中元素为空时所使用的值，该方法可以避免NoSuchElementException异常的抛出。

```java
@Test
public void test08() {
    String words = "I love chongqing and changsha";
    Integer length = Stream.of(words.split(" "))
            .map(word -> word.length())
            //.filter(len -> len > 200)
            .reduce(0, (s1, s2) -> s1 + s2);
    System.out.println(length);
}
```

```java
@Test
public void test09() {
    String words = "I love chongqing and changsha";
    String res = Stream.of(words.split(" "))
            //.filter(n -> n.length() > 200)
            .reduce(null, (s1, s2) -> s1 + "," + s2);
    System.out.println(res);
}
```

**max(Comparator com)**

```java
@Test
public void test10() {
    String words = "I love chongqing and changsha";
    // 从流中找出长度最大的那个单词
    String max = Stream.of(words.split(" "))
            .max((s1, s2) -> s1.length() - s2.length())
            .orElse("当前流中没有元素");
    System.out.println(max);
}
```

```java
@Test
public void test11() {
    String words = "I love chongqing and changsha";
    // 从流中找出长度最小的那个单词
    String max = Stream.of(words.split(" "))
            .max((s1, s2) -> s2.length() - s1.length())
            .orElse("当前流中没有元素");
    System.out.println(max);
}
```

**min(Comparator com)**

```java
@Test
public void test12() {
    String words = "I love chongqing and changsha";
    // 从流中找出长度最小的那个单词
    String max = Stream.of(words.split(" "))
            .min((s1, s2) -> s1.length() - s2.length())
            .orElse("当前流中没有元素");
    System.out.println(max);
}
```

```java
@Test
public void test13() {
    String words = "I love chongqing and changsha";
    // 从流中找出长度最小的那个单词
    String max = Stream.of(words.split(" "))
            .min((s1, s2) -> s2.length() - s1.length())
            .orElse("当前流中没有元素");
    System.out.println(max);
}
```

**(3) 短路操作**

**allMatch(Predicate p)**

用于判断是否所有元素都符合指定的条件，只要有一个不符合，就会马上结束匹配并返回false，只有所有都匹配上了才会返回true。

```java
@Test
public void test14() {
    String words = "I love chongqing and changsha";
    boolean allMatch = Stream.of(words.split(" "))
            .allMatch(word -> word.length() > 3);
    System.out.println(allMatch);
}
```

**anyMatch(Predicate p)**

用于判断是否存在符合条件的元素，只要找到一个符合的元素，马上结束匹配并返回true。

```java
@Test
public void test15() {
    String words = "I love chongqing and changsha";
    boolean anyMatch = Stream.of(words.split(" "))
            .anyMatch(word -> word.length() > 3);
    System.out.println(anyMatch);
}
```

**noneMatch(Predicate p)**

用于判断是否全部不符合条件，只要找到一个符合的，马上返回false，只有所有都不符合才会返回ture。

```java
@Test
public void test16() {
    String words = "I love chongqing and changsha";
    boolean noneMatch = Stream.of(words.split(" "))
            .noneMatch(word -> word.length() > 3);
    System.out.println(noneMatch);
}
```

**findFirst() 与 findAny()**

findFirst()与findAny()方法均会将从流中查找到的元素封装到Optional容器对象中。若没有找到，则Optional容器对象中的值会为空，Optional对象的isPresent()方法返回值会为false。

```java
@Test
public void test17() {
    String words = "I love chongqing and changsha";
    String element = Stream.of(words.split(" "))
            .findFirst()
            .orElse("无任何元素");
    System.out.println(element);
}
```

```java
@Test
public void test18() {
    String words = "I love chongqing and changsha";
    String element = Stream.of(words.split(" "))
            .findAny()
            .orElse("无任何元素");
    System.out.println(element);
}
```

### 4.6 收集器

Stream的collect()方法可以将流中的最终数据收集到集合中，其为一个非短路终止操作。其参数为Collector收集器，一般都是通过调用Collectors的静态方法获取收集器对象的。不过收集器的功能远比将数据收集到集合要强大得多，还可以对收集的数据进行统计、分组等操作。根据不同的需求调用Collectors的不同静态方法，获取到不同的Collector收集器。

**(1) 数据准备**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {
    private String name;
    private String school;
    private String gender;
    private int age;
    private double score;
}
```

```java
private List<Student> students;

@Before
public void before() {
    Student student0 = new Student("Dolly", "重庆大学", "女", 20, 98);
    Student student1 = new Student("Abbot", "四川大学", "男", 24, 94);
    Student student2 = new Student("Hailey", "四川大学", "男", 25, 96);
    Student student3 = new Student("Kali", "香港大学", "女", 23, 93.5);
    Student student4 = new Student("Nana", "香港大学", "女", 22, 99);
    Student student5 = new Student("Quentin", "重庆大学", "男", 25, 94.5);
    Student student6 = new Student("Sal", "重庆大学", "男", 26, 97.5);
    Student student7 = new Student("Tanya", "四川大学", "女", 19, 98);
    Student student8 = new Student("Valentin", "香港大学", "男", 23, 91);
    Student student9 = new Student("Yvonne", "重庆大学", "女", 22, 94);

    students = Arrays.asList(student0, student1, student2, student3, student4, student5,
            student6, student7, student8, student9);
}
```

**(2) 流转集合**

可以将流中的元素转换为List或Set集合。

**toList()**

将流中的元素收集为一个List。

```java
@Test
public void test01() {
    List<String> names = students.stream()
            .map(Student::getName)
            .collect(Collectors.toList());
    System.out.println(names);
}
```

**toSet()**

```java
@Test
public void test02() {
    Set<String> schools = students.stream()
            .map(Student::getSchool)
            .collect(Collectors.toSet());
    System.out.println(schools);
}
```

**toCollection(Supplier sup)**

收集器默认使用的是无序的HashSet，若要指定使用有序的TreeSet，则可通过toCollection()方法指定。

```java
@Test
public void test03() {
    TreeSet<String> schools = students.stream().map(Student::getSchool)
            .collect(Collectors.toCollection(TreeSet::new));
    System.out.println(schools);
}
```

**(3) 分组**

**仅分组**

分组是指，按照流中指定的元素的属性值进行分组。

```java
@Test
public void test04() {
    Map<String, List<Student>> schoolGroup = students.stream()
            .collect(Collectors.groupingBy(Student::getSchool));
    MapUtils.verbosePrint(System.out, "学校", schoolGroup);
    System.out.println(schoolGroup.get("四川大学"));
}
```

**分组后统计**

统计是对元素个数的统计，所以无需指定属性用于统计。

```java
@Test
public void test05() {
    Map<String, Long> schoolCount = students.stream()
            .collect(Collectors.groupingBy(Student::getSchool, Collectors.counting()));
    System.out.println(schoolCount);
    System.out.println(schoolCount.get("四川大学"));
}
```

**(4) 布尔分块**

**仅分块**

布尔分块是按照指定断言的true与false结果进行分组，其只会划分为两组，且key只能是true或false。

分组实现：

```java
@Test
public void test06() {
    Map<String, List<Student>> genderGroup = students.stream()
            .collect(Collectors.groupingBy(Student::getGender));
    MapUtils.verbosePrint(System.out, "性别", genderGroup);
    System.out.println(genderGroup.get("男"));
}
```

分块实现：

```java
@Test
public void test07() {
    Map<Boolean, List<Student>> genderGroup = students.stream()
            .collect(Collectors.partitioningBy(s -> "男".equals(s.getGender())));
    MapUtils.verbosePrint(System.out, "性别", genderGroup);
    System.out.println(genderGroup.get(true));
}
```

```java
@Test
public void test08() {
    Map<Boolean, List<Student>> scoreGroup = students.stream()
        .collect(Collectors.partitioningBy(s -> s.getScore() > 95));
    MapUtils.verbosePrint(System.out, "划分成绩", scoreGroup);
    System.out.println(scoreGroup.get(true));
}
```

**分块后求平均值**

分块后，对两块内容按照指定的属性求平均值。

```java
@Test
public void test09() {
    Map<Boolean, Double> scoreGroupAvg = students.stream()
            .collect(Collectors.partitioningBy(s -> s.getScore() > 95,
                     Collectors.averagingDouble(Student::getScore)));

    System.out.println(scoreGroupAvg);
    System.out.println(scoreGroupAvg.get(true));
}
```

**(5) 获取汇总统计数据**

汇总统计数据指的是对流中某一数据汇总后所统计出的最大值、最小值、平均值等数据。该方法只能适用于数值型数据统计。

```java
@Test
public void test10() {
    DoubleSummaryStatistics scoreSummary = students.stream()
            .collect(Collectors.summarizingDouble(Student::getScore));
    System.out.println(scoreSummary);
    System.out.println("成绩个数：" + scoreSummary.getCount());
    System.out.println("最小成绩：" + scoreSummary.getMin());
}
```
