<!-- omit in toc -->
# 设计模式：Java 实现

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

```java
public abstract class Creator {
    public abstract Product factoryMethod();

    public String someOperation() {
        Product product = factoryMethod();
        return product.operation();
    }
}
```

```java
public class ConcreteCreator1 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct1();
    }
}
```

```java
public class ConcreteCreator2 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct2();
    }
}
```

```java
public interface Product {
    String operation();
}
```

```java
public class ConcreteProduct1 implements Product {
    @Override
    public String operation() {
        return "ConcreteProduct1";
    }
}
```

```java
public class ConcreteProduct2 implements Product {
    @Override
    public String operation() {
        return "ConcreteProduct2";
    }
}
```

### 抽象工厂模式

```java
public interface AbstractFactory {
    AbstractProductA createProductA();

    AbstractProductB createProductB();
}
```

```java
public class ConcreteFactory1 implements AbstractFactory {
    @Override
    public AbstractProductA createProductA() {
        return new ConcreteProductA1();
    }

    @Override
    public AbstractProductB createProductB() {
        return new ConcreteProductB1();
    }
}
```

```java
public class ConcreteFactory2 implements AbstractFactory {
    @Override
    public AbstractProductA createProductA() {
        return new ConcreteProductA2();
    }

    @Override
    public AbstractProductB createProductB() {
        return new ConcreteProductB2();
    }
}
```

```java
public interface AbstractProductA {
    String usefulFunctionA();
}
```

```java
public class ConcreteProductA1 implements AbstractProductA {
    @Override
    public String usefulFunctionA() {
        return "ConcreteProductA1";
    }
}
```

```java
public class ConcreteProductA2 implements AbstractProductA {
    @Override
    public String usefulFunctionA() {
        return "ConcreteProductA2";
    }
}
```

```java
public interface AbstractProductB {
    String usefulFunctionB();

    String anotherUsefulFunctionB(AbstractProductA collaborator);
}
```

```java
public class ConcreteProductB1 implements AbstractProductB {
    @Override
    public String usefulFunctionB() {
        return "ConcreteProductB1";
    }

    @Override
    public String anotherUsefulFunctionB(AbstractProductA collaborator) {
        return "ConcreteProductB1" + collaborator.usefulFunctionA();
    }
}
```

```java
public class ConcreteProductB2 implements AbstractProductB {
    @Override
    public String usefulFunctionB() {
        return "ConcreteProductB2";
    }

    @Override
    public String anotherUsefulFunctionB(AbstractProductA collaborator) {
        return "ConcreteProductB2" + collaborator.usefulFunctionA();
    }
}
```

### 生成器模式

```java
public interface Builder {
    void producePartA();

    void producePartB();

    void producePartC();
}
```

```java
public class ConcreteBuilder implements Builder {
    private Product product;

    public ConcreteBuilder() {
        reset();
    }

    @Override
    public void producePartA() {
        product.addPart("PartA");
    }

    @Override
    public void producePartB() {
        product.addPart("PartB");
    }

    @Override
    public void producePartC() {
        product.addPart("PartC");
    }

    public Product getResult() {
        Product result = product;
        reset();
        return result;
    }

    private void reset() {
        product = new Product();
    }
}
```

```java
import java.util.ArrayList;
import java.util.List;

public class Product {
    private final List<String> parts = new ArrayList<>();

    void addPart(String part) {
        parts.add(part);
    }
}
```

```java
public class Director {
    public static void buildMinimalViableProduct(Builder builder) {
        builder.producePartA();
    }

    public static void buildFullFeaturedProduct(Builder builder) {
        builder.producePartA();
        builder.producePartB();
        builder.producePartC();
    }
}
```

### 原型模式

```cpp
#include <unordered_map>

class Prototype {
public:

    virtual ~Prototype() = default;

    virtual Prototype *Clone() const = 0;
};

class ConcretePrototype1 : public Prototype {
public:
    Prototype *Clone() const override {
        return new ConcretePrototype1(*this);
    }
};

class ConcretePrototype2 : public Prototype {
public:
    Prototype *Clone() const override {
        return new ConcretePrototype2(*this);
    }
};
```

### 单例模式

```cpp
#include <string>

class Singleton {
public:
    Singleton(const Singleton &other) = delete;

    Singleton &operator=(const Singleton &other) = delete;

    static Singleton *GetInstance();

private:
    Singleton() = default;

    ~Singleton() = default;

    static Singleton *_singleton;
};

Singleton *Singleton::_singleton = nullptr;

Singleton *Singleton::GetInstance() {
    if (_singleton == nullptr) {
        _singleton = new Singleton();
    }
    return _singleton;
}
```

## 结构型模式

### 适配器模式

```java
public class Target {
    public String request() {
        return "Target";
    }
}
```

```java
public class Adaptee {
    public String specificRequest() {
        return "Adaptee";
    }
}
```

```java
public class Adapter extends Target {
    private final Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public String request() {
        return "Adapter" + adaptee.specificRequest();
    }
}
```

### 桥接模式

```java
public class Abstraction {
    protected final Implementation implementation;

    public Abstraction(Implementation implementation) {
        this.implementation = implementation;
    }

    public String operation() {
        return implementation.operationImplementation();
    }
}
```

```java
public class ExtendedAbstraction extends Abstraction {
    public ExtendedAbstraction(Implementation implementation) {
        super(implementation);
    }

    @Override
    public String operation() {
        return "ExtendedAbstraction" + super.operation();
    }
}
```

```java
public interface Implementation {
    String operationImplementation();
}
```

```java
public class ConcreteImplementationA implements Implementation {
    @Override
    public String operationImplementation() {
        return "ConcreteImplementationA";
    }
}
```

```java
public class ConcreteImplementationB implements Implementation {
    @Override
    public String operationImplementation() {
        return "ConcreteImplementationB";
    }
}
```

### 组合模式

```cpp
#include <algorithm>
#include <list>
#include <string>

class Component {
public:
    virtual ~Component() = default;

    void SetParent(Component *parent) {
        _parent = parent;
    }

    Component *GetParent() const {
        return _parent;
    }

    virtual void Add(Component *component) {}

    virtual void Remove(Component *component) {}

    virtual bool IsComposite() const {
        return false;
    }

    virtual std::string Operation() const = 0;

private:
    Component *_parent{};
};

class Leaf : public Component {
public:
    std::string Operation() const override {
        return "Leaf: Operation";
    }
};

class Composite : public Component {
public:
    void Add(Component *component) override {
        _children.push_back(component);
        component->SetParent(this);
    }

    void Remove(Component *component) override {
        _children.remove(component);
        component->SetParent(nullptr);
    }

    bool IsComposite() const override {
        return true;
    }

    std::string Operation() const override {
        std::string result;
        for (const Component *c: _children) {
            if (c == _children.back()) {
                result += c->Operation();
            } else {
                result += c->Operation() + "+";
            }
        }
        return result;
    }

private:
    std::list<Component *> _children;
};
```

### 装饰模式

```cpp
#include <iostream>
#include <string>

class Component {
public:
    virtual ~Component() = default;

    virtual std::string Operation() const = 0;
};

class ConcreteComponent : public Component {
public:
    std::string Operation() const override {
        return "ConcreteComponent: Operation";
    }
};

class Decorator : public Component {
public:
    explicit Decorator(Component *component) : _component(component) {
    }

    std::string Operation() const override {
        return _component->Operation();
    }

protected:
    Component *_component;
};

class ConcreteDecoratorA : public Decorator {
public:
    explicit ConcreteDecoratorA(Component *component) : Decorator(component) {
    }

    std::string Operation() const override {
        Decorator::Operation();
        return "ConcreteDecoratorA: Operation";
    }
};

class ConcreteDecoratorB : public Decorator {
public:
    explicit ConcreteDecoratorB(Component *component) : Decorator(component) {
    }

    std::string Operation() const override {
        Decorator::Operation();
        return "ConcreteDecoratorB: Operation";
    }
};
```

### 享元模式

```cpp
#include <iostream>
#include <utility>
#include <vector>
#include <unordered_map>

class Flyweight {
public:
    explicit Flyweight(std::vector<std::string> sharedState) :
            _sharedState(std::move(sharedState)) {}

    void Operation(const std::vector<std::string> &uniqueState) const {
        std::string result;
        for (const auto &item: _sharedState) {
            result += item;
        }
        for (const auto &item: uniqueState) {
            result += item;
        }
        std::cout << "Flyweight: Operation" << result;
    }

private:
    std::vector<std::string> _sharedState;
};

class FlyweightFactory {
public:
    FlyweightFactory(std::initializer_list<std::vector<std::string>> sharedStates) {
        for (const auto &sharedState: sharedStates) {
            CreateFlyweight(sharedState);
        }
    }

    Flyweight CreateFlyweight(const std::vector<std::string> &sharedState) {
        std::string key = FlyweightFactory::GetKey(sharedState);
        if (_flyweights.find(key) == _flyweights.end()) {
            _flyweights.insert({key, Flyweight(sharedState)});
        }
        return _flyweights.at(key);
    }

private:
    static std::string GetKey(const std::vector<std::string> &sharedState) {
        std::string key;
        for (const auto &item: sharedState) {
            key += item;
        }
        return key;
    }

    std::unordered_map<std::string, Flyweight> _flyweights;
};
```

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

```java
import java.util.ArrayList;
import java.util.List;

public abstract class Subject {
    protected List<Observer> observers = new ArrayList<>();

    public void attach(Observer observer) {
        observers.add(observer);
    }

    public void detach(Observer observer) {
        observers.remove(observer);
    }

    public void notify(String dummy) {
        for (Observer observer : observers) {
            observer.update(this);
        }
    }
}
```

```java
public interface Observer {
    void update(Subject subject);
}
```

```java
import java.util.Random;

public class ConcreteSubject extends Subject {
    private int state;

    public int getState() {
        return state;
    }

    public void doSomething() {
        Random random = new Random();
        state = random.nextInt();
        notify(null);
    }
}
```

```java
public class ConcreteObserver implements Observer {
    @Override
    public void update(Subject subject) {
        ConcreteSubject concreteSubject = (ConcreteSubject) subject;
        System.out.println("ConcreteObserver: update" + concreteSubject.getState());
    }
}
```

### 状态模式

```java
public interface State {
    void handle1(Context context);

    void handle2(Context context);
}
```

```java
public class ConcreteStateA implements State {
    @Override
    public void handle1(Context context) {
        System.out.println("ConcreteStateA: handle1");
        context.transitionTo(new ConcreteStateB());
    }

    @Override
    public void handle2(Context context) {
        System.out.println("ConcreteStateA: handle2");
    }
}
```

```java
public class ConcreteStateB implements State {
    @Override
    public void handle1(Context context) {
        System.out.println("ConcreteStateB: handle1");
    }

    @Override
    public void handle2(Context context) {
        System.out.println("ConcreteStateB: handle2");
        context.transitionTo(new ConcreteStateA());
    }
}
```

```java
public class Context {
    private State state;

    public Context(State state) {
        transitionTo(state);
    }

    public void transitionTo(State state) {
        this.state = state;
    }

    public void request1() {
        state.handle1(this);
    }

    public void request2() {
        state.handle2(this);
    }
}
```

### 策略模式

```java
public interface Strategy {
    void doAlgorithm(int[] data);
}
```

```java
import java.util.Arrays;

public class ConcreteStrategyA implements Strategy {
    @Override
    public void doAlgorithm(int[] data) {
        Arrays.sort(data);
    }
}
```

```java
import java.util.Arrays;

public class ConcreteStrategyB implements Strategy {
    @Override
    public void doAlgorithm(int[] data) {
        Arrays.sort(data);
    }
}
```

```java
public class Context {
    void doSomething(Strategy strategy) {
        int[] data = new int[]{1, 2, 3, 4, 5, 6};
        strategy.doAlgorithm(data);
    }
}
```

### 模版方法模式

```java
public abstract class AbstractClass {

    public final void templateMethod() {
        baseOperation1();
        requiredOperation1();
        baseOperation2();
        hook1();
        requiredOperation2();
        baseOperation3();
        hook2();
    }

    protected void baseOperation1() {
        System.out.println("AbstractClass: baseOperation1");
    }

    protected void baseOperation2() {
        System.out.println("AbstractClass: baseOperation2");
    }

    protected void baseOperation3() {
        System.out.println("AbstractClass: baseOperation3");
    }

    abstract protected void requiredOperation1();

    abstract protected void requiredOperation2();

    protected void hook1() {
    }

    protected void hook2() {
    }
}
```

```java
public class ConcreteClass extends AbstractClass {
    @Override
    protected void requiredOperation1() {
        System.out.println("ConcreteClass: requiredOperation1");
    }

    @Override
    protected void requiredOperation2() {
        System.out.println("ConcreteClass: requiredOperation2");
    }

    @Override
    protected void hook1() {
        System.out.println("ConcreteClass: hook1");
    }
}
```

### 访问者模式

```java
public interface Element {
    void accept(Visitor visitor);
}
```

```java
public class ConcreteElementA implements Element {
    @Override
    public void accept(Visitor visitor) {
        visitor.visitConcreteElementA(this);
    }
}
```

```java
public class ConcreteElementB implements Element {
    @Override
    public void accept(Visitor visitor) {
        visitor.visitConcreteElementB(this);
    }
}
```

```java
public interface Visitor {
    void visitConcreteElementA(ConcreteElementA element);

    void visitConcreteElementB(ConcreteElementB element);
}
```

```java
public class ConcreteVisitor1 implements Visitor {
    @Override
    public void visitConcreteElementA(ConcreteElementA element) {
        System.out.println("ConcreteVisitor1: visitConcreteElementA");
    }

    @Override
    public void visitConcreteElementB(ConcreteElementB element) {
        System.out.println("ConcreteVisitor1: visitConcreteElementB");
    }
}
```

```java
public class ConcreteVisitor2 implements Visitor {
    @Override
    public void visitConcreteElementA(ConcreteElementA element) {
        System.out.println("ConcreteVisitor2: visitConcreteElementA");
    }

    @Override
    public void visitConcreteElementB(ConcreteElementB element) {
        System.out.println("ConcreteVisitor2: visitConcreteElementB");
    }
}
```
