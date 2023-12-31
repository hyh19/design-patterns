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

  void someOperation() {
    Product product = factoryMethod();
    product.operation();
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
  void operation();
}

class ConcreteProduct1 implements Product {
  @override
  void operation() {
    print("ConcreteProduct1: operation");
  }
}

class ConcreteProduct2 implements Product {
  @override
  void operation() {
    print("ConcreteProduct2: operation");
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
  void usefulFunctionA();
}

class ConcreteProductA1 implements AbstractProductA {
  @override
  void usefulFunctionA() {
    print("ConcreteProductA1: usefulFunctionA");
  }
}

class ConcreteProductA2 implements AbstractProductA {
  @override
  void usefulFunctionA() {
    print("ConcreteProductA2: usefulFunctionA");
  }
}

abstract class AbstractProductB {
  void usefulFunctionB();

  void anotherUsefulFunctionB(AbstractProductA collaborator);
}

class ConcreteProductB1 implements AbstractProductB {
  @override
  void usefulFunctionB() {
    print("ConcreteProductB1: usefulFunctionB");
  }

  @override
  void anotherUsefulFunctionB(AbstractProductA collaborator) {
    collaborator.usefulFunctionA();
    print("ConcreteProductB1: anotherUsefulFunctionB");
  }
}

class ConcreteProductB2 implements AbstractProductB {
  @override
  void usefulFunctionB() {
    print("ConcreteProductB2: usefulFunctionB");
  }

  @override
  void anotherUsefulFunctionB(AbstractProductA collaborator) {
    collaborator.usefulFunctionA();
    print("ConcreteProductB2: anotherUsefulFunctionB");
  }
}
```

### 生成器模式

```dart
abstract class Builder {
  void buildPartA();

  void buildPartB();

  void buildPartC();
}

class ConcreteBuilder implements Builder {
  late Product _product;

  ConcreteBuilder() {
    _reset();
  }

  @override
  void buildPartA() {
    _product.addPart("PartA");
  }

  @override
  void buildPartB() {
    _product.addPart("PartB");
  }

  @override
  void buildPartC() {
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
  late final List<String> _parts = [];

  void addPart(String part) {
    _parts.add(part);
  }
}

class Director {
  static void buildMinimalViableProduct(Builder builder) {
    builder.buildPartA();
  }

  static void buildFullFeaturedProduct(Builder builder) {
    builder.buildPartA();
    builder.buildPartB();
    builder.buildPartC();
  }
}
```

### 原型模式

```dart
abstract class Shape {
  final int x;
  final int y;
  final String color;

  Shape({
    required this.x,
    required this.y,
    required this.color,
  });

  Shape clone();
}

class Rectangle extends Shape {
  final int width;
  final int height;

  Rectangle({
    required super.x,
    required super.y,
    required super.color,
    required this.width,
    required this.height,
  });

  @override
  Rectangle clone() {
    return Rectangle(
      x: x,
      y: y,
      color: color,
      width: width,
      height: height,
    );
  }
}

class Circle extends Shape {
  final int radius;

  Circle({
    required super.x,
    required super.y,
    required super.color,
    required this.radius,
  });

  @override
  Circle clone() {
    return Circle(
      x: x,
      y: y,
      color: color,
      radius: radius,
    );
  }
}
```

### 单例模式

```dart
class Singleton {
  static Singleton? _instance;

  Singleton._internal();

  factory Singleton._getInstance() {
    _instance ??= Singleton._internal();

    return _instance!;
  }

  static Singleton get instance => Singleton._getInstance();
}
```

## 结构型模式

### 适配器模式

```dart
class Target {
  void request() {
    print("Target: request");
  }
}

class Adaptee {
  void specificRequest() {
    print("Adaptee");
  }
}

class Adapter extends Target {
  final Adaptee _adaptee;

  Adapter(this._adaptee);

  @override
  void request() {
    print("Adapter: request");
    _adaptee.specificRequest();
  }
}
```

### 桥接模式

```dart
class Abstraction {
  final Implementor _imp;

  Abstraction(this._imp);

  void operation() {
    _imp.operationImp();
  }
}

class RefinedAbstraction extends Abstraction {
  RefinedAbstraction(super.implementation);

  @override
  void operation() {
    super.operation();
    print("RefinedAbstraction: operation");
  }
}

abstract class Implementor {
  void operationImp();
}

class ConcreteImplementorA implements Implementor {
  @override
  void operationImp() {
    print("ConcreteImplementorA: operationImp");
  }
}

abstract class ConcreteImplementorB implements Implementor {
  @override
  void operationImp() {
    print("ConcreteImplementorB: operationImp");
  }
}
```

### 组合模式

```dart
abstract class Component {
  void add(Component component) {}

  void remove(Component component) {}

  void operation();
}

class Leaf extends Component {
  @override
  void operation() {
    print("Leaf: operation");
  }
}

class Composite extends Component {
  late final List<Component> _children = [];

  @override
  void add(Component component) {
    _children.add(component);
  }

  @override
  void remove(Component component) {
    _children.remove(component);
  }

  @override
  void operation() {
    print("Composite: operation");
    for (final child in _children) {
      child.operation();
    }
  }
}
```

### 装饰模式

```dart
abstract class Component {
  void operation();
}

class ConcreteComponent implements Component {
  @override
  void operation() {
    print("ConcreteComponent: operation");
  }
}

abstract class Decorator implements Component {
  final Component _component;

  Decorator(this._component);

  @override
  void operation() {
    _component.operation();
  }
}

class ConcreteDecoratorA extends Decorator {
  ConcreteDecoratorA(super.component);

  @override
  void operation() {
    super.operation();
    print("ConcreteDecoratorA: operation");
  }
}

class ConcreteDecoratorB extends Decorator {
  ConcreteDecoratorB(super.component);

  @override
  void operation() {
    super.operation();
    print("ConcreteDecoratorB: operation");
  }
}
```

### 享元模式

```dart
import 'dart:math';

class BigData {
  final List<int> _nums = [];

  BigData() {
    for (var i = 0; i < 1000000; ++i) {
      _nums.add(Random().nextInt(100));
    }
  }
}

class Flyweight {
  final BigData sharedState;

  Flyweight(this.sharedState);

  void operation(List<String> uniqueState) {
    print("Flyweight: operation");
    print(sharedState);
    print(uniqueState);
  }
}

class FlyweightFactory {
  final Map<String, Flyweight> flyweights = {};

  Flyweight getFlyweight(String key) {
    flyweights.putIfAbsent(key, () => Flyweight(BigData()));
    return flyweights[key]!;
  }
}
```

### 代理模式

```dart
abstract class Subject {
  void request();
}

class RealSubject implements Subject {
  @override
  void request() {
    print("RealSubject: request");
  }
}

class Proxy implements Subject {
  final RealSubject _realSubject;

  Proxy(this._realSubject);

  @override
  void request() {
    print("Proxy: request");
    _realSubject.request();
  }
}
```

## 行为模式

### 责任链模式

```dart
abstract class Handler {
  Handler? _successor;

  Handler setSuccessor(Handler successor) {
    _successor = successor;
    return successor;
  }

  String? handleRequest(String request) {
    return _successor?.handleRequest(request);
  }
}

class ConcreteHandler1 extends Handler {
  @override
  String? handleRequest(String request) {
    if (request == "request1") {
      return "ConcreteHandler1: handle $request";
    }
    return super.handleRequest(request);
  }
}

class ConcreteHandler2 extends Handler {
  @override
  String? handleRequest(String request) {
    if (request == "request2") {
      return "ConcreteHandler2: handle $request";
    }
    return super.handleRequest(request);
  }
}
```

### 命令模式

```dart
abstract class Command {
  void execute();
}

class SimpleCommand implements Command {
  final String payload;

  SimpleCommand(this.payload);

  @override
  void execute() {
    print("SimpleCommand: execute$payload");
  }
}

class Receiver {
  void doSomethingA(String a) {
    print("Receiver: doSomethingA ($a)");
  }

  void doSomethingB(String b) {
    print("Receiver: doSomethingB ($b)");
  }
}

class ComplexCommand implements Command {
  final Receiver _receiver;
  final String _a;
  final String _b;

  ComplexCommand(this._receiver, this._a, this._b);

  @override
  void execute() {
    print("ComplexCommand: execute");
    _receiver.doSomethingA(_a);
    _receiver.doSomethingB(_b);
  }
}

class Invoker {
  final Command _onStart;
  final Command _onFinish;

  Invoker(this._onStart, this._onFinish);

  void doSomething() {
    _onStart.execute();
    print("Invoker: doSomething");
    _onFinish.execute();
  }
}
```

### 迭代器模式

```dart
class WordCollection extends Iterable<String> {
  final List<String> _words;

  WordCollection(this._words);

  @override
  Iterator<String> get iterator => WordIterator(_words);
}

class WordIterator extends Iterator<String> {
  final List<String> _words;
  int _index = 0;

  WordIterator(this._words);

  @override
  String get current {
    return _words[_index++];
  }

  @override
  bool moveNext() {
    return _index < _words.length;
  }
}
```

### 中介者模式

```dart
abstract class Mediator {
  void notify(Colleague sender, String event);
}

class ConcreteMediator implements Mediator {
  final ConcreteColleague1 _colleague1;
  final ConcreteColleague2 _colleague2;

  ConcreteMediator(this._colleague1, this._colleague2) {
    _colleague1.mediator = this;
    _colleague2.mediator = this;
  }

  @override
  void notify(Colleague sender, String event) {
    if (event == "A") {
      _colleague2.doC();
    }
    if (event == "D") {
      _colleague1.doB();
      _colleague2.doC();
    }
  }
}

abstract class Colleague {
  late final Mediator mediator;
}

class ConcreteColleague1 extends Colleague {
  void doA() {
    print("ConcreteColleague1: doA");
    mediator.notify(this, "A");
  }

  void doB() {
    print("ConcreteColleague1: doB");
    mediator.notify(this, "B");
  }
}

class ConcreteColleague2 extends Colleague {
  void doC() {
    print("ConcreteColleague1: doC");
    mediator.notify(this, "C");
  }

  void doD() {
    print("ConcreteColleague1: doD");
    mediator.notify(this, "D");
  }
}
```

### 备忘录模式

```dart
class Memento {
  final int state;

  Memento(this.state);
}

class Originator {
  int _state;

  Originator(this._state);

  int get state => _state;

  Memento createMemento() {
    return Memento(_state);
  }

  void setMemento(Memento memento) {
    _state = memento.state;
  }
}

class Caretaker {
  final List<Memento> _mementos = [];
  final Originator _originator;

  Caretaker(this._originator);

  void save() {
    _mementos.add(_originator.createMemento());
  }

  void undo() {
    if (_mementos.isEmpty) {
      return;
    }
    _originator.setMemento(_mementos.removeLast());
  }
}
```

### 观察者模式

```dart
import 'dart:math';

abstract class Subject {
  late final List<Observer> _observers = [];

  void attach(Observer observer) {
    _observers.add(observer);
  }

  void detach(Observer observer) {
    _observers.remove(observer);
  }

  void notify() {
    for (var element in _observers) {
      element.update(this);
    }
  }
}

class ConcreteSubject extends Subject {
  int _state = 0;

  int getState() {
    return _state;
  }

  void doSomething() {
    _state = Random().nextInt(100);
    notify();
  }
}

abstract class Observer {
  void update(Subject subject);
}

class ConcreteObserver implements Observer {
  int _state = 0;

  @override
  void update(Subject subject) {
    if (subject is ConcreteSubject) {
      _state = subject.getState();
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
  late State _state;

  Context(State state) {
    transitionTo(state);
  }

  void transitionTo(State state) {
    _state = state;
  }

  void request1() {
    _state.handle1(this);
  }

  void request2() {
    _state.handle2(this);
  }
}
```

### 策略模式

```dart
abstract class Strategy {
  void doAlgorithm(Context context);
}

class ConcreteStrategyA implements Strategy {
  @override
  void doAlgorithm(Context context) {
    print("ConcreteStrategyA: doAlgorithm");
  }
}

class ConcreteStrategyB implements Strategy {
  @override
  void doAlgorithm(Context context) {
    print("ConcreteStrategyB: doAlgorithm");
  }
}

class Context {
  void doSomething(Strategy strategy) {
    strategy.doAlgorithm(this);
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
