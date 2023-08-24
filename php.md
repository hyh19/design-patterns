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
from __future__ import annotations
from abc import ABC, abstractmethod
from random import randrange
from typing import List


class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def detach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def notify(self) -> None:
        pass


class ConcreteSubject(Subject):
    def __init__(self) -> None:
        self._state: int = randrange(0, 10)
        self._observers: List[Observer] = []

    def attach(self, observer: Observer) -> None:
        self._observers.append(observer)

    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)

    def notify(self) -> None:
        for observer in self._observers:
            observer.update(self._state)

    def some_business_logic(self) -> None:
        self._state = randrange(0, 10)
        self.notify()


class Observer(ABC):
    @abstractmethod
    def update(self, state: int) -> None:
        pass


class ConcreteObserverA(Observer):
    def update(self, state: int) -> None:
        print("ConcreteObserverA: update")


class ConcreteObserverB(Observer):
    def update(self, state: int) -> None:
        print("ConcreteObserverB: update")
```

### 状态模式

```php
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import Optional


class Context:
    def __init__(self, state: State) -> None:
        self.transition_to(state)

    def transition_to(self, state: State) -> None:
        self._state = state
        self._state.context = self

    def request1(self) -> None:
        self._state.handle1()

    def request2(self) -> None:
        self._state.handle2()


class State(ABC):
    def __init__(self) -> None:
        self._context: Optional[Context] = None

    @property
    def context(self) -> Optional[Context]:
        return self._context

    @context.setter
    def context(self, context: Context) -> None:
        self._context = context

    @abstractmethod
    def handle1(self) -> None:
        pass

    @abstractmethod
    def handle2(self) -> None:
        pass


class ConcreteStateA(State):
    def handle1(self) -> None:
        print("ConcreteStateA handle1")
        assert self.context is not None
        self.context.transition_to(ConcreteStateB())

    def handle2(self) -> None:
        print("ConcreteStateA handle2")


class ConcreteStateB(State):
    def handle1(self) -> None:
        print("ConcreteStateB handle1")

    def handle2(self) -> None:
        print("ConcreteStateB handle2")
        assert self.context is not None
        self.context.transition_to(ConcreteStateA())
```

### 策略模式

```php
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Strategy(ABC):
    @abstractmethod
    def do_algorithm(self, data: List[str]) -> List[str]:
        pass


class ConcreteStrategyA(Strategy):
    def do_algorithm(self, data: List[str]) -> List[str]:
        return sorted(data)


class ConcreteStrategyB(Strategy):
    def do_algorithm(self, data: List[str]) -> List[str]:
        return sorted(data, reverse=True)


class Context:
    def do_some_business_logic(self, strategy: Strategy) -> None:
        result = strategy.do_algorithm(["a", "b", "c", "d", "e"])
        print(",".join(result))
```

### 模版方法模式

```php
from abc import ABC, abstractmethod


class AbstractClass(ABC):
    def template_method(self) -> None:
        self.base_operation1()
        self.required_operation1()
        self.base_operation2()
        self.hook1()
        self.required_operation2()
        self.base_operation3()
        self.hook2()

    def base_operation1(self) -> None:
        print("AbstractClass: base_operation1")

    def base_operation2(self) -> None:
        print("AbstractClass: base_operation2")

    def base_operation3(self) -> None:
        print("AbstractClass: base_operation3")

    @abstractmethod
    def required_operation1(self) -> None:
        pass

    @abstractmethod
    def required_operation2(self) -> None:
        pass

    def hook1(self) -> None:
        pass

    def hook2(self) -> None:
        pass


class ConcreteClass1(AbstractClass):
    def required_operation1(self) -> None:
        print("ConcreteClass1: required_operation1")

    def required_operation2(self) -> None:
        print("ConcreteClass1: required_operation2")


class ConcreteClass2(AbstractClass):
    def required_operation1(self) -> None:
        print("ConcreteClass2: required_operation1")

    def required_operation2(self) -> None:
        print("ConcreteClass2: required_operation2")

    def hook1(self) -> None:
        print("ConcreteClass2: hook1")
```

### 访问者模式

```php
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Component(ABC):
    @abstractmethod
    def accept(self, visitor: Visitor) -> None:
        pass


class ConcreteComponentA(Component):
    def accept(self, visitor: Visitor) -> None:
        visitor.visit_concrete_component_a(self)


class ConcreteComponentB(Component):
    def accept(self, visitor: Visitor):
        visitor.visit_concrete_component_b(self)


class Visitor(ABC):

    @abstractmethod
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        pass

    @abstractmethod
    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        pass


class ConcreteVisitor1(Visitor):
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        print("ConcreteVisitor1: visit_concrete_component_a")

    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        print("ConcreteVisitor1: visit_concrete_component_b")


class ConcreteVisitor2(Visitor):
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        print("ConcreteVisitor2: visit_concrete_component_a")

    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        print("ConcreteVisitor2: visit_concrete_component_b")
```
