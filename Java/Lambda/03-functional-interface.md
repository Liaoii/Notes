## 2. 函数式接口编程

JDK8中根据接口方法参数与返回值的不同，定义了若干内置的函数接口，在使用Lambda表达式时，无需再自己定义接口，可以根据情况选择对应的内置接口进行使用。

### 2.1 Predicate接口

**(1) test()**

该方法会将参数值应用于接口Lambda表达式，测试该值的断言。

```java
@Test
public void test01() {
    Predicate<Integer> predicate = i -> i > 5;
    System.out.println(predicate.test(9)); //true
    System.out.println(predicate.test(4)); //false
}
```

**(2) 默认方法**

该接口提供了三个默认方法add、or、negate，它们用于对两个断言结果再次进行运算。

```java
@Test
public void test02() {
    Predicate<Integer> gt7 = i -> i > 7;
    Predicate<Integer> lt3 = i -> i < 3;
    System.out.println(gt7.and(lt3).test(2)); //false
    System.out.println(gt7.or(lt3).test(2)); //true
    System.out.println(gt7.negate().test(2)); //true
}
```

**(3) 静态方法**

静态方法isEqual，该方法的参数将作为比较对象，与test方法的参数进行相等性比较。

```java
@Test
public void test03() {
    System.out.println(Predicate.isEqual("Hello").test("hello")); //false
    System.out.println(Predicate.isEqual("Hello").test("Hello")); //true
}
```

**(4) IntPredicate与DoublePredicate**

IntPredicate、DoublePredicate与Predicate接口没有任何关系，是另外一种全新的接口，仅仅是为了使用方便。

```java
@Test
public void test04() {
    IntPredicate intPredicate = i -> i > 5;
    DoublePredicate doublePredicate = d -> d > 5;
    System.out.println(intPredicate.test(6)); //true
    System.out.println(intPredicate.test(2)); //false
    System.out.println(doublePredicate.test(6)); //true
    System.out.println(doublePredicate.test(2)); //false
}
```

### 2.2 Consumer接口

Consumer消费者，只有一个输入没有输出的函数接口，该接口有一个抽象方法accept与一个默认方法andThen。

**(1) accept()**

```java
@Test
public void test01() {
    Consumer<String> consumer = str -> System.out.println("Hello, " + str);
    consumer.accept("Tom");
}
```

**(2) 默认方法andThen()**

andThen，该方法将连接两个Consumer表达式，先执行前面的，再执行后面的。

```java
@Test
public void test02() {
    Consumer<Integer> consumer1 = i -> System.out.println(i * 2);
    Consumer<Integer> consumer2 = i -> System.out.println(i * i);
    consumer1.andThen(consumer2).accept(5);
}
```

### 2.3 Supplier接口

Supplier提供者，没有输入但有输出的函数接口，该接口只有一个抽象方法get，用于获取函数接口方法的返回值。

```java
@Test
public void test01() {
    Supplier<String> supplier = () -> "Lambda";
    System.out.println(supplier.get());
}
```

### 2.4 Function接口

只有一个输入，且有一个输出的函数接口，该接口有一个抽象方法apply，有两个默认方法andThen与compose，以及一个静态方法identity。

**(1) apply()**

```java
@Test
public void test01() {
    Function<Integer, String> function = num -> "我爱你, " + num;
    System.out.println(function.apply(2022));
}
```

**(2) 默认方法**

andThen是先执行前面的接口Lambda表达式，再将前面的执行结果作为后面Lambda的输入参数执行后面的表达式。

compose与andThen的顺序相反。

```java
@Test
public void test02() {
    Function<Integer, Integer> function1 = x -> x * 2;
    Function<Integer, Integer> function2 = x -> x * x;
    //先将5作为function1的参数，计算结果为10
    //再将10作为function2的参数再进行计算
    System.out.println(function1.andThen(function2).apply(5)); //100
    //先将5作为function2的参数，计算结果为25
    //再将25作为function1的参数再进行计算
    System.out.println(function1.compose(function2).apply(5)); //50
}
```

**(3) 静态方法**

identity的返回结果为该Function的输入参数值。

```java
@Test
public void test03() {
    System.out.println(Function.identity().apply(5)); //5
    System.out.println(Function.identity().apply(5 * 5)); //25
}
```

### 2.5 UnaryOperator接口

**(1) apply()**

```java
@Test
public void test01() {
    UnaryOperator<String> unaryOperator = str -> "我爱你, " + str;
    System.out.println(unaryOperator.apply("北京"));
}
```

**(2) 默认方法**

```java
@Test
public void test02() {
    UnaryOperator<Integer> unaryOperator1 = x -> x * 2;
    UnaryOperator<Integer> unaryOperator2 = x -> x * x;
    //先将5作为unaryOperator1的参数，计算结果为10
    //再将10作为unaryOperator2的参数再进行计算
    System.out.println(unaryOperator1.andThen(unaryOperator2).apply(5)); //100
    //先将5作为unaryOperator2的参数，计算结果为25
    //再将25作为unaryOperator1的参数再进行计算
    System.out.println(unaryOperator1.compose(unaryOperator2).apply(5)); //50
}
```

**(3) 静态方法**

```java
@Test
public void test03() {
    System.out.println(UnaryOperator.identity().apply(5)); //5
    System.out.println(UnaryOperator.identity().apply(5 * 5)); //25
}
```

### 2.6 BiFunction接口

BiFunction有两个输入与一个输出，BiFunction接口与Function接口没有任何关系。其有一个抽象方法apply，与一个默认方法andThen。

**(1) apply()**

```java
@Test
public void test01() {
    BiFunction<Integer, Integer, String> biFunction = (x, y) -> x + " : " + y;
    System.out.println(biFunction.apply(100, 25));
}
```

**(2) 默认方法**

```java
@Test
public void test02() {
    BiFunction<Integer, Integer, String> biFunction = (x, y) -> x + " : " + y;
    UnaryOperator<String> unaryOperator = str -> "键值对为: " + str;
    System.out.println(biFunction.andThen(unaryOperator).apply(100, 25));
}

@Test
public void test03() {
    BiFunction<Integer, Integer, Integer> biFunction = (x, y) -> x + y;
    Function<Integer, String> function = num -> "结果为: " + num;
    System.out.println(biFunction.andThen(function).apply(10, 30));
}
```

### 2.7 BinaryOperator接口

**(1) apply()**

```java
@Test
public void test01() {
    BinaryOperator<Integer> binaryOperator = (x, y) -> x * y;
    System.out.println(binaryOperator.apply(5, 8));
}
```

**(2) 默认方法**

```java
@Test
public void test02() {
    BinaryOperator<Integer> binaryOperator = (x, y) -> x * y;
    Function<Integer, String> function = num -> "结果为: " + num;
    System.out.println(binaryOperator.andThen(function).apply(9, 9));
}
```

**(3) 静态方法**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
class Student {
    private String name;
    private int age;
}

class StudentComparator implements Comparator<Student> {
    @Override
    public int compare(Student stu1, Student stu2) {
        if (stu1.getAge() > stu2.getAge()) {
            return 1;
        } else if (stu1.getAge() < stu2.getAge()) {
            return -1;
        }
        return 0;
    }
}
```

```java
@Test
public void test03() {
    Student stu1 = new Student("张珊", 23);
    Student stu2 = new Student("李斯", 24);
    StudentComparator studentComparator = new StudentComparator();
    Student minStu = BinaryOperator.minBy(studentComparator).apply(stu1, stu2);
    Student maxStu = BinaryOperator.maxBy(studentComparator).apply(stu1, stu2);
    System.out.println(minStu);
    System.out.println(maxStu);
}
```
