# 设计模式：PHP 实现

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

class ConcreteBuilder1 implements Builder
{
    /**
     * @var Product1
     */
    private Product1 $product;
    
    public function __construct()
    {
        $this->reset();
    }

    /**
     * @return void
     */
    public function producePartA(): void
    {
        $this->product->addPart("PartA1");
    }

    /**
     * @return void
     */
    public function producePartB(): void
    {
        $this->product->addPart("PartB1");
    }

    /**
     * @return void
     */
    public function producePartC(): void
    {
        $this->product->addPart("PartC1");
    }

    /**
     * @return Product1
     */
    public function getProduct(): Product1
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
        $this->product = new Product1();
    }
}

class Product1
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
