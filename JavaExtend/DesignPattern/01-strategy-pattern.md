## 策略模式（Strategy Pattern）

### 1. 定义

策略模式定义了算法族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化独立于使用算法的客户

### 2. 遇到的问题

使用继承时如果子类的行为有所不同，需要覆盖相关方法。

使用继承时可能存在子类不具有父类某些行为的情况，此时也需要进行覆盖。

当实现接口时，由于接口没有具体实现，所以无法做到代码的复用。

### 3. 设计原则

① 找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起。

② 针对接口编程，而不是针对实现编程。

③ 多用组合，少用继承

### 4. 类图

<left><img src="images\01-strategy-pattern.png"/></left>

### 5. 代码

**Duck**

```java
public abstract class Duck {
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;

    public Duck() {}

    public void setFlyBehavior(FlyBehavior fb) {
        flyBehavior = fb;
    }

    public void setQuackBehavior(QuackBehavior qb) {
        quackBehavior = qb;
    }

    abstract void display();

    public void performFly() {
        flyBehavior.fly();
    }

    public void performQuack() {
        quackBehavior.quack();
    }

    public void swim() {
        System.out.println("All ducks float, even decoys!");
    }
}
```

**FlyBehavior**

```java
public interface FlyBehavior {
    void fly();
}
```

**FlyNoWay**

```java
public class FlyNoWay implements FlyBehavior {
    public void fly() {
        System.out.println("I can't fly");
    }
}
```

**FlyRocketPowered**

```java
public class FlyRocketPowered implements FlyBehavior {
	public void fly() {
		System.out.println("I'm flying with a rocket");
	}
}
```

**FlyWithWings**

```java
public class FlyWithWings implements FlyBehavior {
	public void fly() {
		System.out.println("I'm flying!!");
	}
}
```

**QuackBehavior**

```java
public interface QuackBehavior {
    void quack();
}
```

**Quack**

```java
public class Quack implements QuackBehavior {
	public void quack() {
		System.out.println("Quack");
	}
}
```

**Squeak**

```java
public class Squeak implements QuackBehavior {
	public void quack() {
		System.out.println("Squeak");
	}
}
```

**FakeQuack**

```java
public class FakeQuack implements QuackBehavior {
   public void quack() {
      System.out.println("Qwak");
   }
}
```

**MuteQuack**

```java
public class MuteQuack implements QuackBehavior {
   public void quack() {
      System.out.println("<< Silence >>");
   }
}
```

**MallardDuck**

```java
public class MallardDuck extends Duck {

    public MallardDuck() {
        quackBehavior = new Quack();
        flyBehavior = new FlyWithWings();
    }

    public void display() {
        System.out.println("I'm a real Mallard duck");
    }
}
```

**RedHeadDuck**

```java
public class RedHeadDuck extends Duck {
 
   public RedHeadDuck() {
      flyBehavior = new FlyWithWings();
      quackBehavior = new Quack();
   }
 
   public void display() {
      System.out.println("I'm a real Red Headed duck");
   }
}
```

**RubberDuck**

```java
public class RubberDuck extends Duck {
 
   public RubberDuck() {
      flyBehavior = new FlyNoWay();
      //quackBehavior = new Squeak();
      quackBehavior = () -> System.out.println("Squeak");
   }
   
   public RubberDuck(FlyBehavior flyBehavior, QuackBehavior quackBehavior) {
      this.flyBehavior = flyBehavior;
      this.quackBehavior = quackBehavior; 
   }
 
   public void display() {
      System.out.println("I'm a rubber duckie");
   }
}
```

**ModelDuck**

```java
public class ModelDuck extends Duck {

    public ModelDuck() {
        flyBehavior = new FlyNoWay();
        quackBehavior = new Quack();
    }

    @Override
    public void display() {
        System.out.println("I'm a model duck");
    }
}
```

**DecoyDuck**

```java
public class DecoyDuck extends Duck {
   public DecoyDuck() {
      setFlyBehavior(new FlyNoWay());
      setQuackBehavior(new MuteQuack());
   }
   public void display() {
      System.out.println("I'm a duck Decoy");
   }
}
```

**MiniDuckSimulator**

```java
public class MiniDuckSimulator {

    public static void main(String[] args) {

        MallardDuck mallard = new MallardDuck();
        FlyBehavior cantFly = () -> System.out.println("I can't fly");
        QuackBehavior squeak = () -> System.out.println("Squeak");
        RubberDuck rubberDuckie = new RubberDuck(cantFly, squeak);
        DecoyDuck decoy = new DecoyDuck();

        Duck model = new ModelDuck();

        mallard.performQuack();
        rubberDuckie.performQuack();
        decoy.performQuack();

        model.performFly();
        model.setFlyBehavior(new FlyRocketPowered());
        model.performFly();
    }
}
```

**MiniDuckSimulator**

```java
public class MiniDuckSimulator {
 
   public static void main(String[] args) {
 
      Duck mallard = new MallardDuck();
      mallard.performQuack();
      mallard.performFly();
   
      Duck model = new ModelDuck();
      model.performFly();
      model.setFlyBehavior(new FlyRocketPowered());
      model.performFly();
   }
}
```
