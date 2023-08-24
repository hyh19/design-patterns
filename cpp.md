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

```php
<?php

class Singleton
{
    /**
     * @var Singleton[]
     */
    private static array $instances = [];

    protected function __construct()
    {
    }

    /**
     * @return void
     */
    protected function __clone(): void
    {
    }

    /**
     * @return void
     * @throws Exception
     */
    public function __wakeup(): void
    {
        throw new Exception("Cannot unserialize a singleton.");
    }

    /**
     * @return Singleton
     */
    public static function getInstance(): Singleton
    {
        $cls = static::class;
        if (!isset(self::$instances[$cls])) {
            self::$instances[$cls] = new static();
        }

        return self::$instances[$cls];
    }
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

```php
<?php

abstract class Component
{
    /**
     * @var Component|null
     */
    protected ?Component $parent;

    /**
     * @param Component|null $parent
     * @return void
     */
    public function setParent(?Component $parent): void
    {
        $this->parent = $parent;
    }

    /**
     * @return Component
     */
    public function getParent(): Component
    {
        return $this->parent;
    }

    /**
     * @param Component $component
     * @return void
     */
    public function add(Component $component): void
    {
    }

    /**
     * @param Component $component
     * @return void
     */
    public function remove(Component $component): void
    {
    }

    /**
     * @return bool
     */
    public function isComposite(): bool
    {
        return false;
    }

    /**
     * @return string
     */
    abstract public function operation(): string;
}

class Leaf extends Component
{
    /**
     * @return string
     */
    public function operation(): string
    {
        return "Leaf";
    }
}

class Composite extends Component
{
    /**
     * @var SplObjectStorage
     */
    protected SplObjectStorage $children;

    public function __construct()
    {
        $this->children = new SplObjectStorage();
    }

    /**
     * @param Component $component
     * @return void
     */
    public function add(Component $component): void
    {
        $this->children->attach($component);
        $component->setParent($this);
    }

    /**
     * @param Component $component
     * @return void
     */
    public function remove(Component $component): void
    {
        $this->children->detach($component);
        $component->setParent(null);
    }

    /**
     * @return bool
     */
    public function isComposite(): bool
    {
        return true;
    }

    /**
     * @return string
     */
    public function operation(): string
    {
        $results = [];
        foreach ($this->children as $child) {
            $results[] = $child->operation();
        }

        return implode(",", $results);
    }
}
```

### 装饰模式

```php
<?php

interface Component
{
    /**
     * @return string
     */
    public function operation(): string;
}

class ConcreteComponent implements Component
{
    /**
     * @return string
     */
    public function operation(): string
    {
        return "ConcreteComponent: operation";
    }
}

class Decorator implements Component
{
    /**
     * @var Component
     */
    protected Component $component;

    /**
     * @param Component $component
     */
    public function __construct(Component $component)
    {
        $this->component = $component;
    }

    /**
     * @return string
     */
    public function operation(): string
    {
        return $this->component->operation();
    }
}

class ConcreteDecoratorA extends Decorator
{
    /**
     * @return string
     */
    public function operation(): string
    {
        parent::operation();
        return "ConcreteDecoratorA: operation";
    }
}

class ConcreteDecoratorB extends Decorator
{
    /**
     * @return string
     */
    public function operation(): string
    {
        parent::operation();
        return "ConcreteDecoratorB: operation";
    }
}
```

### 享元模式

```php
<?php

class Flyweight
{
    /**
     * @var string[]
     */
    private array $sharedState;

    /**
     * @param string[] $sharedState
     */
    public function __construct(array $sharedState)
    {
        $this->sharedState = $sharedState;
    }

    /**
     * @param string[] $uniqueState
     * @return void
     */
    public function operation(array $uniqueState): void
    {
        var_dump($this->sharedState);
        var_dump($uniqueState);
        echo "Flyweight: operation";
    }
}

class FlyweightFactory
{
    /**
     * @var Flyweight[]
     */
    private array $flyweights = [];

    /**
     * @param string[][] $flyweights
     */
    public function __construct(array $flyweights)
    {
        foreach ($flyweights as $state) {
            $this->flyweights[$this->getKey($state)] = new Flyweight($state);
        }
    }

    /**
     * @param string[] $sharedState
     * @return Flyweight
     */
    public function getFlyweight(array $sharedState): Flyweight
    {
        $key = $this->getKey($sharedState);
        if (!isset($this->flyweights[$key])) {
            $this->flyweights[$key] = new Flyweight($sharedState);
        }
        return $this->flyweights[$key];
    }

    /**
     * @param string[] $state
     * @return string
     */
    private function getKey(array $state): string
    {
        return implode("_", $state);
    }
}
```

### 代理模式

```php
<?php

interface Subject
{
    /**
     * @return void
     */
    public function request(): void;
}

class RealSubject implements Subject
{
    /**
     * @return void
     */
    public function request(): void
    {
        echo "RealSubject: request";
    }
}

class Proxy implements Subject
{
    /**
     * @var RealSubject
     */
    private RealSubject $realSubject;

    /**
     * @param RealSubject $realSubject
     */
    public function __construct(RealSubject $realSubject)
    {
        $this->realSubject = $realSubject;
    }

    /**
     * @return void
     */
    public function request(): void
    {
        echo "Proxy: request";
        $this->realSubject->request();
    }
}
```

## 行为模式

### 责任链模式

```php
<?php

interface Handler
{
    /**
     * @param Handler $handler
     * @return Handler
     */
    public function setNext(Handler $handler): Handler;

    /**
     * @param string $request
     * @return string|null
     */
    public function handle(string $request): ?string;
}

abstract class BaseHandler implements Handler
{
    /**
     * @var Handler|null
     */
    private ?Handler $nextHandler;

    /**
     * @param Handler $handler
     * @return Handler
     */
    public function setNext(Handler $handler): Handler
    {
        $this->nextHandler = $handler;
        return $handler;
    }

    /**
     * @param string $request
     * @return string|null
     */
    public function handle(string $request): ?string
    {
        return $this->nextHandler?->handle($request);
    }
}

class ConcreteHandler1 extends BaseHandler
{
    /**
     * @param string $request
     * @return string|null
     */
    public function handle(string $request): ?string
    {
        if ($request === "request1") {
            return "ConcreteHandler1:" . $request;
        } else {
            return parent::handle($request);
        }
    }
}

class ConcreteHandler2 extends BaseHandler
{
    /**
     * @param string $request
     * @return string|null
     */
    public function handle(string $request): ?string
    {
        if ($request === "request2") {
            return "ConcreteHandler3:" . $request;
        } else {
            return parent::handle($request);
        }
    }
}

class ConcreteHandler3 extends BaseHandler
{
    /**
     * @param string $request
     * @return string|null
     */
    public function handle(string $request): ?string
    {
        if ($request === "request3") {
            return "ConcreteHandler3:" . $request;
        } else {
            return parent::handle($request);
        }
    }
}
```

### 命令模式

```php
<?php

interface Command
{
    /**
     * @return void
     */
    public function execute(): void;
}

class SimpleCommand implements Command
{
    /**
     * @var string
     */
    private string $payload;

    /**
     * @param string $payload
     */
    public function __construct(string $payload)
    {
        $this->payload = $payload;
    }

    /**
     * @return void
     */
    public function execute(): void
    {
        echo "SimpleCommand: execute ($this->payload)";
    }
}

class ComplexCommand implements Command
{
    /**
     * @var Receiver
     */
    private Receiver $receiver;

    /**
     * @var string
     */
    private string $a;

    /**
     * @var string
     */
    private string $b;

    /**
     * @param Receiver $receiver
     * @param string $a
     * @param string $b
     */
    public function __construct(Receiver $receiver, string $a, string $b)
    {
        $this->receiver = $receiver;
        $this->a = $a;
        $this->b = $b;
    }

    /**
     * @return void
     */
    public function execute(): void
    {
        echo "ComplexCommand: execute";
        $this->receiver->doSomethingA($this->a);
        $this->receiver->doSomethingB($this->b);
    }
}

class Receiver
{
    /**
     * @param string $a
     * @return void
     */
    public function doSomethingA(string $a): void
    {
        echo "Receiver: doSomethingA ($a)";
    }

    /**
     * @param string $b
     * @return void
     */
    public function doSomethingB(string $b): void
    {
        echo "Receiver: doSomethingB ($b)";
    }
}

class Invoker
{
    /**
     * @var Command
     */
    private Command $onStart;

    /**
     * @var Command
     */
    private Command $onFinish;

    /**
     * @param Command $command
     * @return void
     */
    public function setOnStart(Command $command): void
    {
        $this->onStart = $command;
    }

    /**
     * @param Command $command
     * @return void
     */
    public function setOnFinish(Command $command): void
    {
        $this->onFinish = $command;
    }

    /**
     * @return void
     */
    public function doSomething(): void
    {
        $this->onStart->execute();
        $this->onFinish->execute();
    }
}
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
