<!-- omit in toc -->
# 设计模式：Dart 实现

- [创建型模式](#创建型模式)
  - [工厂方法模式](#工厂方法模式)
  - [抽象工厂模式](#抽象工厂模式)
  - [生成器模式](#生成器模式)
  - [原型模式](#原型模式)
  - [单例模式](#单例模式)
- [结构型模式](#结构型模式)
  - [适配器模式](#适配器模式)
  - [桥接模式](#桥接模式)
  - [组合模式](#组合模式)
  - [装饰模式](#装饰模式)
  - [享元模式](#享元模式)
  - [代理模式](#代理模式)
- [行为模式](#行为模式)
  - [责任链模式](#责任链模式)
  - [命令模式](#命令模式)
  - [迭代器模式](#迭代器模式)
  - [中介者模式](#中介者模式)
  - [备忘录模式](#备忘录模式)
  - [观察者模式](#观察者模式)
  - [状态模式](#状态模式)
  - [策略模式](#策略模式)
  - [模版方法模式](#模版方法模式)
  - [访问者模式](#访问者模式)

## 创建型模式

### 工厂方法模式

```dart
abstract class Creator {
  Product factoryMethod();

  String someOperation() {
    Product product = factoryMethod();
    return product.operation();
  }
}

class ConcreteCreator1 extends Creator {
  @override
  Product factoryMethod() {
    return ConcreteProduct1();
  }
}

class ConcreteCreator2 extends Creator {
  @override
  Product factoryMethod() {
    return ConcreteProduct2();
  }
}

abstract class Product {
  String operation();
}

class ConcreteProduct1 implements Product {
  @override
  String operation() {
    return "ConcreteProduct1";
  }
}

class ConcreteProduct2 implements Product {
  @override
  String operation() {
    return "ConcreteProduct2";
  }
}
```

### 抽象工厂模式

```dart
abstract class AbstractFactory {
  AbstractProductA createProductA();

  AbstractProductB createProductB();
}

class ConcreteFactory1 implements AbstractFactory {
  @override
  AbstractProductA createProductA() {
    return ConcreteProductA1();
  }

  @override
  AbstractProductB createProductB() {
    return ConcreteProductB1();
  }
}

class ConcreteFactory2 implements AbstractFactory {
  @override
  AbstractProductA createProductA() {
    return ConcreteProductA2();
  }

  @override
  AbstractProductB createProductB() {
    return ConcreteProductB2();
  }
}

abstract class AbstractProductA {
  String usefulFunctionA();
}

class ConcreteProductA1 implements AbstractProductA {
  @override
  String usefulFunctionA() {
    return "ConcreteProductA1";
  }
}

class ConcreteProductA2 implements AbstractProductA {
  @override
  String usefulFunctionA() {
    return "ConcreteProductA2";
  }
}

abstract class AbstractProductB {
  String usefulFunctionB();

  String anotherUsefulFunctionB(AbstractProductA collaborator);
}

class ConcreteProductB1 implements AbstractProductB {
  @override
  String usefulFunctionB() {
    return "ConcreteProductB1";
  }

  @override
  String anotherUsefulFunctionB(AbstractProductA collaborator) {
    return "ConcreteProductB1${collaborator.usefulFunctionA()}";
  }
}

class ConcreteProductB2 implements AbstractProductB {
  @override
  String usefulFunctionB() {
    return "ConcreteProductB2";
  }

  @override
  String anotherUsefulFunctionB(AbstractProductA collaborator) {
    return "ConcreteProductB2${collaborator.usefulFunctionA()}";
  }
}
```

### 生成器模式

```dart
abstract class Builder {
  void producePartA();

  void producePartB();

  void producePartC();
}

class ConcreteBuilder implements Builder {
  late Product _product;

  ConcreteBuilder() {
    _reset();
  }

  @override
  void producePartA() {
    _product.addPart("PartA");
  }

  @override
  void producePartB() {
    _product.addPart("PartB");
  }

  @override
  void producePartC() {
    _product.addPart("PartC");
  }

  Product getResult() {
    Product result = _product;
    _reset();
    return result;
  }

  void _reset() {
    _product = Product();
  }
}

class Product {
  final List<String> _parts = [];

  void addPart(String part) {
    _parts.add(part);
  }
}

class Director {
  static void buildMinimalViableProduct(Builder builder) {
    builder.producePartA();
  }

  static void buildFullFeaturedProduct(Builder builder) {
    builder.producePartA();
    builder.producePartB();
    builder.producePartC();
  }
}
```

### 原型模式

```java
import java.util.Objects;

public abstract class Shape {
    public int x;
    public int y;
    public String color;

    protected Shape() {
    }

    protected Shape(Shape target) {
        this.x = target.x;
        this.y = target.y;
        this.color = target.color;
    }

    public abstract Shape clone();

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Shape) {
            Shape shape = (Shape) obj;
            return this.x == shape.x && this.y == shape.y && Objects.equals(this.color, shape.color);
        }
        return false;
    }
}
```

```java
public class Rectangle extends Shape {
    public int width;
    public int height;

    public Rectangle() {
    }

    protected Rectangle(Rectangle target) {
        super(target);
        this.width = target.width;
        this.height = target.height;
    }

    @Override
    public Rectangle clone() {
        return new Rectangle(this);
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Rectangle && super.equals(obj)) {
            Rectangle rectangle = (Rectangle) obj;
            return this.width == rectangle.width && this.height == rectangle.height;
        }
        return false;
    }
}
```

```java
public class Circle extends Shape {
    public int radius;

    public Circle() {
    }

    protected Circle(Circle target) {
        super(target);
        this.radius = target.radius;
    }

    @Override
    public Circle clone() {
        return new Circle(this);
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Circle && super.equals(obj)) {
            Circle circle = (Circle) obj;
            return this.radius == circle.radius;
        }
        return false;
    }
}
```

### 单例模式

```java
public final class Singleton {
    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        Singleton result = instance;
        if (result != null) {
            return result;
        }
        synchronized (Singleton.class) {
            if (instance == null) {
                instance = new Singleton();
            }
            return instance;
        }
    }
}
```

## 结构型模式

### 适配器模式

```dart
class Target {
  String request() {
    return "Target";
  }
}

class Adaptee {
  String specificRequest() {
    return "Adaptee";
  }
}

class Adapter extends Target {
  final Adaptee _adaptee;

  Adapter(this._adaptee);

  @override
  String request() {
    return "Adapter${_adaptee.specificRequest()}";
  }
}
```

### 桥接模式

```dart
class Abstraction {
  final Implementation _implementation;

  Abstraction(this._implementation);

  String operation() {
    return _implementation.operationImplementation();
  }
}

class ExtendedAbstraction extends Abstraction {
  ExtendedAbstraction(super.implementation);

  @override
  String operation() {
    return "ExtendedAbstraction${super.operation()}";
  }
}

abstract class Implementation {
  String operationImplementation();
}

class ConcreteImplementationA implements Implementation {
  @override
  String operationImplementation() {
    return "ConcreteImplementationA";
  }
}

abstract class ConcreteImplementationB implements Implementation {
  @override
  String operationImplementation() {
    return "ConcreteImplementationB";
  }
}
```

### 组合模式

```java
public abstract class Component {
    protected Component parent;

    public Component getParent() {
        return parent;
    }

    public void setParent(Component parent) {
        this.parent = parent;
    }

    void add(Component component) {
    }

    void remove(Component component) {
    }

    boolean isComposite() {
        return false;
    }

    abstract public String operation();
}
```

```java
public class Leaf extends Component {
    @Override
    public String operation() {
        return "Leaf: operation";
    }
}
```

```java
import java.util.ArrayList;
import java.util.List;

public class Composite extends Component {
    private final List<Component> children = new ArrayList<>();

    @Override
    void add(Component component) {
        children.add(component);
        component.setParent(this);
    }

    @Override
    void remove(Component component) {
        children.remove(component);
        component.setParent(null);
    }

    @Override
    boolean isComposite() {
        return true;
    }

    @Override
    public String operation() {
        StringBuilder sb = new StringBuilder();
        for (Component child : children) {
            sb.append(child.operation());
        }
        return sb.toString();
    }
}
```

### 装饰模式

```java
public interface Component {
    void operation();
}
```

```java
public class ConcreteComponent implements Component {
    @Override
    public void operation() {
        System.out.println("ConcreteComponent: operation");
    }
}
```

```java
public abstract class Decorator implements Component {
    private final Component component;

    public Decorator(Component component) {
        this.component = component;
    }

    @Override
    public void operation() {
        component.operation();
    }
}
```

```java
public class ConcreteDecoratorA extends Decorator {
    public ConcreteDecoratorA(Component component) {
        super(component);
    }

    @Override
    public void operation() {
        super.operation();
        System.out.println("ConcreteDecoratorA: operation");
    }
}
```

```java
public class ConcreteDecoratorB extends Decorator {
    public ConcreteDecoratorB(Component component) {
        super(component);
    }

    @Override
    public void operation() {
        super.operation();
        System.out.println("ConcreteDecoratorB: operation");
    }
}
```

### 享元模式

<!-- TODO -->

### 代理模式

```java
public interface Subject {
    void request();
}
```

```java
public class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("RealSubject: request");
    }
}
```

```java
public class Proxy implements Subject {
    private final RealSubject realSubject;

    public Proxy(RealSubject realSubject) {
        this.realSubject = realSubject;
    }

    @Override
    public void request() {
        System.out.println("Proxy: request");
        realSubject.request();
    }
}
```

## 行为模式

### 责任链模式

```java
public abstract class Handler {
    private Handler successor;

    public Handler setSuccessor(Handler handler) {
        successor = handler;
        return handler;
    }

    public String handle(String request) {
        if (successor != null) {
            return successor.handle(request);
        }
        return null;
    }
}
```

```java
public class ConcreteHandler1 extends Handler{
    @Override
    public String handle(String request) {
        if (request.equals("request1")) {
            return "ConcreteHandler1: handle" + request;
        }
        return super.handle(request);
    }
}
```

```java
public class ConcreteHandler2 extends Handler {
    @Override
    public String handle(String request) {
        if (request.equals("request2")) {
            return "ConcreteHandler2: handle" + request;
        }
        return super.handle(request);
    }
}
```

### 命令模式

```java
public interface Command {
    void execute();
}
```

```java
public class SimpleCommand implements Command {
    private final String payload;

    public SimpleCommand(String payload) {
        this.payload = payload;
    }

    @Override
    public void execute() {
        System.out.println("SimpleCommand: execute" + payload);
    }
}
```

```java
public class ComplexCommand implements Command {
    private final Receiver receiver;
    private final String a;
    private final String b;

    public ComplexCommand(Receiver receiver, String a, String b) {
        this.receiver = receiver;
        this.a = a;
        this.b = b;
    }

    @Override
    public void execute() {
        System.out.println("ComplexCommand: execute");
        receiver.doSomethingA(a);
        receiver.doSomethingB(b);
    }
}
```

```java
public class Invoker {
    private final Command onStart;
    private final Command onFinish;

    public Invoker(Command onStart, Command onFinish) {
        this.onStart = onStart;
        this.onFinish = onFinish;
    }

    void doSomething() {
        onStart.execute();
        System.out.println("Invoker: doSomething");
        onFinish.execute();
    }
}
```

### 迭代器模式

<!-- TODO -->

### 中介者模式

```java
public interface Mediator {
    void notify(Colleague sender, String event);
}
```

```java
public class ConcreteMediator implements Mediator {
    private final ConcreteColleague1 colleague1;
    private final ConcreteColleague2 colleague2;

    public ConcreteMediator(ConcreteColleague1 colleague1, ConcreteColleague2 colleague2) {
        this.colleague1 = colleague1;
        this.colleague2 = colleague2;
        this.colleague1.mediator = this;
        this.colleague2.mediator = this;
    }

    @Override
    public void notify(Colleague sender, String event) {
        if (event.equals("A")) {
            colleague2.doC();
        }
        if (event.equals("D")) {
            colleague1.doB();
            colleague2.doC();
        }
    }
}
```

```java
public abstract class Colleague {
    protected Mediator mediator;

    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }
}
```

```java
public class ConcreteColleague1 extends Colleague {
    public void doA() {
        System.out.println("ConcreteColleague1: doA");
        mediator.notify(this, "A");
    }

    public void doB() {
        System.out.println("ConcreteColleague1: doB");
        mediator.notify(this, "B");
    }
}
```

```java
public class ConcreteColleague2 extends Colleague {
    public void doC() {
        System.out.println("ConcreteColleague1: doC");
        mediator.notify(this, "C");
    }

    public void doD() {
        System.out.println("ConcreteColleague1: doD");
        mediator.notify(this, "D");
    }
}
```

### 备忘录模式

```java
public class Memento {
    private final int state;

    public Memento(int state) {
        this.state = state;
    }

    public int getState() {
        return state;
    }
}
```

```java
import java.util.Random;

public class Originator {
    private int state;

    public Originator(int state) {
        this.state = state;
    }

    public int getState() {
        return state;
    }

    public Memento save() {
        return new Memento(state);
    }

    public void restore(Memento memento) {
        state = memento.getState();
    }

    public void doSomething() {
        state = (new Random()).nextInt();
    }
}
```

```java
import java.util.Deque;
import java.util.LinkedList;

public class Caretaker {
    private final Deque<Memento> mementos = new LinkedList<>();
    private final Originator originator;

    public Caretaker(Originator originator) {
        this.originator = originator;
    }

    public void backup() {
        mementos.push(originator.save());
    }

    public void undo() {
        if (mementos.isEmpty()) {
            return;
        }
        originator.restore(mementos.pop());
    }
}
```

### 观察者模式

```dart
import 'dart:math';

abstract class Subject {
  List<Observer> observers = [];

  void attach(Observer observer) {
    observers.add(observer);
  }

  void detach(Observer observer) {
    observers.remove(observer);
  }

  void notify() {
    for (var element in observers) {
      element.update(this);
    }
  }
}

abstract class Observer {
  void update(Subject subject);
}

class ConcreteSubject extends Subject {
  late int state;

  int getState() {
    return state;
  }

  void doSomething() {
    state = Random().nextInt(100);
    notify();
  }
}

class ConcreteObserver implements Observer {
  @override
  void update(Subject subject) {
    if (subject is ConcreteSubject) {
      print("ConcreteObserver: update ${subject.getState()}");
    }
  }
}
```

### 状态模式

```dart
abstract class State {
  void handle1(Context context);

  void handle2(Context context);
}

class ConcreteStateA implements State {
  @override
  void handle1(Context context) {
    print("ConcreteStateA: handle1");
    context.transitionTo(ConcreteStateB());
  }

  @override
  void handle2(Context context) {
    print("ConcreteStateA: handle2");
  }
}

class ConcreteStateB implements State {
  @override
  void handle1(Context context) {
    print("ConcreteStateB: handle1");
  }

  @override
  void handle2(Context context) {
    print("ConcreteStateB: handle2");
    context.transitionTo(ConcreteStateA());
  }
}

class Context {
  late State state;

  Context(State state) {
    transitionTo(state);
  }

  void transitionTo(State state) {
    this.state = state;
  }

  void request1() {
    state.handle1(this);
  }

  void request2() {
    state.handle2(this);
  }
}
```

### 策略模式

```dart
abstract class Strategy {
  void doAlgorithm(List<int> data);
}

class ConcreteStrategyA implements Strategy {
  @override
  void doAlgorithm(List<int> data) {
    data.sort();
  }
}

class ConcreteStrategyB implements Strategy {
  @override
  void doAlgorithm(List<int> data) {
    data.sort((a, b) => b - a);
  }
}

class Context {
  void doSomething(Strategy strategy) {
    final data = <int>[1, 2, 3, 4, 5, 6];
    strategy.doAlgorithm(data);
  }
}
```

### 模版方法模式

```dart
abstract class AbstractClass {
  void templateMethod() {
    baseOperation1();
    requiredOperation1();
    baseOperation2();
    hook1();
    requiredOperation2();
    baseOperation3();
    hook2();
  }

  void baseOperation1() {
    print("AbstractClass: baseOperation1");
  }

  void baseOperation2() {
    print("AbstractClass: baseOperation2");
  }

  void baseOperation3() {
    print("AbstractClass: baseOperation3");
  }

  void requiredOperation1();

  void requiredOperation2();

  void hook1() {}

  void hook2() {}
}

class ConcreteClass extends AbstractClass {
  @override
  void requiredOperation1() {
    print("ConcreteClass: requiredOperation1");
  }

  @override
  void requiredOperation2() {
    print("ConcreteClass: requiredOperation2");
  }

  @override
  void hook1() {
    print("ConcreteClass: hook1");
  }
}
```

### 访问者模式

```dart
abstract class Element {
  void accept(Visitor visitor);
}

class ConcreteElementA implements Element {
  @override
  void accept(Visitor visitor) {
    visitor.visitConcreteElementA(this);
  }
}

class ConcreteElementB implements Element {
  @override
  void accept(Visitor visitor) {
    visitor.visitConcreteElementB(this);
  }
}

abstract class Visitor {
  void visitConcreteElementA(ConcreteElementA element);

  void visitConcreteElementB(ConcreteElementB element);
}

class ConcreteVisitor1 implements Visitor {
  @override
  void visitConcreteElementA(ConcreteElementA element) {
    print("ConcreteVisitor1: visitConcreteElementA");
  }

  @override
  void visitConcreteElementB(ConcreteElementB element) {
    print("ConcreteVisitor1: visitConcreteElementB");
  }
}

class ConcreteVisitor2 implements Visitor {
  @override
  void visitConcreteElementA(ConcreteElementA element) {
    print("ConcreteVisitor2: visitConcreteElementA");
  }

  @override
  void visitConcreteElementB(ConcreteElementB element) {
    print("ConcreteVisitor2: visitConcreteElementB");
  }
}
```
