# 设计模式：C++ 实现

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

class Product1 {
public:
    void AddPart(const std::string &part) {
        _parts.emplace_back(part);
    }

private:
    std::vector<std::string> _parts;
};

class ConcreteBuilder1 : public Builder {
public:
    ConcreteBuilder1() {
        Reset();
    }

    ~ConcreteBuilder1() override {
        delete _product;
    }

    void ProducePartA() const override {
        _product->AddPart("PartA1");
    }

    void ProducePartB() const override {
        _product->AddPart("PartB1");
    }

    void ProducePartC() const override {
        _product->AddPart("PartC1");
    }

    Product1 *GetProduct() {
        Product1 *result = _product;
        Reset();

        return result;
    }

private:
    void Reset() {
        _product = new Product1();
    }

    Product1 *_product;
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
