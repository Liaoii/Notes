## 工厂方法模式（Factory Method）

### 1. 定义

工厂方法模式定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到子类。

### 2. 设计原则

① 依赖倒置原则，要依赖抽象，不要依赖具体类。

### 3. 结构

<left><img src="images\06-factory-method.png"/></left>

### 4. 代码

**Pizza**

```java
public abstract class Pizza {
    String name;
    String dough;
    String sauce;
    List<String> toppings = new ArrayList<>();

    public String getName() {
        return name;
    }

    public void prepare() {
        System.out.println("Preparing " + name);
    }

    public void bake() {
        System.out.println("Baking " + name);
    }

    public void cut() {
        System.out.println("Cutting " + name);
    }

    public void box() {
        System.out.println("Boxing " + name);
    }

    @Override
    public String toString() {
        StringBuilder display = new StringBuilder();
        display.append("---- ").append(name).append(" ----\n");
        display.append(dough).append("\n");
        display.append(sauce).append("\n");
        for (String topping : toppings) {
            display.append(topping).append("\n");
        }
        return display.toString();
    }
}
```

**PizzaStore**

```java
public abstract class PizzaStore {

    public Pizza orderPizza(String type) {
        Pizza pizza = createPizza(type);
        System.out.println("--- Making a " + pizza.getName() + " ---");
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }

    abstract Pizza createPizza(String item);
}
```

**ChicagoPizzaStore**

```java
public class ChicagoPizzaStore extends PizzaStore {

   Pizza createPizza(String item) {
           if (item.equals("cheese")) {
                  return new ChicagoStyleCheesePizza();
           } else if (item.equals("veggie")) {
                  return new ChicagoStyleVeggiePizza();
           } else if (item.equals("clam")) {
                  return new ChicagoStyleClamPizza();
           } else if (item.equals("pepperoni")) {
                  return new ChicagoStylePepperoniPizza();
           } else return null;
   }
}
```

**ChicagoStyleCheesePizza**

```java
public class ChicagoStyleCheesePizza extends Pizza {

    public ChicagoStyleCheesePizza() {
        name = "Chicago Style Deep Dish Cheese Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.add("Shredded Mozzarella Cheese");
    }

    public void cut() {
        System.out.println("Cutting the pizza into square slices");
    }
}
```

**ChicagoStylePepperoniPizza**

```java
public class ChicagoStylePepperoniPizza extends Pizza {
    public ChicagoStylePepperoniPizza() {
        name = "Chicago Style Pepperoni Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.add("Shredded Mozzarella Cheese");
        toppings.add("Black Olives");
        toppings.add("Spinach");
        toppings.add("Eggplant");
        toppings.add("Sliced Pepperoni");
    }

    public void cut() {
        System.out.println("Cutting the pizza into square slices");
    }
}
```

**ChicagoStyleClamPizza**

```java
public class ChicagoStyleClamPizza extends Pizza {
    public ChicagoStyleClamPizza() {
        name = "Chicago Style Clam Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.add("Shredded Mozzarella Cheese");
        toppings.add("Frozen Clams from Chesapeake Bay");
    }

    public void cut() {
        System.out.println("Cutting the pizza into square slices");
    }
}
```

**ChicagoStyleVeggiePizza**

```java
public class ChicagoStyleVeggiePizza extends Pizza {
    public ChicagoStyleVeggiePizza() {
        name = "Chicago Deep Dish Veggie Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";

        toppings.add("Shredded Mozzarella Cheese");
        toppings.add("Black Olives");
        toppings.add("Spinach");
        toppings.add("Eggplant");
    }

    public void cut() {
        System.out.println("Cutting the pizza into square slices");
    }
}
```

**NYPizzaStore**

```java
public class NYPizzaStore extends PizzaStore {

    Pizza createPizza(String item) {
        if (item.equals("cheese")) {
            return new NYStyleCheesePizza();
        } else if (item.equals("veggie")) {
            return new NYStyleVeggiePizza();
        } else if (item.equals("clam")) {
            return new NYStyleClamPizza();
        } else if (item.equals("pepperoni")) {
            return new NYStylePepperoniPizza();
        } else return null;
    }
}
```

**NYStyleCheesePizza**

```java
public class NYStyleCheesePizza extends Pizza {

   public NYStyleCheesePizza() { 
      name = "NY Style Sauce and Cheese Pizza";
      dough = "Thin Crust Dough";
      sauce = "Marinara Sauce";
 
      toppings.add("Grated Reggiano Cheese");
   }
}
```

**NYStylePepperoniPizza**

```java
public class NYStylePepperoniPizza extends Pizza {

   public NYStylePepperoniPizza() {
      name = "NY Style Pepperoni Pizza";
      dough = "Thin Crust Dough";
      sauce = "Marinara Sauce";
 
      toppings.add("Grated Reggiano Cheese");
      toppings.add("Sliced Pepperoni");
      toppings.add("Garlic");
      toppings.add("Onion");
      toppings.add("Mushrooms");
      toppings.add("Red Pepper");
   }
}
```

**NYStyleClamPizza**

```java
public class NYStyleClamPizza extends Pizza {

   public NYStyleClamPizza() {
      name = "NY Style Clam Pizza";
      dough = "Thin Crust Dough";
      sauce = "Marinara Sauce";
 
      toppings.add("Grated Reggiano Cheese");
      toppings.add("Fresh Clams from Long Island Sound");
   }
}
```

**NYStyleVeggiePizza**

```java
public class NYStyleVeggiePizza extends Pizza {

    public NYStyleVeggiePizza() {
        name = "NY Style Veggie Pizza";
        dough = "Thin Crust Dough";
        sauce = "Marinara Sauce";

        toppings.add("Grated Reggiano Cheese");
        toppings.add("Garlic");
        toppings.add("Onion");
        toppings.add("Mushrooms");
        toppings.add("Red Pepper");
    }
}
```

