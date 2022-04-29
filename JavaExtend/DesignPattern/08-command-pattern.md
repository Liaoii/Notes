## 命令模式（Command）

### 1. 定义

命令模式将“请求”封装成对象，以便使用不同的请求、队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。

### 2. 类图

<left><img src="images\08-command.png"/></left>

### 3. 代码

**Command**

```java
public interface Command {
   void execute();
}
```

**NoCommand**

```java
public class NoCommand implements Command {
   public void execute() { }
}
```

**Light**

```java
public class Light {
   String location = "";

   public Light(String location) {
      this.location = location;
   }

   public void on() {
      System.out.println(location + " light is on");
   }

   public void off() {
      System.out.println(location + " light is off");
   }
}
```

**LightOnCommand**

```java
public class LightOnCommand implements Command {
   Light light;

   public LightOnCommand(Light light) {
      this.light = light;
   }

   public void execute() {
      light.on();
   }
}
```

**LightOffCommand**

```java
public class LightOffCommand implements Command {
   Light light;
 
   public LightOffCommand(Light light) {
      this.light = light;
   }
 
   public void execute() {
      light.off();
   }
}
```

**CeilingFan**

```java
public class CeilingFan {
   String location = "";
   int level;
   public static final int HIGH = 2;
   public static final int MEDIUM = 1;
   public static final int LOW = 0;
 
   public CeilingFan(String location) {
      this.location = location;
   }
  
   public void high() {
      level = HIGH;
      System.out.println(location + " ceiling fan is on high");
 
   } 

   public void medium() {
      level = MEDIUM;
      System.out.println(location + " ceiling fan is on medium");
   }

   public void low() {
      level = LOW;
      System.out.println(location + " ceiling fan is on low");
   }
 
   public void off() {
      level = 0;
      System.out.println(location + " ceiling fan is off");
   }
 
   public int getSpeed() {
      return level;
   }
}
```

**CeilingFanOnCommand**

```java
public class CeilingFanOnCommand implements Command {
   CeilingFan ceilingFan;

   public CeilingFanOnCommand(CeilingFan ceilingFan) {
      this.ceilingFan = ceilingFan;
   }
   public void execute() {
      ceilingFan.high();
   }
}
```

**CeilingFanOffCommand**

```java
public class CeilingFanOffCommand implements Command {
   CeilingFan ceilingFan;

   public CeilingFanOffCommand(CeilingFan ceilingFan) {
      this.ceilingFan = ceilingFan;
   }
   public void execute() {
      ceilingFan.off();
   }
}
```

**RemoteControl**

```java
public class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;

    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];

        Command noCommand = new NoCommand();
        for (int i = 0; i < 7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
    }

    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommands[slot] = onCommand;
        offCommands[slot] = offCommand;
    }

    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
    }

    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
    }

    public String toString() {
        StringBuffer stringBuff = new StringBuffer();
        stringBuff.append("\n------ Remote Control -------\n");
        for (int i = 0; i < onCommands.length; i++) {
            stringBuff.append("[slot " + i + "] " + onCommands[i].getClass().getName() + "    " + offCommands[i].getClass().getName() + "\n");
        }
        return stringBuff.toString();
    }
}
```

**RemoteLoader**

```java
public class RemoteLoader {

    public static void main(String[] args) {
        RemoteControl remoteControl = new RemoteControl();

        Light livingRoomLight = new Light("Living Room");
        Light kitchenLight = new Light("Kitchen");
        CeilingFan ceilingFan = new CeilingFan("Living Room");

        LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight);
        LightOnCommand kitchenLightOn = new LightOnCommand(kitchenLight);
        LightOffCommand kitchenLightOff = new LightOffCommand(kitchenLight);

        CeilingFanOnCommand ceilingFanOn = new CeilingFanOnCommand(ceilingFan);
        CeilingFanOffCommand ceilingFanOff = new CeilingFanOffCommand(ceilingFan);

        remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
        remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff);
        remoteControl.setCommand(2, ceilingFanOn, ceilingFanOff);

        System.out.println(remoteControl);

        remoteControl.onButtonWasPushed(0);
        remoteControl.offButtonWasPushed(0);
        remoteControl.onButtonWasPushed(1);
        remoteControl.offButtonWasPushed(1);
        remoteControl.onButtonWasPushed(2);
        remoteControl.offButtonWasPushed(2);
    }
}
```