<!-- omit in toc -->
# 设计模式：Java 实现

- [抽象工厂模式](#抽象工厂模式)
- [生成器模式](#生成器模式)
- [工厂方法模式](#工厂方法模式)
- [适配器模式](#适配器模式)

## 抽象工厂模式

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

## 生成器模式

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

## 工厂方法模式

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

## 适配器模式

```java
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
