## 3. 方法引用

Lambda方法引用是指，借助内置函数式接口对一个类中的静态方法、实例方法、构造方法进行使用的方式。

### 3.1 准备工作

**(1) 定义Person类**

```java
public class Person {
    private String name;

    //无参构造器
    public Person() {}

    //有参构造器
    public Person(String name) {
        this.name = name;
    }

    //静态方法
    public static void sleeping(int hours) {
        System.out.println("人们每天需要睡眠" + hours + "小时。");
    }

    //实例方法
    public String play(int minutes) {
        return name + "已经玩了" + minutes + "分钟。";
    }

    //实例方法
    public void study(String course) {
        System.out.println(name + "正在学习" + course);
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

**(2) 实例方法隐含参数**

一个类的实例方法中都隐含一个参数this，该参数处于第一个参数位置，但在静态方法中却不存在该参数。这就是为什么在实例方法中可以使用this，而静态方法中不能使用this的原因。

```java
//实例方法
public String play(Person this, int minutes) {
    return name + "已经玩了" + minutes + "分钟。";
}

//实例方法
public void study(Person this, String course) {
    System.out.println(name + "正在学习" + course);
}
```

### 3.2 方法引用

**(1) Lambda静态方法引用**

```java
@Test
public void test01() {
    Consumer<Integer> consumer = Person::sleeping;
    consumer.accept(7);
}
```

**(2) Lambda实例方法引用**

```java
@Test
public void test02() {
    Person person = new Person("李石");
    Function<Integer, String> function = person::play;
    System.out.println(function.apply(15));
}

@Test
public void test03() {
    Person person = new Person("李思");
    Consumer<String> consumer = person::study;
    consumer.accept("WebFlux");
}
```

**(3) Lambda实例方法带隐含参数的引用**

```java
@Test
public void test04() {
    Person person = new Person("李思");
    BiFunction<Person, Integer, String> biFunction = Person::play;
    System.out.println(biFunction.apply(person, 7));
}

@Test
public void test05() {
    Person person = new Person("张珊");
    BiConsumer<Person, String> biConsumer = Person::study;
    biConsumer.accept(person, "WebFlux");
}
```

**(3) Lambda无参构造器引用**

```java
@Test
public void test06() {
    Supplier<Person> supplier = Person::new;
    System.out.println(supplier.get());
}
```

**(4) Lambda带参构造器引用**

```java
@Test
public void test07() {
    Function<String, Person> function = Person::new;
    System.out.println(function.apply("王舞"));
}
```