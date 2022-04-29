## 模板方法模式（Temple Method）

### 1. 定义

模板方法模式在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以在不改变算法结构的情况下，重新定义算法中的某些步骤。

### 2. 原则

好莱坞原则：别调用我们，我们会调用你。

### 3. 类图

<left><img src="images\11-template-method.png"/></left>

### 4. 代码

**CaffeineBeverage**

```java
public abstract class CaffeineBeverage {
  
   final void prepareRecipe() {
      boilWater();
      brew();
      pourInCup();
      addCondiments();
   }
 
   abstract void brew();
  
   abstract void addCondiments();
 
   void boilWater() {
      System.out.println("Boiling water");
   }
  
   void pourInCup() {
      System.out.println("Pouring into cup");
   }
}
```

**Coffee**

```java
public class Coffee extends CaffeineBeverage {
    public void brew() {
        System.out.println("Dripping Coffee through filter");
    }

    public void addCondiments() {
        System.out.println("Adding Sugar and Milk");
    }
}
```

**Tea**

```java
public class Tea extends CaffeineBeverage {
    public void brew() {
        System.out.println("Steeping the tea");
    }

    public void addCondiments() {
        System.out.println("Adding Lemon");
    }
}
```

**BeverageTestDrive**

```java
public class BeverageTestDrive {
   public static void main(String[] args) {
 
      Tea tea = new Tea();
      Coffee coffee = new Coffee();
 
      System.out.println("\nMaking tea...");
      tea.prepareRecipe();
 
      System.out.println("\nMaking coffee...");
      coffee.prepareRecipe();

   }
}
```

**使用钩子函数**

**CaffeineBeverageWithHook**

```java
public abstract class CaffeineBeverageWithHook {

    final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {
            addCondiments();
        }
    }

    abstract void brew();

    abstract void addCondiments();

    void boilWater() {
        System.out.println("Boiling water");
    }

    void pourInCup() {
        System.out.println("Pouring into cup");
    }

    boolean customerWantsCondiments() {
        return true;
    }
}
```

**CoffeeWithHook**

```java
public class CoffeeWithHook extends CaffeineBeverageWithHook {

    public void brew() {
        System.out.println("Dripping Coffee through filter");
    }

    public void addCondiments() {
        System.out.println("Adding Sugar and Milk");
    }

    public boolean customerWantsCondiments() {

        String answer = getUserInput();

        if (answer.toLowerCase().startsWith("y")) {
            return true;
        } else {
            return false;
        }
    }

    private String getUserInput() {
        String answer = null;

        System.out.print("Would you like milk and sugar with your coffee (y/n)? ");

        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        try {
            answer = in.readLine();
        } catch (IOException ioe) {
            System.err.println("IO error trying to read your answer");
        }
        if (answer == null) {
            return "no";
        }
        return answer;
    }
}
```

**TeaWithHook**

```java
public class TeaWithHook extends CaffeineBeverageWithHook {

    public void brew() {
        System.out.println("Steeping the tea");
    }

    public void addCondiments() {
        System.out.println("Adding Lemon");
    }

    public boolean customerWantsCondiments() {

        String answer = getUserInput();

        return answer.toLowerCase().startsWith("y");
    }

    private String getUserInput() {
        // get the user's response
        String answer = null;

        System.out.print("Would you like lemon with your tea (y/n)? ");

        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        try {
            answer = in.readLine();
        } catch (IOException ioe) {
            System.err.println("IO error trying to read your answer");
        }
        if (answer == null) {
            return "no";
        }
        return answer;
    }
}
```

**BeverageTestDrive**

```java
public class BeverageTestDrive {
   public static void main(String[] args) {
      TeaWithHook teaHook = new TeaWithHook();
      CoffeeWithHook coffeeHook = new CoffeeWithHook();

      System.out.println("\nMaking tea...");
      teaHook.prepareRecipe();

      System.out.println("\nMaking coffee...");
      coffeeHook.prepareRecipe();
   }
}
```