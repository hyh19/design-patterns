# 设计模式：Swift 实现

## 抽象工厂模式

```swift
protocol AbstractFactory {

    func createProductA() -> AbstractProductA

    func createProductB() -> AbstractProductB
}

class ConcreteFactory1: AbstractFactory {

    func createProductA() -> AbstractProductA {
        return ConcreteProductA1()
    }

    func createProductB() -> AbstractProductB {
        return ConcreteProductB1()
    }
}

class ConcreteFactory2: AbstractFactory {

    func createProductA() -> AbstractProductA {
        return ConcreteProductA2()
    }

    func createProductB() -> AbstractProductB {
        return ConcreteProductB2()
    }
}

protocol AbstractProductA {

    func usefulFunctionA() -> String
}

class ConcreteProductA1: AbstractProductA {

    func usefulFunctionA() -> String {
        return "A1"
    }
}

class ConcreteProductA2: AbstractProductA {

    func usefulFunctionA() -> String {
        return "A2"
    }
}

protocol AbstractProductB {

    func usefulFunctionB() -> String

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String
}

class ConcreteProductB1: AbstractProductB {

    func usefulFunctionB() -> String {
        return "B1"
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String {
        return "B1" + collaborator.usefulFunctionA()
    }
}

class ConcreteProductB2: AbstractProductB {

    func usefulFunctionB() -> String {
        return "B2"
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String {
        return "B2" + collaborator.usefulFunctionA()
    }
}
```

## 生成器模式

```swift
protocol Builder {

    func producePartA()

    func producePartB()

    func producePartC()
}

class ConcreteBuilder: Builder {

    private var product: Product!

    init() {
        reset()
    }

    func producePartA() {
        product.add(part: "PartA1")
    }

    func producePartB() {
        product.add(part: "PartB1")
    }

    func producePartC() {
        product.add(part: "PartC1")
    }

    func getResult() -> Product {
        let result = product
        reset()
        return result!
    }

    private func reset() {
        product = Product()
    }
}

class Product {

    private var parts: [String] = []

    func add(part: String) {
        parts.append(part)
    }
}

class Director {

    static func buildMinimalViableProduct(_ builder: Builder) {
        builder.producePartA()
    }

    static func buildFullFeaturedProduct(_ builder: Builder) {
        builder.producePartA()
        builder.producePartB()
        builder.producePartC()
    }
}
```
