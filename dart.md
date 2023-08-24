<!-- omit in toc -->
# 设计模式：Dart 实现

- [抽象工厂模式](#抽象工厂模式)
- [生成器模式](#生成器模式)
- [工厂方法模式](#工厂方法模式)
- [适配器模式](#适配器模式)
- [桥接模式](#桥接模式)
- [中介者模式](#中介者模式)

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
abstract class Builder {
  void producePartA();

  void producePartB();

  void producePartC();
}

class ConcreteBuilder implements Builder {
  late Product _product;

  ConcreteBuilder() {
    _reset();
  }

  @override
  void producePartA() {
    _product.addPart("PartA");
  }

  @override
  void producePartB() {
    _product.addPart("PartB");
  }

  @override
  void producePartC() {
    _product.addPart("PartC");
  }

  Product getResult() {
    Product result = _product;
    _reset();
    return result;
  }

  void _reset() {
    _product = Product();
  }
}

class Product {
  final List<String> _parts = [];

  void addPart(String part) {
    _parts.add(part);
  }
}

class Director {
  static void buildMinimalViableProduct(Builder builder) {
    builder.producePartA();
  }

  static void buildFullFeaturedProduct(Builder builder) {
    builder.producePartA();
    builder.producePartB();
    builder.producePartC();
  }
}
```

## 工厂方法模式

```dart
abstract class Creator {
  Product factoryMethod();

  String someOperation() {
    Product product = factoryMethod();
    return product.operation();
  }
}

class ConcreteCreator1 extends Creator {
  @override
  Product factoryMethod() {
    return ConcreteProduct1();
  }
}

class ConcreteCreator2 extends Creator {
  @override
  Product factoryMethod() {
    return ConcreteProduct2();
  }
}

abstract class Product {
  String operation();
}

class ConcreteProduct1 implements Product {
  @override
  String operation() {
    return "ConcreteProduct1";
  }
}

class ConcreteProduct2 implements Product {
  @override
  String operation() {
    return "ConcreteProduct2";
  }
}
```

## 适配器模式

```dart
class Target {
  String request() {
    return "Target";
  }
}

class Adaptee {
  String specificRequest() {
    return "Adaptee";
  }
}

class Adapter extends Target {
  final Adaptee _adaptee;

  Adapter(this._adaptee);

  @override
  String request() {
    return "Adapter${_adaptee.specificRequest()}";
  }
}
```

## 桥接模式

```dart
class Abstraction {
  final Implementation _implementation;

  Abstraction(this._implementation);

  String operation() {
    return _implementation.operationImplementation();
  }
}

class ExtendedAbstraction extends Abstraction {
  ExtendedAbstraction(super.implementation);

  @override
  String operation() {
    return "ExtendedAbstraction${super.operation()}";
  }
}

abstract class Implementation {
  String operationImplementation();
}

class ConcreteImplementationA implements Implementation {
  @override
  String operationImplementation() {
    return "ConcreteImplementationA";
  }
}

abstract class ConcreteImplementationB implements Implementation {
  @override
  String operationImplementation() {
    return "ConcreteImplementationB";
  }
}
```

## 中介者模式

