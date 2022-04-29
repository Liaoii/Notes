## 适配器模式（Adapter）

### 1. 定义

适配器模式将一个类的接口，转换成客户期望的另一个接口。适配器让原本接口不兼容的类可以合作无间。

### 2. 类图

<left><img src="images\09-adapter.png"/></left>

### 3. 代码

**Duck**

```java
public interface Duck {
   void quack();
   void fly();
}
```

**MallardDuck**

```java
public class MallardDuck implements Duck {
   public void quack() {
      System.out.println("Quack");
   }
 
   public void fly() {
      System.out.println("I'm flying");
   }
}
```

**Turkey**

```java
public interface Turkey {
   void gobble();
   void fly();
}
```

**WildTurkey**

```java
public class WildTurkey implements Turkey {
   public void gobble() {
      System.out.println("Gobble gobble");
   }
 
   public void fly() {
      System.out.println("I'm flying a short distance");
   }
}
```

**TurkeyAdapter**

```java
public class TurkeyAdapter implements Duck {
    Turkey turkey;

    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }

    public void quack() {
        turkey.gobble();
    }

    public void fly() {
        for (int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}
```

**DuckTestDrive**

```java
public class DuckTestDrive {
    public static void main(String[] args) {
        Duck duck = new MallardDuck();

        Turkey turkey = new WildTurkey();
        Duck turkeyAdapter = new TurkeyAdapter(turkey);

        System.out.println("The Turkey says...");
        turkey.gobble();
        turkey.fly();

        System.out.println("\nThe Duck says...");
        testDuck(duck);

        System.out.println("\nThe TurkeyAdapter says...");
        testDuck(turkeyAdapter);
    }

    static void testDuck(Duck duck) {
        duck.quack();
        duck.fly();
    }
}
```