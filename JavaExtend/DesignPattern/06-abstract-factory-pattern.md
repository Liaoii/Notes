## 抽象工厂模式（Abstract Factory）

### 1. 定义

抽象工厂模式提供了一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。

### 2. 结构

### 3. 代码

#### (1) 原料类

**Dough**

```java
public interface Dough {
   String toString();
}
```

**ThinCrustDough**

```java
public class ThinCrustDough implements Dough {
   public String toString() {
      return "Thin Crust Dough";
   }
}
```

**ThickCrustDough**

```java
public class ThickCrustDough implements Dough {
   public String toString() {
      return "ThickCrust style extra thick crust dough";
   }
}
```

**Sauce**

```java
public interface Sauce {
   String toString();
}
```

**MarinaraSauce**

```java
public class MarinaraSauce implements Sauce {
   public String toString() {
      return "Marinara Sauce";
   }
}
```

**PlumTomatoSauce**

```java
public class PlumTomatoSauce implements Sauce {
   public String toString() {
      return "Tomato sauce with plum tomatoes";
   }
}
```

**Cheese**

```java
public interface Cheese {
   String toString();
}
```

**ReggianoCheese**

```java
public class ReggianoCheese implements Cheese {
    public String toString() {
        return "Reggiano Cheese";
    }
}
```

**MozzarellaCheese**

```java
public class MozzarellaCheese implements Cheese {
   public String toString() {
      return "Shredded Mozzarella";
   }
}
```

**Clams**

```java
public interface Clams {
   String toString();
}
```

**FreshClams**

```java
public class FreshClams implements Clams {
   public String toString() {
      return "Fresh Clams from Long Island Sound";
   }
}
```

**FrozenClams**

```java
public class FrozenClams implements Clams {
   public String toString() {
      return "Frozen Clams from Chesapeake Bay";
   }
}
```

**Veggies**

```java
public interface Veggies {
   String toString();
}
```

**Garlic**

```java
public class Garlic implements Veggies {
   public String toString() {
      return "Garlic";
   }
}
```

**Onion**

```java
public class Onion implements Veggies {
   public String toString() {
      return "Onion";
   }
}
```

**Mushroom**

```java
public class Mushroom implements Veggies {
   public String toString() {
      return "Mushrooms";
   }
}
```

**RedPepper**

```java
public class RedPepper implements Veggies {
   public String toString() {
      return "Red Pepper";
   }
}
```

**BlackOlives**

```java
public class BlackOlives implements Veggies {
   public String toString() {
      return "Black Olives";
   }
}
```

**Spinach**

```java
public class Spinach implements Veggies {
   public String toString() {
      return "Spinach";
   }
}
```

**Eggplant**

```java
public class Eggplant implements Veggies {
   public String toString() {
      return "Eggplant";
   }
}
```

**Pepperoni**

```java
public interface Pepperoni {
   String toString();
}
```

**SlicedPepperoni**

```java
public class SlicedPepperoni implements Pepperoni {
   public String toString() {
      return "Sliced Pepperoni";
   }
}
```

#### (2) 工厂类

**PizzaIngredientFactory**

```java
public interface PizzaIngredientFactory {
 
   Dough createDough();
   Sauce createSauce();
   Cheese createCheese();
   Veggies[] createVeggies();
   Pepperoni createPepperoni();
   Clams createClam();
 
}
```

**NYPizzaIngredientFactory**

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {

    public Dough createDough() {
        return new ThinCrustDough();
    }

    public Sauce createSauce() {
        return new MarinaraSauce();
    }

    public Cheese createCheese() {
        return new ReggianoCheese();
    }

    public Veggies[] createVeggies() {
        Veggies veggies[] = {new Garlic(), new Onion(), new Mushroom(), new RedPepper()};
        return veggies;
    }

    public Pepperoni createPepperoni() {
        return new SlicedPepperoni();
    }

    public Clams createClam() {
        return new FreshClams();
    }
}
```

**ChicagoPizzaIngredientFactory**

```java
public class ChicagoPizzaIngredientFactory implements PizzaIngredientFactory {

    public Dough createDough() {
        return new ThickCrustDough();
    }

    public Sauce createSauce() {
        return new PlumTomatoSauce();
    }

    public Cheese createCheese() {
        return new MozzarellaCheese();
    }

    public Veggies[] createVeggies() {
        Veggies veggies[] = {new BlackOlives(), new Spinach(), new Eggplant()};
        return veggies;
    }

    public Pepperoni createPepperoni() {
        return new SlicedPepperoni();
    }

    public Clams createClam() {
        return new FrozenClams();
    }
}
```

#### (3) Pizza类

**Pizza**

```java
public abstract class Pizza {
    String name;

    Dough dough;
    Sauce sauce;
    Veggies veggies[];
    Cheese cheese;
    Pepperoni pepperoni;
    Clams clam;

    abstract void prepare();

    void bake() {
        System.out.println("Bake for 25 minutes at 350");
    }

    void cut() {
        System.out.println("Cutting the pizza into diagonal slices");
    }

    void box() {
        System.out.println("Place pizza in official PizzaStore box");
    }

    void setName(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }

    public String toString() {
        StringBuffer result = new StringBuffer();
        result.append("---- " + name + " ----\n");
        if (dough != null) {
            result.append(dough);
            result.append("\n");
        }
        if (sauce != null) {
            result.append(sauce);
            result.append("\n");
        }
        if (cheese != null) {
            result.append(cheese);
            result.append("\n");
        }
        if (veggies != null) {
            for (int i = 0; i < veggies.length; i++) {
                result.append(veggies[i]);
                if (i < veggies.length - 1) {
                    result.append(", ");
                }
            }
            result.append("\n");
        }
        if (clam != null) {
            result.append(clam);
            result.append("\n");
        }
        if (pepperoni != null) {
            result.append(pepperoni);
            result.append("\n");
        }
        return result.toString();
    }
}
```

**CheesePizza**

```java
public class CheesePizza extends Pizza {
   PizzaIngredientFactory ingredientFactory;
 
   public CheesePizza(PizzaIngredientFactory ingredientFactory) {
      this.ingredientFactory = ingredientFactory;
   }
 
   void prepare() {
      System.out.println("Preparing " + name);
      dough = ingredientFactory.createDough();
      sauce = ingredientFactory.createSauce();
      cheese = ingredientFactory.createCheese();
   }
}
```

**ClamPizza**

```java
public class ClamPizza extends Pizza {
   PizzaIngredientFactory ingredientFactory;
 
   public ClamPizza(PizzaIngredientFactory ingredientFactory) {
      this.ingredientFactory = ingredientFactory;
   }
 
   void prepare() {
      System.out.println("Preparing " + name);
      dough = ingredientFactory.createDough();
      sauce = ingredientFactory.createSauce();
      cheese = ingredientFactory.createCheese();
      clam = ingredientFactory.createClam();
   }
}
```

**PepperoniPizza**

```java
public class PepperoniPizza extends Pizza {
   PizzaIngredientFactory ingredientFactory;
 
   public PepperoniPizza(PizzaIngredientFactory ingredientFactory) {
      this.ingredientFactory = ingredientFactory;
   }
 
   void prepare() {
      System.out.println("Preparing " + name);
      dough = ingredientFactory.createDough();
      sauce = ingredientFactory.createSauce();
      cheese = ingredientFactory.createCheese();
      veggies = ingredientFactory.createVeggies();
      pepperoni = ingredientFactory.createPepperoni();
   }
}
```

**VeggiePizza**

```java
public class VeggiePizza extends Pizza {
   PizzaIngredientFactory ingredientFactory;
 
   public VeggiePizza(PizzaIngredientFactory ingredientFactory) {
      this.ingredientFactory = ingredientFactory;
   }
 
   void prepare() {
      System.out.println("Preparing " + name);
      dough = ingredientFactory.createDough();
      sauce = ingredientFactory.createSauce();
      cheese = ingredientFactory.createCheese();
      veggies = ingredientFactory.createVeggies();
   }
}
```

#### （4）Store类

**PizzaStore**

```java
public abstract class PizzaStore {
 
   protected abstract Pizza createPizza(String item);
 
   public Pizza orderPizza(String type) {
      Pizza pizza = createPizza(type);
      System.out.println("--- Making a " + pizza.getName() + " ---");
      pizza.prepare();
      pizza.bake();
      pizza.cut();
      pizza.box();
      return pizza;
   }
}
```

**NYPizzaStore**

```java
public class NYPizzaStore extends PizzaStore {

    protected Pizza createPizza(String item) {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new NYPizzaIngredientFactory();

        if (item.equals("cheese")) {

            pizza = new CheesePizza(ingredientFactory);
            pizza.setName("New York Style Cheese Pizza");

        } else if (item.equals("veggie")) {

            pizza = new VeggiePizza(ingredientFactory);
            pizza.setName("New York Style Veggie Pizza");

        } else if (item.equals("clam")) {

            pizza = new ClamPizza(ingredientFactory);
            pizza.setName("New York Style Clam Pizza");

        } else if (item.equals("pepperoni")) {

            pizza = new PepperoniPizza(ingredientFactory);
            pizza.setName("New York Style Pepperoni Pizza");

        }
        return pizza;
    }
}
```

**ChicagoPizzaStore**

```java
public class ChicagoPizzaStore extends PizzaStore {

    protected Pizza createPizza(String item) {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new ChicagoPizzaIngredientFactory();

        if (item.equals("cheese")) {

            pizza = new CheesePizza(ingredientFactory);
            pizza.setName("Chicago Style Cheese Pizza");

        } else if (item.equals("veggie")) {

            pizza = new VeggiePizza(ingredientFactory);
            pizza.setName("Chicago Style Veggie Pizza");

        } else if (item.equals("clam")) {

            pizza = new ClamPizza(ingredientFactory);
            pizza.setName("Chicago Style Clam Pizza");

        } else if (item.equals("pepperoni")) {

            pizza = new PepperoniPizza(ingredientFactory);
            pizza.setName("Chicago Style Pepperoni Pizza");

        }
        return pizza;
    }
}
```

#### (5) 测试类

**PizzaTestDrive**

```java
public class PizzaTestDrive {

    public static void main(String[] args) {
        PizzaStore nyStore = new NYPizzaStore();
        PizzaStore chicagoStore = new ChicagoPizzaStore();

        Pizza pizza = nyStore.orderPizza("cheese");
        System.out.println("Ethan ordered a " + pizza + "\n");

        pizza = chicagoStore.orderPizza("cheese");
        System.out.println("Joel ordered a " + pizza + "\n");

        pizza = nyStore.orderPizza("clam");
        System.out.println("Ethan ordered a " + pizza + "\n");

        pizza = chicagoStore.orderPizza("clam");
        System.out.println("Joel ordered a " + pizza + "\n");

        pizza = nyStore.orderPizza("pepperoni");
        System.out.println("Ethan ordered a " + pizza + "\n");

        pizza = chicagoStore.orderPizza("pepperoni");
        System.out.println("Joel ordered a " + pizza + "\n");

        pizza = nyStore.orderPizza("veggie");
        System.out.println("Ethan ordered a " + pizza + "\n");

        pizza = chicagoStore.orderPizza("veggie");
        System.out.println("Joel ordered a " + pizza + "\n");
    }
}
```