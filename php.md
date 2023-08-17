<!-- omit in toc -->
# 设计模式：PHP 实现

- [抽象工厂模式](#抽象工厂模式)
- [生成器模式](#生成器模式)
- [工厂方法模式](#工厂方法模式)
- [适配器模式](#适配器模式)

## 抽象工厂模式

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

## 生成器模式

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

## 工厂方法模式

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

## 适配器模式

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
