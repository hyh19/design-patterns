<!-- omit in toc -->
# 设计模式：C++ 实现

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

```cpp
#include <string>

class Product {
public:
    virtual ~Product() = default;

    virtual std::string Operation() const = 0;
};

class ConcreteProduct1 : public Product {
public:
    std::string Operation() const override {
        return "ConcreteProduct1";
    }
};

class ConcreteProduct2 : public Product {
public:
    std::string Operation() const override {
        return "ConcreteProduct2";
    }
};

class Creator {
public:
    virtual ~Creator() = default;

    virtual Product *FactoryMethod() const = 0;

    std::string SomeOperation() const {
        Product *product = FactoryMethod();
        std::string result = product->Operation();
        delete product;
        return result;
    }
};

class ConcreteCreator1 : public Creator {
public:
    Product *FactoryMethod() const override {
        return new ConcreteProduct1();
    }
};

class ConcreteCreator2 : public Creator {
public:
    Product *FactoryMethod() const override {
        return new ConcreteProduct2();
    }
};
```

### 抽象工厂模式

```cpp
#include <string>

class AbstractProductA {
public:
    virtual ~AbstractProductA() = default;

    virtual std::string UsefulFunctionA() const = 0;
};

class ConcreteProductA1 : public AbstractProductA {
public:
    std::string UsefulFunctionA() const override {
        return "ConcreteProductA1";
    }
};

class ConcreteProductA2 : public AbstractProductA {
public:
    std::string UsefulFunctionA() const override {
        return "ConcreteProductA2";
    }
};

class AbstractProductB {
public:
    virtual ~AbstractProductB() = default;

    virtual std::string UsefulFunctionB() const = 0;

    virtual std::string AnotherUsefulFunctionB(const AbstractProductA &collaborator) const = 0;
};

class ConcreteProductB1 : public AbstractProductB {
public:
    std::string UsefulFunctionB() const override {
        return "ConcreteProductB1";
    }

    std::string AnotherUsefulFunctionB(const AbstractProductA &collaborator) const override {
        return "ConcreteProductB1" + collaborator.UsefulFunctionA();
    }
};

class ConcreteProductB2 : public AbstractProductB {
public:
    std::string UsefulFunctionB() const override {
        return "ConcreteProductB2";
    }

    std::string AnotherUsefulFunctionB(const AbstractProductA &collaborator) const override {
        return "ConcreteProductB2" + collaborator.UsefulFunctionA();
    }
};

class AbstractFactory {
public:
    virtual ~AbstractFactory() = default;

    virtual AbstractProductA *CreateProductA() const = 0;

    virtual AbstractProductB *CreateProductB() const = 0;
};

class ConcreteFactory1 : public AbstractFactory {
public:
    AbstractProductA *CreateProductA() const override {
        return new ConcreteProductA1();
    }

    AbstractProductB *CreateProductB() const override {
        return new ConcreteProductB1();
    }
};

class ConcreteFactory2 : public AbstractFactory {
public:
    AbstractProductA *CreateProductA() const override {
        return new ConcreteProductA2();
    }

    AbstractProductB *CreateProductB() const override {
        return new ConcreteProductB2();
    }
};
```

### 生成器模式

```cpp
#include <string>
#include <vector>

class Builder {
public:
    virtual ~Builder() = default;

    virtual void ProducePartA() const = 0;

    virtual void ProducePartB() const = 0;

    virtual void ProducePartC() const = 0;
};

class Product {
public:
    void AddPart(const std::string &part) {
        _parts.emplace_back(part);
    }

private:
    std::vector<std::string> _parts;
};

class ConcreteBuilder : public Builder {
public:
    ConcreteBuilder() {
        Reset();
    }

    ~ConcreteBuilder() override {
        delete _product;
    }

    void ProducePartA() const override {
        _product->AddPart("PartA");
    }

    void ProducePartB() const override {
        _product->AddPart("PartB");
    }

    void ProducePartC() const override {
        _product->AddPart("PartC");
    }

    Product *GetResult() {
        Product *result = _product;
        Reset();
        return result;
    }

private:
    void Reset() {
        _product = new Product();
    }

    Product *_product;
};

class Director {
public:
    static void BuildMinimalViableProduct(const Builder *builder) {
        builder->ProducePartA();
    }

    static void BuildFullFeaturedProduct(const Builder *builder) {
        builder->ProducePartA();
        builder->ProducePartB();
        builder->ProducePartC();
    }
};
```

### 原型模式

```php
<?php

class Prototype
{
    /**
     * @var int
     */
    public int $primitive;

    /**
     * @var DateTime
     */
    public DateTime $object;

    /**
     * @return void
     */
    public function __clone(): void
    {
        $this->object = clone $this->object;
    }
}
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

```cpp
#include <string>
#include <utility>

class Target {
public:
    virtual ~Target() = default;

    virtual std::string Request() const {
        return "Target";
    }
};

class Adaptee {
public:
    std::string SpecificRequest() const {
        return "Adaptee";
    }
};

class Adapter : public Target {
public:
    explicit Adapter(std::shared_ptr<Adaptee> adaptee) : _adaptee(std::move(adaptee)) {}

    std::string Request() const override {
        return "Adapter" + _adaptee->SpecificRequest();
    }

private:
    std::shared_ptr<Adaptee> _adaptee;
};
```

### 桥接模式

```cpp
#include <string>
#include <utility>

class Implementation {
public:
    virtual ~Implementation() = default;

    virtual std::string OperationImplementation() const = 0;
};

class ConcreteImplementationA : public Implementation {
public:
    std::string OperationImplementation() const override {
        return "ConcreteImplementationA";
    }
};

class ConcreteImplementationB : public Implementation {
public:
    std::string OperationImplementation() const override {
        return "ConcreteImplementationB";
    }
};

class Abstraction {
public:
    explicit Abstraction(std::shared_ptr<Implementation> implementation) :
            _implementation(std::move(implementation)) {}

    virtual ~Abstraction() = default;

    virtual std::string Operation() const {
        return _implementation->OperationImplementation();
    }

protected:
    std::shared_ptr<Implementation> _implementation;
};

class ExtendedAbstraction : public Abstraction {
public:
    explicit ExtendedAbstraction(const std::shared_ptr<Implementation> &implementation) : Abstraction(implementation) {}

    std::string Operation() const override {
        return "ExtendedAbstraction" + Abstraction::Operation();
    }
};
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
// TODO
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

```cpp
#include <string>
#include <utility>
#include <vector>

class Originator;

class Memento {
public:
    explicit Memento(std::string state) : _state(std::move(state)) {
    }

    std::string GetState() const {
        return _state;
    }

private:
    std::string _state;
};

class Originator {
public:
    explicit Originator(std::string state) : _state(std::move(state)) {
    }

    const Memento *Save() {
        return new Memento(_state);
    }

    void Restore(const Memento *memento) {
        _state = memento->GetState();
    }

private:
    std::string _state;
};

class Caretaker {
public:
    explicit Caretaker(Originator *originator) : _originator(originator) {
    }

    ~Caretaker() {
        for (auto memento: _mementos) {
            delete memento;
        }
    }

    void Backup() {
        _mementos.push_back(_originator->Save());
    }

    void Undo() {
        if (_mementos.empty()) {
            return;
        }

        const Memento *memento = _mementos.back();
        _mementos.pop_back();
        _originator->Restore(memento);
        delete memento;
    }

private:
    std::vector<const Memento *> _mementos;
    Originator *_originator;
};
```

### 观察者模式

```cpp
#include <iostream>
#include <list>
#include <utility>

class Observer {
public:
    virtual ~Observer() = default;

    virtual void Update(const std::string &state) = 0;
};

class Subject {
public:
    virtual ~Subject() = default;

    virtual void Attach(Observer *observer) = 0;

    virtual void Detach(Observer *observer) = 0;

    virtual void Notify() = 0;
};

class ConcreteSubject : public Subject {
public:
    void Attach(Observer *observer) override {
        _observers.push_back(observer);
    }

    void Detach(Observer *observer) override {
        _observers.remove(observer);
    }

    void Notify() override {
        auto iterator = _observers.begin();
        while (iterator != _observers.end()) {
            (*iterator)->Update(_state);
            ++iterator;
        }
    }

    void doSomething() {
        _state = "Change State";
        Notify();
    }

private:
    std::list<Observer *> _observers;
    std::string _state;
};

class ConcreteObserver : public Observer {
public:
    explicit ConcreteObserver(ConcreteSubject *subject) : _subject(subject) {
        _subject->Attach(this);
    }

    ~ConcreteObserver() override {
        RemoveMeFromTheList();
    }

    void Update(const std::string &state) override {
        _state = state;
    }

    void RemoveMeFromTheList() {
        if (_subject) {
            _subject->Detach(this);
            _subject = nullptr;
        }
    }

private:
    std::string _state;
    ConcreteSubject *_subject;
};
```

### 状态模式

```cpp
#include <iostream>
#include <utility>

class Context;

class State {
public:
    virtual ~State() = default;

    virtual void Handle1(Context *context) = 0;

    virtual void Handle2(Context *context) = 0;
};

class Context {
public:
    explicit Context(std::shared_ptr<State> state) : _state(std::move(state)) {}

    void TransitionTo(std::shared_ptr<State> state) {
        _state = std::move(state);
    }

    void Request1() {
        _state->Handle1(this);
    }

    void Request2() {
        _state->Handle2(this);
    }

private:
    std::shared_ptr<State> _state;
};

class ConcreteStateA : public State {
public:
    void Handle1(Context *context) override;

    void Handle2(Context *context) override {
        std::cout << "ConcreteStateA: Handle2";
    }
};

class ConcreteStateB : public State {
public:
    void Handle1(Context *context) override {
        std::cout << "ConcreteStateB: Handle1";
    }

    void Handle2(Context *context) override {
        std::cout << "ConcreteStateB: Handle2";
        context->TransitionTo(std::make_shared<ConcreteStateA>());
    }
};

void ConcreteStateA::Handle1(Context *context) {
    std::cout << "ConcreteStateA: Handle1";
    context->TransitionTo(std::make_shared<ConcreteStateB>());
}
```

### 策略模式

```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <utility>

class Strategy {
public:
    virtual ~Strategy() = default;

    virtual std::string doAlgorithm(std::string_view data) const = 0;
};

class Context {
public:
    void doSomething(const Strategy *strategy) const {
        std::string result = strategy->doAlgorithm("aecbd");
        std::cout << result;
    }
};

class ConcreteStrategyA : public Strategy {
public:
    std::string doAlgorithm(std::string_view data) const override {
        std::string result(data);
        std::sort(std::begin(result), std::end(result));

        return result;
    }
};

class ConcreteStrategyB : public Strategy {
    std::string doAlgorithm(std::string_view data) const override {
        std::string result(data);
        std::sort(std::begin(result), std::end(result), std::greater<>());

        return result;
    }
};
```

### 模版方法模式

```cpp
#include <iostream>

class AbstractClass {
public:
    void TemplateMethod() const {
        this->BaseOperation1();
        this->RequiredOperation1();
        this->BaseOperation2();
        this->Hook1();
        this->RequiredOperation2();
        this->BaseOperation3();
        this->Hook2();
    }

protected:
    void BaseOperation1() const {
        std::cout << "AbstractClass: BaseOperation1";
    }

    void BaseOperation2() const {
        std::cout << "AbstractClass: BaseOperation2";
    }

    void BaseOperation3() const {
        std::cout << "AbstractClass: BaseOperation3";
    }

    virtual void RequiredOperation1() const = 0;

    virtual void RequiredOperation2() const = 0;

    virtual void Hook1() const {}

    virtual void Hook2() const {}
};

class ConcreteClass1 : public AbstractClass {
protected:
    void RequiredOperation1() const override {
        std::cout << "ConcreteClass1: RequiredOperation1";
    }

    void RequiredOperation2() const override {
        std::cout << "ConcreteClass1: RequiredOperation2";
    }
};

class ConcreteClass2 : public AbstractClass {
protected:
    void RequiredOperation1() const override {
        std::cout << "ConcreteClass2: RequiredOperation1";
    }

    void RequiredOperation2() const override {
        std::cout << "ConcreteClass2: RequiredOperation2";
    }

    void Hook1() const override {
        std::cout << "ConcreteClass2: Hook1";
    }
};
```

### 访问者模式

```cpp
#include <iostream>

class ConcreteComponentA;

class ConcreteComponentB;

class Visitor {
public:
    virtual void VisitConcreteComponentA(const ConcreteComponentA *element) const = 0;

    virtual void VisitConcreteComponentB(const ConcreteComponentB *element) const = 0;
};

class Component {
public:
    virtual ~Component() = default;

    virtual void Accept(const Visitor *visitor) const = 0;
};

class ConcreteComponentA : public Component {
public:
    void Accept(const Visitor *visitor) const override {
        visitor->VisitConcreteComponentA(this);
    }
};

class ConcreteComponentB : public Component {
public:
    void Accept(const Visitor *visitor) const override {
        visitor->VisitConcreteComponentB(this);
    }
};

class ConcreteVisitor1 : public Visitor {
public:
    void VisitConcreteComponentA(const ConcreteComponentA *element) const override {
        std::cout << "ConcreteVisitor1: VisitConcreteComponentA";
    }

    void VisitConcreteComponentB(const ConcreteComponentB *element) const override {
        std::cout << "ConcreteVisitor1: VisitConcreteComponentB";
    }
};

class ConcreteVisitor2 : public Visitor {
public:
    void VisitConcreteComponentA(const ConcreteComponentA *element) const override {
        std::cout << "ConcreteVisitor2: VisitConcreteComponentA";
    }

    void VisitConcreteComponentB(const ConcreteComponentB *element) const override {
        std::cout << "ConcreteVisitor2: VisitConcreteComponentB";
    }
};
```
