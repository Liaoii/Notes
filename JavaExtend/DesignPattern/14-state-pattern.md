## 状态模式（State）

### 1. 定义

状态模式允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

### 2. 类图



### 3. 代码

**State**

```java
public interface State {
 
   void insertQuarter();
   void ejectQuarter();
   void turnCrank();
   void dispense();
   
   void refill();
}
```

**NoQuarterState**

```java
public class NoQuarterState implements State {
    GumballMachine gumballMachine;

    public NoQuarterState(GumballMachine gumballMachine) {
        this.gumballMachine = gumballMachine;
    }

    public void insertQuarter() {
        System.out.println("You inserted a quarter");
        gumballMachine.setState(gumballMachine.getHasQuarterState());
    }

    public void ejectQuarter() {
        System.out.println("You haven't inserted a quarter");
    }

    public void turnCrank() {
        System.out.println("You turned, but there's no quarter");
    }

    public void dispense() {
        System.out.println("You need to pay first");
    }

    public void refill() {}

    public String toString() {
        return "waiting for quarter";
    }
}
```

**HasQuarterState**

```java
import java.util.Random;

public class HasQuarterState implements State {
   Random randomWinner = new Random(System.currentTimeMillis());
   GumballMachine gumballMachine;
 
   public HasQuarterState(GumballMachine gumballMachine) {
      this.gumballMachine = gumballMachine;
   }
  
   public void insertQuarter() {
      System.out.println("You can't insert another quarter");
   }
 
   public void ejectQuarter() {
      System.out.println("Quarter returned");
      gumballMachine.setState(gumballMachine.getNoQuarterState());
   }
 
   public void turnCrank() {
      System.out.println("You turned...");
      int winner = randomWinner.nextInt(10);
      if ((winner == 0) && (gumballMachine.getCount() > 1)) {
         gumballMachine.setState(gumballMachine.getWinnerState());
      } else {
         gumballMachine.setState(gumballMachine.getSoldState());
      }
   }

    public void dispense() {
        System.out.println("No gumball dispensed");
    }
    
    public void refill() { }
 
   public String toString() {
      return "waiting for turn of crank";
   }
}
```

**SoldOutState**

```java
public class SoldOutState implements State {
    GumballMachine gumballMachine;
 
    public SoldOutState(GumballMachine gumballMachine) {
        this.gumballMachine = gumballMachine;
    }
 
   public void insertQuarter() {
      System.out.println("You can't insert a quarter, the machine is sold out");
   }
 
   public void ejectQuarter() {
      System.out.println("You can't eject, you haven't inserted a quarter yet");
   }
 
   public void turnCrank() {
      System.out.println("You turned, but there are no gumballs");
   }
 
   public void dispense() {
      System.out.println("No gumball dispensed");
   }
   
   public void refill() { 
      gumballMachine.setState(gumballMachine.getNoQuarterState());
   }
 
   public String toString() {
      return "sold out";
   }
}
```

**SoldState**

```java
public class SoldState implements State {
    GumballMachine gumballMachine;
 
    public SoldState(GumballMachine gumballMachine) {
        this.gumballMachine = gumballMachine;
    }
       
   public void insertQuarter() {
      System.out.println("Please wait, we're already giving you a gumball");
   }
 
   public void ejectQuarter() {
      System.out.println("Sorry, you already turned the crank");
   }
 
   public void turnCrank() {
      System.out.println("Turning twice doesn't get you another gumball!");
   }
 
   public void dispense() {
      gumballMachine.releaseBall();
      if (gumballMachine.getCount() > 0) {
         gumballMachine.setState(gumballMachine.getNoQuarterState());
      } else {
         System.out.println("Oops, out of gumballs!");
         gumballMachine.setState(gumballMachine.getSoldOutState());
      }
   }
   
   public void refill() { }
 
   public String toString() {
      return "dispensing a gumball";
   }
}
```

**WinnerState**

```java
public class WinnerState implements State {
    GumballMachine gumballMachine;
 
    public WinnerState(GumballMachine gumballMachine) {
        this.gumballMachine = gumballMachine;
    }
 
   public void insertQuarter() {
      System.out.println("Please wait, we're already giving you a Gumball");
   }
 
   public void ejectQuarter() {
      System.out.println("Please wait, we're already giving you a Gumball");
   }
 
   public void turnCrank() {
      System.out.println("Turning again doesn't get you another gumball!");
   }
 
   public void dispense() {
      gumballMachine.releaseBall();
      if (gumballMachine.getCount() == 0) {
         gumballMachine.setState(gumballMachine.getSoldOutState());
      } else {
         gumballMachine.releaseBall();
         System.out.println("YOU'RE A WINNER! You got two gumballs for your quarter");
         if (gumballMachine.getCount() > 0) {
            gumballMachine.setState(gumballMachine.getNoQuarterState());
         } else {
               System.out.println("Oops, out of gumballs!");
            gumballMachine.setState(gumballMachine.getSoldOutState());
         }
      }
   }
 
   public void refill() { }
   
   public String toString() {
      return "despensing two gumballs for your quarter, because YOU'RE A WINNER!";
   }
}
```

**GumballMachine**

```java
public class GumballMachine {
 
   State soldOutState;
   State noQuarterState;
   State hasQuarterState;
   State soldState;
   State winnerState;
 
   State state = soldOutState;
   int count = 0;
 
   public GumballMachine(int numberGumballs) {
      soldOutState = new SoldOutState(this);
      noQuarterState = new NoQuarterState(this);
      hasQuarterState = new HasQuarterState(this);
      soldState = new SoldState(this);
      winnerState = new WinnerState(this);

      this.count = numberGumballs;
      if (numberGumballs > 0) {
         state = noQuarterState;
      } 
   }
 
   public void insertQuarter() {
      state.insertQuarter();
   }
 
   public void ejectQuarter() {
      state.ejectQuarter();
   }
 
   public void turnCrank() {
      state.turnCrank();
      state.dispense();
   }

   void setState(State state) {
      this.state = state;
   }
 
   void releaseBall() {
      System.out.println("A gumball comes rolling out the slot...");
      if (count > 0) {
         count = count - 1;
      }
   }
 
   int getCount() {
      return count;
   }
 
   void refill(int count) {
      this.count += count;
      System.out.println("The gumball machine was just refilled; its new count is: " + this.count);
      state.refill();
   }

    public State getState() {
        return state;
    }

    public State getSoldOutState() {
        return soldOutState;
    }

    public State getNoQuarterState() {
        return noQuarterState;
    }

    public State getHasQuarterState() {
        return hasQuarterState;
    }

    public State getSoldState() {
        return soldState;
    }

    public State getWinnerState() {
        return winnerState;
    }
 
   public String toString() {
      StringBuffer result = new StringBuffer();
      result.append("\nMighty Gumball, Inc.");
      result.append("\nJava-enabled Standing Gumball Model #2004");
      result.append("\nInventory: " + count + " gumball");
      if (count != 1) {
         result.append("s");
      }
      result.append("\n");
      result.append("Machine is " + state + "\n");
      return result.toString();
   }
}
```

**GumballMachineTestDrive**

```java
public class GumballMachineTestDrive {

    public static void main(String[] args) {
        GumballMachine gumballMachine = new GumballMachine(10);

        System.out.println(gumballMachine);

        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();
        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();

        System.out.println(gumballMachine);

        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();
        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();

        System.out.println(gumballMachine);

        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();
        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();

        System.out.println(gumballMachine);

        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();
        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();

        System.out.println(gumballMachine);

        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();
        gumballMachine.insertQuarter();
        gumballMachine.turnCrank();

        System.out.println(gumballMachine);
    }
}
```
