<!-- omit in toc -->
# 设计模式：C++ 实现

- [抽象工厂模式](#抽象工厂模式)
- [生成器模式](#生成器模式)
- [工厂方法模式](#工厂方法模式)
- [适配器模式](#适配器模式)

## 抽象工厂模式

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

## 生成器模式

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

## 工厂方法模式

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

## 适配器模式

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
