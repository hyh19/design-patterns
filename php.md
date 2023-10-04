<!-- omit in toc -->
# 设计模式：PHP 实现

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

```php
<?php

abstract class Creator
{
    /**
     * @return Product
     */
    abstract public function factoryMethod(): Product;

    /**
     * @return string
     */
    public function someOperation(): string
    {
        $product = $this->factoryMethod();
        return $product->operation();
    }
}

class ConcreteCreator1 extends Creator
{
    /**
     * @return Product
     */
    public function factoryMethod(): Product
    {
        return new ConcreteProduct1();
    }
}

class ConcreteCreator2 extends Creator
{
    /**
     * @return Product
     */
    public function factoryMethod(): Product
    {
        return new ConcreteProduct2();
    }
}

interface Product
{
    /**
     * @return string
     */
    public function operation(): string;
}

class ConcreteProduct1 implements Product
{
    /**
     * @return string
     */
    public function operation(): string
    {
        return "ConcreteProduct1";
    }
}

class ConcreteProduct2 implements Product
{
    /**
     * @return string
     */
    public function operation(): string
    {
        return "ConcreteProduct2";
    }
}
```

### 抽象工厂模式

```php
<?php

interface AbstractFactory
{
    /**
     * @return AbstractProductA
     */
    public function createProductA(): AbstractProductA;

    /**
     * @return AbstractProductB
     */
    public function createProductB(): AbstractProductB;
}

class ConcreteFactory1 implements AbstractFactory
{
    /**
     * @return AbstractProductA
     */
    public function createProductA(): AbstractProductA
    {
        return new ConcreteProductA1();
    }

    /**
     * @return AbstractProductB
     */
    public function createProductB(): AbstractProductB
    {
        return new ConcreteProductB1();
    }
}

class ConcreteFactory2 implements AbstractFactory
{
    /**
     * @return AbstractProductA
     */
    public function createProductA(): AbstractProductA
    {
        return new ConcreteProductA2();
    }

    /**
     * @return AbstractProductB
     */
    public function createProductB(): AbstractProductB
    {
        return new ConcreteProductB2();
    }
}

interface AbstractProductA
{
    /**
     * @return string
     */
    public function usefulFunctionA(): string;
}

class ConcreteProductA1 implements AbstractProductA
{
    /**
     * @return string
     */
    public function usefulFunctionA(): string
    {
        return "ConcreteProductA1";
    }
}

class ConcreteProductA2 implements AbstractProductA
{
    /**
     * @return string
     */
    public function usefulFunctionA(): string
    {
        return "ConcreteProductA2";
    }
}

interface AbstractProductB
{
    /**
     * @return string
     */
    public function usefulFunctionB(): string;

    /**
     * @param AbstractProductA $collaborator
     * @return string
     */
    public function anotherUsefulFunctionB(AbstractProductA $collaborator): string;
}

class ConcreteProductB1 implements AbstractProductB
{
    /**
     * @return string
     */
    public function usefulFunctionB(): string
    {
        return "ConcreteProductB1";
    }

    /**
     * @param AbstractProductA $collaborator
     * @return string
     */
    public function anotherUsefulFunctionB(AbstractProductA $collaborator): string
    {
        return "ConcreteProductB1" . $collaborator->usefulFunctionA();
    }
}

class ConcreteProductB2 implements AbstractProductB
{
    /**
     * @return string
     */
    public function usefulFunctionB(): string
    {
        return "ConcreteProductB2";
    }

    /**
     * @param AbstractProductA $collaborator
     * @return string
     */
    public function anotherUsefulFunctionB(AbstractProductA $collaborator): string
    {
        return "ConcreteProductB2" . $collaborator->usefulFunctionA();
    }
}
```

### 生成器模式

```php
<?php

interface Builder
{
    /**
     * @return void
     */
    public function producePartA(): void;

    /**
     * @return void
     */
    public function producePartB(): void;

    /**
     * @return void
     */
    public function producePartC(): void;
}

class ConcreteBuilder implements Builder
{
    /**
     * @var Product
     */
    private Product $product;
    
    public function __construct()
    {
        $this->reset();
    }

    /**
     * @return void
     */
    public function producePartA(): void
    {
        $this->product->addPart("PartA");
    }

    /**
     * @return void
     */
    public function producePartB(): void
    {
        $this->product->addPart("PartB");
    }

    /**
     * @return void
     */
    public function producePartC(): void
    {
        $this->product->addPart("PartC");
    }

    /**
     * @return Product
     */
    public function getResult(): Product
    {
        $result = $this->product;
        $this->reset();
        return $result;
    }

    /**
     * @return void
     */
    private function reset(): void
    {
        $this->product = new Product();
    }
}

class Product
{
    /**
     * @var string[]
     */
    private array $parts = [];

    /**
     * @param string $part
     * @return void
     */
    public function addPart(string $part): void
    {
        $this->parts[] = $part;
    }
}

class Director
{
    /**
     * @param Builder $builder
     * @return void
     */
    public static function buildMinimalViableProduct(Builder $builder): void
    {
        $builder->producePartA();
    }

    /**
     * @param Builder $builder
     * @return void
     */
    public static function buildFullFeaturedProduct(Builder $builder): void
    {
        $builder->producePartA();
        $builder->producePartB();
        $builder->producePartC();
    }
}
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

```php
<?php

class Target
{
    public function __construct()
    {
    }

    /**
     * @return string
     */
    public function request(): string
    {
        return "Target";
    }
}

class Adaptee
{
    /**
     * @return string
     */
    public function specificRequest(): string
    {
        return "Adaptee";
    }
}

class Adapter extends Target
{
    /**
     * @var Adaptee
     */
    private Adaptee $adaptee;

    /**
     * @param Adaptee $adaptee
     */
    public function __construct(Adaptee $adaptee)
    {
        parent::__construct();
        $this->adaptee = $adaptee;
    }

    /**
     * @return string
     */
    public function request(): string
    {
        return "Adapter" . $this->adaptee->specificRequest();
    }
}
```

### 桥接模式

```php
<?php

class Abstraction
{
    /**
     * @var Implementation
     */
    protected Implementation $implementation;

    /**
     * @param Implementation $implementation
     */
    public function __construct(Implementation $implementation)
    {
        $this->implementation = $implementation;
    }

    /**
     * @return string
     */
    public function operation(): string
    {
        return $this->implementation->operationImplementation();
    }
}

class ExtendedAbstraction extends Abstraction
{
    /**
     * @return string
     */
    public function operation(): string
    {
        return "ExtendedAbstraction: Extended operation with:\n" . parent::operation();
    }
}

interface Implementation
{
    /**
     * @return string
     */
    public function operationImplementation(): string;
}

class ConcreteImplementationA implements Implementation
{
    /**
     * @return string
     */
    public function operationImplementation(): string
    {
        return "ConcreteImplementationA";
    }
}

class ConcreteImplementationB implements Implementation
{
    /**
     * @return string
     */
    public function operationImplementation(): string
    {
        return "ConcreteImplementationB";
    }
}
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

abstract class Handler
{
    /**
     * @var Handler|null
     */
    private ?Handler $successor;

    /**
     * @param Handler $successor
     * @return Handler
     */
    public function setSuccessor(Handler $successor): Handler
    {
        $this->successor = $successor;
        return $successor;
    }

    /**
     * @param string $request
     * @return string|null
     */
    public function handleRequest(string $request): ?string
    {
        return $this->successor?->handleRequest($request);
    }
}

class ConcreteHandler1 extends Handler
{
    /**
     * @param string $request
     * @return string|null
     */
    public function handleRequest(string $request): ?string
    {
        if ($request === "request1") {
            return "ConcreteHandler1: handle" . $request;
        }
        return parent::handleRequest($request);
    }
}

class ConcreteHandler2 extends Handler
{
    /**
     * @param string $request
     * @return string|null
     */
    public function handleRequest(string $request): ?string
    {
        if ($request === "request2") {
            return "ConcreteHandler2: handle" . $request;
        }
        return parent::handleRequest($request);
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

```php
<?php

class AlphabeticalOrderIterator implements Iterator
{
    /**
     * @var WordsCollection
     */
    private WordsCollection $collection;

    /**
     * @var int
     */
    private int $position = 0;

    /**
     * @var bool
     */
    private bool $reverse;

    /**
     * @param WordsCollection $collection
     * @param bool $reverse
     */
    public function __construct(WordsCollection $collection, bool $reverse = false)
    {
        $this->collection = $collection;
        $this->reverse = $reverse;
    }

    /**
     * @return void
     */
    public function rewind(): void
    {
        $this->position = $this->reverse ?
            count($this->collection->getItems()) - 1 : 0;
    }

    /**
     * @return string
     */
    public function current(): string
    {
        return $this->collection->getItems()[$this->position];
    }

    /**
     * @return int
     */
    public function key(): int
    {
        return $this->position;
    }

    /**
     * @return void
     */
    public function next(): void
    {
        $this->position = $this->position + ($this->reverse ? -1 : 1);
    }

    /**
     * @return bool
     */
    public function valid(): bool
    {
        return isset($this->collection->getItems()[$this->position]);
    }
}

class WordsCollection implements IteratorAggregate
{
    /**
     * @var string[]
     */
    private array $items = [];

    /**
     * @return string[]
     */
    public function getItems(): array
    {
        return $this->items;
    }

    /**
     * @param $item
     * @return void
     */
    public function addItem($item): void
    {
        $this->items[] = $item;
    }

    /**
     * @return Iterator
     */
    public function getIterator(): Iterator
    {
        return new AlphabeticalOrderIterator($this);
    }

    /**
     * @return Iterator
     */
    public function getReverseIterator(): Iterator
    {
        return new AlphabeticalOrderIterator($this, true);
    }
}
```

### 中介者模式

```php
<?php

interface Mediator
{
    /**
     * @param Component $sender
     * @param string $event
     * @return void
     */
    public function notify(Component $sender, string $event): void;
}

class ConcreteMediator implements Mediator
{
    /**
     * @var ConcreteComponent1
     */
    private ConcreteComponent1 $component1;

    /**
     * @var ConcreteComponent2
     */
    private ConcreteComponent2 $component2;

    /**
     * @param ConcreteComponent1 $c1
     * @param ConcreteComponent2 $c2
     */
    public function __construct(ConcreteComponent1 $c1, ConcreteComponent2 $c2)
    {
        $this->component1 = $c1;
        $this->component2 = $c2;
        $this->component1->setMediator($this);
        $this->component2->setMediator($this);
    }

    /**
     * @param Component $sender
     * @param string $event
     * @return void
     */
    public function notify(Component $sender, string $event): void
    {
        if ($event == "A") {
            $this->component2->doC();
        }

        if ($event == "D") {
            $this->component1->doB();
            $this->component2->doC();
        }
    }
}

abstract class Component
{
    /**
     * @var Mediator|null
     */
    protected ?Mediator $mediator;

    /**
     * @param Mediator|null $mediator
     */
    public function __construct(Mediator $mediator = null)
    {
        $this->mediator = $mediator;
    }

    /**
     * @param Mediator $mediator
     * @return void
     */
    public function setMediator(Mediator $mediator): void
    {
        $this->mediator = $mediator;
    }
}

class ConcreteComponent1 extends Component
{
    /**
     * @return void
     */
    public function doA(): void
    {
        echo "ConcreteComponent1: doA";
        $this->mediator->notify($this, "A");
    }

    /**
     * @return void
     */
    public function doB(): void
    {
        echo "ConcreteComponent1: doB";
        $this->mediator->notify($this, "B");
    }
}

class ConcreteComponent2 extends Component
{
    /**
     * @return void
     */
    public function doC(): void
    {
        echo "ConcreteComponent2: doC";
        $this->mediator->notify($this, "C");
    }

    /**
     * @return void
     */
    public function doD(): void
    {
        echo "ConcreteComponent2: doD";
        $this->mediator->notify($this, "D");
    }
}
```

### 备忘录模式

```php
<?php

class Originator
{
    /**
     * @var string
     */
    private string $state;

    /**
     * @param string $state
     */
    public function __construct(string $state)
    {
        $this->state = $state;
    }

    /**
     * @return Memento
     */
    public function save(): Memento
    {
        return new Memento($this->state);
    }

    /**
     * @param Memento $memento
     * @return void
     */
    public function restore(Memento $memento): void
    {
        $this->state = $memento->getState();
    }

    /**
     * @return void
     */
    public function doSomething(): void
    {
        $this->state = $this->generateRandomString(30);
    }

    /**
     * @param int $length
     * @return string
     */
    private function generateRandomString(int $length = 10): string
    {
        return substr(
            str_shuffle(
                str_repeat(
                    $x = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ',
                    ceil($length / strlen($x))
                )
            ),
            1,
            $length,
        );
    }
}

class Memento
{
    /**
     * @var string
     */
    private string $state;

    /**
     * @param string $state
     */
    public function __construct(string $state)
    {
        $this->state = $state;
    }

    /**
     * @return string
     */
    public function getState(): string
    {
        return $this->state;
    }
}

class Caretaker
{
    /**
     * @var Memento[]
     */
    private array $mementos = [];

    /**
     * @var Originator
     */
    private Originator $originator;

    /**
     * @param Originator $originator
     */
    public function __construct(Originator $originator)
    {
        $this->originator = $originator;
    }

    /**
     * @return void
     */
    public function backup(): void
    {
        $this->mementos[] = $this->originator->save();
    }

    /**
     * @return void
     */
    public function undo(): void
    {
        if (!count($this->mementos)) {
            return;
        }

        $this->originator->restore(array_pop($this->mementos));
    }
}
```

### 观察者模式

```php
<?php

class Subject implements SplSubject
{
    /**
     * @var int
     */
    private int $state;

    /**
     * @var SplObjectStorage
     */
    private SplObjectStorage $observers;

    public function __construct()
    {
        $this->observers = new SplObjectStorage();
    }

    /**
     * @return int
     */
    public function getState(): int
    {
        return $this->state;
    }

    /**
     * @param SplObserver $observer
     * @return void
     */
    public function attach(SplObserver $observer): void
    {
        $this->observers->attach($observer);
    }

    /**
     * @param SplObserver $observer
     * @return void
     */
    public function detach(SplObserver $observer): void
    {
        $this->observers->detach($observer);
    }

    /**
     * @return void
     */
    public function notify(): void
    {
        foreach ($this->observers as $observer) {
            $observer->update($this);
        }
    }

    /**
     * @return void
     */
    public function doSomething(): void
    {
        $this->state = rand(0, 10);
        $this->notify();
    }
}

class ConcreteObserverA implements SplObserver
{
    /**
     * @param SplSubject $subject
     * @return void
     */
    public function update(SplSubject $subject): void
    {
        echo "ConcreteObserverA: update";
    }
}

class ConcreteObserverB implements SplObserver
{
    /**
     * @param SplSubject $subject
     * @return void
     */
    public function update(SplSubject $subject): void
    {
        echo "ConcreteObserverB: update";
    }
}
```

### 状态模式

```php
<?php

class Context
{
    /**
     * @var State
     */
    private State $state;

    /**
     * @param State $state
     */
    public function __construct(State $state)
    {
        $this->transitionTo($state);
    }

    /**
     * @param State $state
     * @return void
     */
    public function transitionTo(State $state): void
    {
        $this->state = $state;
        $this->state->setContext($this);
    }

    /**
     * @return void
     */
    public function request1(): void
    {
        $this->state->handle1();
    }

    /**
     * @return void
     */
    public function request2(): void
    {
        $this->state->handle2();
    }
}

abstract class State
{
    /**
     * @var Context
     */
    protected Context $context;

    /**
     * @param Context $context
     * @return void
     */
    public function setContext(Context $context): void
    {
        $this->context = $context;
    }

    /**
     * @return void
     */
    abstract public function handle1(): void;

    /**
     * @return void
     */
    abstract public function handle2(): void;
}

class ConcreteStateA extends State
{
    /**
     * @return void
     */
    public function handle1(): void
    {
        echo "ConcreteStateA: handle1";
        $this->context->transitionTo(new ConcreteStateB());
    }

    /**
     * @return void
     */
    public function handle2(): void
    {
        echo "ConcreteStateA: handle2";
    }
}

class ConcreteStateB extends State
{
    /**
     * @return void
     */
    public function handle1(): void
    {
        echo "ConcreteStateB: handle1";
    }

    /**
     * @return void
     */
    public function handle2(): void
    {
        echo "ConcreteStateB: handle2";
        $this->context->transitionTo(new ConcreteStateA());
    }
}
```

### 策略模式

```php
<?php

class Context
{
    public function doSomething(Strategy $strategy): void
    {
        $result = $strategy->doAlgorithm(["a", "b", "c", "d", "e"]);
        echo implode(",", $result);
    }
}

interface Strategy
{
    /**
     * @param string[] $data
     * @return string[]
     */
    public function doAlgorithm(array $data): array;
}

class ConcreteStrategyA implements Strategy
{
    /**
     * @param string[] $data
     * @return string[]
     */
    public function doAlgorithm(array $data): array
    {
        sort($data);
        return $data;
    }
}

class ConcreteStrategyB implements Strategy
{
    /**
     * @param string[] $data
     * @return string[]
     */
    public function doAlgorithm(array $data): array
    {
        rsort($data);
        return $data;
    }
}
```

### 模版方法模式

```php
<?php

abstract class AbstractClass
{
    /**
     * @return void
     */
    final public function templateMethod(): void
    {
        $this->baseOperation1();
        $this->requiredOperation1();
        $this->baseOperation2();
        $this->hook1();
        $this->requiredOperation2();
        $this->baseOperation3();
        $this->hook2();
    }

    /**
     * @return void
     */
    protected function baseOperation1(): void
    {
        echo "AbstractClass: baseOperation1";
    }

    /**
     * @return void
     */
    protected function baseOperation2(): void
    {
        echo "AbstractClass: baseOperation2";
    }

    /**
     * @return void
     */
    protected function baseOperation3(): void
    {
        echo "AbstractClass: baseOperation3";
    }

    /**
     * @return void
     */
    abstract protected function requiredOperation1(): void;

    /**
     * @return void
     */
    abstract protected function requiredOperation2(): void;

    /**
     * @return void
     */
    protected function hook1(): void
    {
    }

    /**
     * @return void
     */
    protected function hook2(): void
    {
    }
}

class ConcreteClass1 extends AbstractClass
{
    /**
     * @return void
     */
    protected function requiredOperation1(): void
    {
        echo "ConcreteClass1: requiredOperation1";
    }

    /**
     * @return void
     */
    protected function requiredOperation2(): void
    {
        echo "ConcreteClass1: requiredOperation2";
    }
}

class ConcreteClass2 extends AbstractClass
{
    /**
     * @return void
     */
    protected function requiredOperation1(): void
    {
        echo "ConcreteClass2: requiredOperation1";
    }

    /**
     * @return void
     */
    protected function requiredOperation2(): void
    {
        echo "ConcreteClass2: requiredOperation2";
    }

    /**
     * @return void
     */
    protected function hook1(): void
    {
        echo "ConcreteClass2: hook1";
    }
}
```

### 访问者模式

```php
<?php

interface Component
{
    /**
     * @param Visitor $visitor
     * @return void
     */
    public function accept(Visitor $visitor): void;
}

class ConcreteComponentA implements Component
{
    /**
     * @param Visitor $visitor
     * @return void
     */
    public function accept(Visitor $visitor): void
    {
        $visitor->visitConcreteComponentA($this);
    }
}

class ConcreteComponentB implements Component
{
    /**
     * @param Visitor $visitor
     * @return void
     */
    public function accept(Visitor $visitor): void
    {
        $visitor->visitConcreteComponentB($this);
    }
}

interface Visitor
{
    /**
     * @param ConcreteComponentA $element
     * @return void
     */
    public function visitConcreteComponentA(ConcreteComponentA $element): void;

    /**
     * @param ConcreteComponentB $element
     * @return void
     */
    public function visitConcreteComponentB(ConcreteComponentB $element): void;
}

class ConcreteVisitor1 implements Visitor
{
    /**
     * @param ConcreteComponentA $element
     * @return void
     */
    public function visitConcreteComponentA(ConcreteComponentA $element): void
    {
        echo "ConcreteVisitor2: visitConcreteComponentA";
    }

    /**
     * @param ConcreteComponentB $element
     * @return void
     */
    public function visitConcreteComponentB(ConcreteComponentB $element): void
    {
        echo "ConcreteVisitor2: visitConcreteComponentB";
    }
}

class ConcreteVisitor2 implements Visitor
{
    /**
     * @param ConcreteComponentA $element
     * @return void
     */
    public function visitConcreteComponentA(ConcreteComponentA $element): void
    {
        echo "ConcreteVisitor2: visitConcreteComponentA";
    }

    /**
     * @param ConcreteComponentB $element
     * @return void
     */
    public function visitConcreteComponentB(ConcreteComponentB $element): void
    {
        echo "ConcreteVisitor2: visitConcreteComponentB";
    }
}
```
