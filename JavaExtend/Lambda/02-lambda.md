## 1. Lambda表达式

Lambda是函数接口的一种实现方式，用于代替匿名内部类。

### 1.1 概念

函数式接口（Functional Interface），也称为功能性接口。简单来说，接口中可以包含多个方法，但仅能有一个自己的抽象方法，即接口的默认方法和静态方法并不影响一个接口成为函数式接口。

### 1.2 用法

**(1) 无参数无返回值**

```java
@FunctionalInterface
interface Some1 {
    void doSome();
}

public class Test01 {
    @Test
    public void test01() {
        Some1 some = new Some1() {
            @Override
            public void doSome() {
                System.out.println("使用匿名内部类实现");
            }
        };
        some.doSome();
    }

    @Test
    public void test02() {
        Some1 some = () -> {
            System.out.println("Lambda实现");
        };
        some.doSome();
    }

    @Test
    public void test03() {
        Some1 some = () -> System.out.println("Lambda实现");
        some.doSome();
    }
}
```

**(2) 无参数有返回值**

```java
@FunctionalInterface
interface Some2 {
    String doSome();
}

public class Test02 {

    @Test
    public void test01() {
        Some2 some2 = new Some2() {
            @Override
            public String doSome() {
                return "使用匿名内部类实现";
            }
        };
        System.out.println(some2.doSome());
    }

    @Test
    public void test02() {
        Some2 some2 = () -> {
            System.out.println("使用Lambda实现");
            return "Lambda";
        };
        System.out.println(some2.doSome());
    }

    @Test
    public void test03() {
        Some2 some2 = () -> "Lambda";
        System.out.println(some2.doSome());
    }
}
```

**(3) 有参数有返回值**

```java
@FunctionalInterface
interface Some3 {
    String doSome(String a, int b);
}

public class Test03 {
    @Test
    public void test01() {
        Some3 some3 = new Some3() {
            @Override
            public String doSome(String a, int b) {
                return a + b;
            }
        };
        System.out.println(some3.doSome("Hello, ", 2022));
    }

    @Test
    public void test02() {
        Some3 some3 = (str, num) -> str + num;
        System.out.println(some3.doSome("Hello, ", 2022));
    }
}
```

### 1.3 默认方法

```java
@FunctionalInterface
interface Some4 {
    String doSome(String a, int b);

    default void doOther(String a, int b) {
        System.out.println("执行默认方法: " + a + b);
    }
}

public class Test04 {
    @Test
    public void test01() {
        Some4 some4 = (str, num) -> str + num;
        System.out.println(some4.doSome("Hello, ", 2022));
        some4.doOther("Hello, ", 2022);
    }
}
```
