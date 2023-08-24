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

```cpp
#include <iostream>
#include <utility>

class Subject {
public:
    virtual void Request() const = 0;
};

class RealSubject : public Subject {
public:
    void Request() const override {
        std::cout << "RealSubject: Request";
    }
};

class Proxy : public Subject {
public:
    explicit Proxy(std::shared_ptr<RealSubject> realSubject) : _realSubject(std::move(realSubject)) {
    }

    void Request() const override {
        std::cout << "Proxy: Request";
        _realSubject->Request();
    }

private:
    std::shared_ptr<RealSubject> _realSubject;
};
```

## 行为模式

### 责任链模式

```cpp
#include <iostream>
#include <string>
#include <vector>

class Handler {
public:
    virtual Handler *SetNext(Handler *handler) = 0;

    virtual std::string Handle(std::string request) const = 0;
};

class AbstractHandler : public Handler {
public:
    AbstractHandler() : nextHandler(nullptr) {
    }

    Handler *SetNext(Handler *handler) override {
        this->nextHandler = handler;
        return handler;
    }

    std::string Handle(std::string request) const override {
        if (this->nextHandler) {
            return this->nextHandler->Handle(request);
        }

        return {};
    }

private:
    Handler *nextHandler;
};

class ConcreteHandler1 : public AbstractHandler {
public:
    std::string Handle(std::string request) const override {
        if (request == "request1") {
            return "ConcreteHandler1: Handle" + request;
        } else {
            return AbstractHandler::Handle(request);
        }
    }
};

class ConcreteHandler2 : public AbstractHandler {
public:
    std::string Handle(std::string request) const override {
        if (request == "request2") {
            return "ConcreteHandler2: Handle" + request;
        } else {
            return AbstractHandler::Handle(request);
        }
    }
};

class ConcreteHandler3 : public AbstractHandler {
public:
    std::string Handle(std::string request) const override {
        if (request == "request3") {
            return "ConcreteHandler3: Handle" + request;
        } else {
            return AbstractHandler::Handle(request);
        }
    }
};
```

### 命令模式

```cpp
#include <iostream>
#include <string>
#include <utility>

class Command {
public:
    virtual ~Command() = default;

    virtual void Execute() const = 0;
};

class SimpleCommand : public Command {
public:
    explicit SimpleCommand(std::string payLoad) : _payLoad(std::move(payLoad)) {
    }

    void Execute() const override {
        std::cout << "SimpleCommand: Execute" + _payLoad;
    }

private:
    std::string _payLoad;
};

class Receiver {
public:
    void DoSomethingA(const std::string &a) {
        std::cout << "Receiver: DoSomethingA" + a;
    }

    void DoSomethingB(const std::string &b) {
        std::cout << "Receiver: DoSomethingB" + b;
    }
};

class ComplexCommand : public Command {
public:
    ComplexCommand(std::shared_ptr<Receiver> receiver, std::string a, std::string b) :
            _receiver(std::move(receiver)), _a(std::move(a)), _b(std::move(b)) {
    }

    void Execute() const override {
        std::cout << "ComplexCommand: Execute";
        _receiver->DoSomethingA(_a);
        _receiver->DoSomethingB(_b);
    }

private:
    std::shared_ptr<Receiver> _receiver;
    std::string _a;
    std::string _b;
};

class Invoker {
public:
    void SetOnStart(std::shared_ptr<Command> command) {
        _onStart = std::move(command);
    }

    void SetOnFinish(std::shared_ptr<Command> command) {
        _onFinish = std::move(command);
    }

    void DoSomething() {
        if (_onStart) {
            _onStart->Execute();
        }
        if (_onFinish) {
            _onFinish->Execute();
        }
    }

private:
    std::shared_ptr<Command> _onStart;
    std::shared_ptr<Command> _onFinish;
};
```

### 迭代器模式

```cpp
#include <iostream>
#include <vector>

template<typename ElementType, typename ContainerType>
class Iterator {
public:
    explicit Iterator(ContainerType *container, bool reverse = false) : _container(container) {
        _position = _container->_elements.begin();
    }

    void First() {
        _position = _container->_elements.begin();
    }

    void Next() {
        _position++;
    }

    bool IsDone() {
        return _position == _container->_elements.end();
    }

    ElementType CurrentItem() {
        return *_position;
    }

private:
    typedef typename std::vector<ElementType>::iterator Position;

    ContainerType *_container;
    Position _position;
};

template<typename ElementType>
class Container {
public:
    void Add(ElementType element) {
        _elements.push_back(element);
    }

    Iterator<ElementType, Container> *CreateIterator() {
        return new Iterator<ElementType, Container>(this);
    }

private:
    std::vector<ElementType> _elements;

    friend class Iterator<ElementType, Container>;
};
```

### 中介者模式

```cpp
#include <string>

class BaseComponent;

class Mediator {
public:
    virtual void Notify(BaseComponent *sender, const std::string &event) const = 0;
};

class BaseComponent {
public:
    explicit BaseComponent(Mediator *mediator = nullptr) : _mediator(mediator) {
    }

    void SetMediator(Mediator *mediator) {
        _mediator = mediator;
    }

protected:
    Mediator *_mediator;
};

class Component1 : public BaseComponent {
public:
    void DoA() {
        _mediator->Notify(this, "A");
    }

    void DoB() {
        _mediator->Notify(this, "B");
    }
};

class Component2 : public BaseComponent {
public:
    void DoC() {
        _mediator->Notify(this, "C");
    }

    void DoD() {
        _mediator->Notify(this, "D");
    }
};

class ConcreteMediator : public Mediator {
public:
    ConcreteMediator(Component1 *c1, Component2 *c2) : _component1(c1), _component2(c2) {
        _component1->SetMediator(this);
        _component2->SetMediator(this);
    }

    void Notify(BaseComponent *sender, const std::string &event) const override {
        if (event == "A") {
            _component2->DoC();
        }
        if (event == "D") {
            _component1->DoB();
            _component2->DoC();
        }
    }

private:
    Component1 *_component1;
    Component2 *_component2;
};
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
