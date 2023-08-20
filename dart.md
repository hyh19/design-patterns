<!-- omit in toc -->
# 设计模式：Dart 实现

- [抽象工厂模式](#抽象工厂模式)
- [生成器模式](#生成器模式)
- [工厂方法模式](#工厂方法模式)
- [适配器模式](#适配器模式)

## 抽象工厂模式

```dart
abstract class AbstractFactory {
  AbstractProductA createProductA();

  AbstractProductB createProductB();
}

class ConcreteFactory1 implements AbstractFactory {
  @override
  AbstractProductA createProductA() {
    return ConcreteProductA1();
  }

  @override
  AbstractProductB createProductB() {
    return ConcreteProductB1();
  }
}

class ConcreteFactory2 implements AbstractFactory {
  @override
  AbstractProductA createProductA() {
    return ConcreteProductA2();
  }

  @override
  AbstractProductB createProductB() {
    return ConcreteProductB2();
  }
}

abstract class AbstractProductA {
  String usefulFunctionA();
}

class ConcreteProductA1 implements AbstractProductA {
  @override
  String usefulFunctionA() {
    return "ConcreteProductA1";
  }
}

class ConcreteProductA2 implements AbstractProductA {
  @override
  String usefulFunctionA() {
    return "ConcreteProductA2";
  }
}

abstract class AbstractProductB {
  String usefulFunctionB();

  String anotherUsefulFunctionB(AbstractProductA collaborator);
}

class ConcreteProductB1 implements AbstractProductB {
  @override
  String usefulFunctionB() {
    return "ConcreteProductB1";
  }

  @override
  String anotherUsefulFunctionB(AbstractProductA collaborator) {
    return "ConcreteProductB1${collaborator.usefulFunctionA()}";
  }
}

class ConcreteProductB2 implements AbstractProductB {
  @override
  String usefulFunctionB() {
    return "ConcreteProductB2";
  }

  @override
  String anotherUsefulFunctionB(AbstractProductA collaborator) {
    return "ConcreteProductB2${collaborator.usefulFunctionA()}";
  }
}
```

## 生成器模式

```dart
public interface Builder {
    void producePartA();

    void producePartB();

    void producePartC();
}
```

```dart
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

```dart
import java.util.ArrayList;
import java.util.List;

public class Product {
    private final List<String> parts = new ArrayList<>();

    void addPart(String part) {
        parts.add(part);
    }
}
```

```dart
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

```dart
public abstract class Creator {
    public abstract Product factoryMethod();

    public String someOperation() {
        Product product = factoryMethod();
        return product.operation();
    }
}
```

```dart
public class ConcreteCreator1 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct1();
    }
}
```

```dart
public class ConcreteCreator2 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct2();
    }
}
```

```dart
public interface Product {
    String operation();
}
```

```dart
public class ConcreteProduct1 implements Product {
    @Override
    public String operation() {
        return "ConcreteProduct1";
    }
}
```

```dart
public class ConcreteProduct2 implements Product {
    @Override
    public String operation() {
        return "ConcreteProduct2";
    }
}
```

## 适配器模式

```dart
public class Target {
    public String request() {
        return "Target";
    }
}
```

```dart
public class Adaptee {
    public String specificRequest() {
        return "Adaptee";
    }
}
```

```dart
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
