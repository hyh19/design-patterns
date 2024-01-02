<!-- omit in toc -->
# 设计模式：Swift 实现

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

```swift
protocol Creator {

    func factoryMethod() -> Product

    func someOperation()
}

extension Creator {

    func someOperation() {
        let product = factoryMethod()
        product.operation()
    }
}

class ConcreteCreator1: Creator {

    func factoryMethod() -> Product {
        return ConcreteProduct1()
    }
}

class ConcreteCreator2: Creator {

    func factoryMethod() -> Product {
        return ConcreteProduct2()
    }
}

protocol Product {

    func operation()
}

class ConcreteProduct1: Product {

    func operation() {
        print("ConcreteProduct1: operation")
    }
}

class ConcreteProduct2: Product {

    func operation() {
        print("ConcreteProduct2: operation")
    }
}
```

### 抽象工厂模式

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

    func usefulFunctionA()
}

class ConcreteProductA1: AbstractProductA {

    func usefulFunctionA() {
        print("ConcreteProductA1: usefulFunctionA")
    }
}

class ConcreteProductA2: AbstractProductA {

    func usefulFunctionA() {
        print("ConcreteProductA2: usefulFunctionA")
    }
}

protocol AbstractProductB {

    func usefulFunctionB()

    func anotherUsefulFunctionB(collaborator: AbstractProductA)
}

class ConcreteProductB1: AbstractProductB {

    func usefulFunctionB() {
        print("ConcreteProductB1: usefulFunctionB")
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) {
        collaborator.usefulFunctionA()
        print("ConcreteProductB1: anotherUsefulFunctionB")
    }
}

class ConcreteProductB2: AbstractProductB {

    func usefulFunctionB() {
        print("ConcreteProductB2: usefulFunctionB")
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) {
        collaborator.usefulFunctionA()
        print("ConcreteProductB2: anotherUsefulFunctionB")
    }
}
```

### 生成器模式

```swift
protocol Builder {

    func buildPartA()

    func buildPartB()

    func buildPartC()
}

class ConcreteBuilder: Builder {

    private var product: Product!

    init() {
        reset()
    }

    func buildPartA() {
        product.add(part: "PartA")
    }

    func buildPartB() {
        product.add(part: "PartB")
    }

    func buildPartC() {
        product.add(part: "PartC")
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
        builder.buildPartA()
    }

    static func buildFullFeaturedProduct(_ builder: Builder) {
        builder.buildPartA()
        builder.buildPartB()
        builder.buildPartC()
    }
}
```

### 原型模式

```swift
import Foundation

class BaseClass: NSCopying, Equatable {

    private var intValue = 1
    private var stringValue = "Value"

    required init(intValue: Int = 1, stringValue: String = "Value") {
        self.intValue = intValue
        self.stringValue = stringValue
    }

    func copy(with zone: NSZone? = nil) -> Any {
        let prototype = type(of: self).init()
        prototype.intValue = intValue
        prototype.stringValue = stringValue
        return prototype
    }

    static func ==(lhs: BaseClass, rhs: BaseClass) -> Bool {
        return lhs.intValue == rhs.intValue && lhs.stringValue == rhs.stringValue
    }
}

class SubClass: BaseClass {

    private var boolValue = true

    func copy() -> Any {
        return copy(with: nil)
    }

    override func copy(with zone: NSZone?) -> Any {
        guard let prototype = super.copy(with: zone) as? SubClass else {
            return SubClass()
        }
        prototype.boolValue = boolValue
        return prototype
    }
}
```

### 单例模式

```swift
fimport Foundation

class Singleton {

    static let shared: Singleton = {
        let instance = Singleton()
        return instance
    }()

    private init() {
    }
}

extension Singleton: NSCopying {

    func copy(with zone: NSZone? = nil) -> Any {
        return self
    }
}
```

## 结构型模式

### 适配器模式

```swift
class Target {

    func request() -> String {
        return "Target"
    }
}

class Adaptee {

    public func specificRequest() -> String {
        return "Adaptee"
    }
}

class Adapter: Target {

    private var adaptee: Adaptee

    init(_ adaptee: Adaptee) {
        self.adaptee = adaptee
    }

    override func request() -> String {
        return "Adapter" + adaptee.specificRequest()
    }
}
```

### 桥接模式

```swift
class Abstraction {

    fileprivate var implementation: Implementation

    init(_ implementation: Implementation) {
        self.implementation = implementation
    }

    func operation() -> String {
        return implementation.operationImplementation()
    }
}

class ExtendedAbstraction: Abstraction {

    override func operation() -> String {
        return "ExtendedAbstraction" + super.operation()
    }
}

protocol Implementation {

    func operationImplementation() -> String
}

class ConcreteImplementationA: Implementation {

    func operationImplementation() -> String {
        return "ConcreteImplementationA"
    }
}

class ConcreteImplementationB: Implementation {

    func operationImplementation() -> String {
        return "ConcreteImplementationB"
    }
}
```

### 组合模式

```swift
protocol Component {

    var parent: Component? { get set }

    func add(component: Component)
    
    func remove(component: Component)

    func isComposite() -> Bool

    func operation() -> String
}

extension Component {

    func add(component: Component) {
    }

    func remove(component: Component) {
    }

    func isComposite() -> Bool {
        return false
    }
}

class Leaf: Component {

    var parent: Component?

    func operation() -> String {
        return "Leaf: operation"
    }
}

class Composite: Component {

    var parent: Component?

    private var children = [Component]()

    func add(component: Component) {
        var item = component
        item.parent = self
        children.append(item)
    }

    func remove(component: Component) {
        // ...
    }

    func isComposite() -> Bool {
        return true
    }

    func operation() -> String {
        let result = children.map {
            $0.operation()
        }
        return result.joined(separator: " ")
    }
}
```

### 装饰模式

```swift
protocol Component {

    func operation() -> String
}

class ConcreteComponent: Component {

    func operation() -> String {
        return "ConcreteComponent: operation"
    }
}

class Decorator: Component {

    private var component: Component

    init(_ component: Component) {
        self.component = component
    }

    func operation() -> String {
        return component.operation()
    }
}

class ConcreteDecoratorA: Decorator {

    override func operation() -> String {
        return "ConcreteDecoratorA: operation" + super.operation()
    }
}

class ConcreteDecoratorB: Decorator {

    override func operation() -> String {
        return "ConcreteDecoratorB: operation" + super.operation()
    }
}
```

### 享元模式

```swift
class Flyweight {

    private let sharedState: [String]

    init(sharedState: [String]) {
        self.sharedState = sharedState
    }

    func operation(uniqueState: [String]) {
        print("Flyweight: operation (\(sharedState)) (\(uniqueState))")
    }
}

class FlyweightFactory {

    private var flyweights = [String: Flyweight]()

    init(states: [[String]]) {
        for state in states {
            flyweights[state.key] = Flyweight(sharedState: state)
        }
    }

    func flyweight(for state: [String]) -> Flyweight {
        let key = state.key
        guard let foundFlyweight = flyweights[key] else {
            let flyweight = Flyweight(sharedState: state)
            flyweights.updateValue(flyweight, forKey: key)
            return flyweight
        }

        return foundFlyweight
    }
}

extension Array where Element == String {

    var key: String {
        return self.joined()
    }
}
```

### 代理模式

```swift
protocol Subject {

    func request()
}

class RealSubject: Subject {

    func request() {
        print("RealSubject: request")
    }
}

class Proxy: Subject {

    private var realSubject: RealSubject

    init(_ realSubject: RealSubject) {
        self.realSubject = realSubject
    }

    func request() {
        print("Proxy: request")
        realSubject.request()
    }
}
```

## 行为模式

### 责任链模式

```swift
protocol Handler: AnyObject {

    var nextHandler: Handler? { get set }

    @discardableResult
    func setNext(handler: Handler) -> Handler

    func handle(request: String) -> String?
}

extension Handler {

    func setNext(handler: Handler) -> Handler {
        nextHandler = handler
        return handler
    }

    func handle(request: String) -> String? {
        return nextHandler?.handle(request: request)
    }
}

class ConcreteHandler1: Handler {

    var nextHandler: Handler?

    func handle(request: String) -> String? {
        if request == "request1" {
            return "ConcreteHandler1: handle \(request)"
        }
        return nextHandler?.handle(request: request)
    }
}

class ConcreteHandler2: Handler {

    var nextHandler: Handler?

    func handle(request: String) -> String? {
        if request == "request2" {
            return "ConcreteHandler2: handle \(request)"
        }
        return nextHandler?.handle(request: request)
    }
}
```

### 命令模式

```swift
protocol Command {

    func execute()
}

class SimpleCommand: Command {

    private var payload: String

    init(_ payload: String) {
        self.payload = payload
    }

    func execute() {
        print("SimpleCommand: execute (\(payload))")
    }
}

class ComplexCommand: Command {

    private var receiver: Receiver
    private var a: String
    private var b: String

    init(_ receiver: Receiver, _ a: String, _ b: String) {
        self.receiver = receiver
        self.a = a
        self.b = b
    }

    func execute() {
        print("ComplexCommand: execute")
        receiver.doSomethingA(a)
        receiver.doSomethingB(b)
    }
}

class Receiver {

    func doSomethingA(_ a: String) {
        print("Receiver: doSomethingA (\(a)")
    }

    func doSomethingB(_ b: String) {
        print("Receiver: doSomethingB (\(b))")
    }
}

class Invoker {

    private var onStart: Command?
    private var onFinish: Command?

    func setOnStart(_ command: Command) {
        onStart = command
    }

    func setOnFinish(_ command: Command) {
        onFinish = command
    }

    func doSomething() {
        onStart?.execute()
        onFinish?.execute()
    }
}
```

### 迭代器模式

```swift
class WordsCollection {

    fileprivate lazy var items = [String]()

    func append(_ item: String) {
        self.items.append(item)
    }
}

extension WordsCollection: Sequence {

    func makeIterator() -> WordsIterator {
        return WordsIterator(self)
    }
}

class WordsIterator: IteratorProtocol {

    private let collection: WordsCollection
    private var index = 0

    init(_ collection: WordsCollection) {
        self.collection = collection
    }

    func next() -> String? {
        defer {
            index += 1
        }
        return index < collection.items.count ? collection.items[index] : nil
    }
}
```

### 中介者模式

```swift
protocol Mediator: AnyObject {

    func notify(sender: Colleague, event: String)
}

class ConcreteMediator: Mediator {

    private var colleague1: ConcreteColleague1
    private var colleague2: ConcreteColleague2

    init(_ colleague1: ConcreteColleague1, _ colleague2: ConcreteColleague2) {
        self.colleague1 = colleague1
        self.colleague2 = colleague2
        colleague1.update(mediator: self)
        colleague2.update(mediator: self)
    }

    func notify(sender: Colleague, event: String) {
        if event == "A" {
            colleague2.doC()
        } else if (event == "D") {
            colleague1.doB()
            colleague2.doC()
        }
    }
}

class Colleague {

    fileprivate weak var mediator: Mediator?

    init(mediator: Mediator? = nil) {
        self.mediator = mediator
    }

    func update(mediator: Mediator) {
        self.mediator = mediator
    }
}

class ConcreteColleague1: Colleague {

    func doA() {
        print("ConcreteColleague1: doA")
        mediator?.notify(sender: self, event: "A")
    }

    func doB() {
        print("ConcreteColleague1: doA")
        mediator?.notify(sender: self, event: "B")
    }
}

class ConcreteColleague2: Colleague {

    func doC() {
        print("ConcreteColleague2: doC")
        mediator?.notify(sender: self, event: "C")
    }

    func doD() {
        print("ConcreteColleague2: doD")
        mediator?.notify(sender: self, event: "D")
    }
}
```

### 备忘录模式

```swift
class Originator {

    private var state: String

    init(state: String) {
        self.state = state
    }

    func save() -> Memento {
        return Memento(state: state)
    }

    func restore(memento: Memento) {
        self.state = memento.state
    }
}

class Memento {

    private(set) var state: String

    init(state: String) {
        self.state = state
    }
}

class Caretaker {

    private lazy var mementos = [Memento]()
    private var originator: Originator

    init(originator: Originator) {
        self.originator = originator
    }

    func backup() {
        mementos.append(originator.save())
    }

    func undo() {
        guard !mementos.isEmpty else {
            return
        }
        originator.restore(memento: mementos.removeLast())
    }
}
```

### 观察者模式

```swift
class Subject {

    var state: Int = {
        return Int(arc4random_uniform(10))
    }()

    private lazy var observers = [Observer]()

    func attach(_ observer: Observer) {
        observers.append(observer)
    }

    func detach(_ observer: Observer) {
        if let idx = observers.firstIndex(where: { $0 === observer }) {
            observers.remove(at: idx)
        }
    }

    func notify() {
        observers.forEach {
            $0.update(subject: self)
        }
    }

    func someBusinessLogic() {
        state = Int(arc4random_uniform(10))
        notify()
    }
}

protocol Observer: AnyObject {

    func update(subject: Subject)
}

class ConcreteObserverA: Observer {

    func update(subject: Subject) {
        print("ConcreteObserverA: update")
    }
}

class ConcreteObserverB: Observer {

    func update(subject: Subject) {
        print("ConcreteObserverB: update")
    }
}
```

### 状态模式

```swift
class Context {

    private var state: State

    init(_ state: State) {
        self.state = state
        transitionTo(state: state)
    }

    func transitionTo(state: State) {
        self.state = state
        self.state.update(context: self)
    }

    func request1() {
        state.handle1()
    }

    func request2() {
        state.handle2()
    }
}

protocol State: AnyObject {

    func update(context: Context)

    func handle1()
    func handle2()
}

class BaseState: State {

    private(set) weak var context: Context?

    func update(context: Context) {
        self.context = context
    }

    func handle1() {
    }

    func handle2() {
    }
}

class ConcreteStateA: BaseState {

    override func handle1() {
        print("ConcreteStateA: handle1")
        context?.transitionTo(state: ConcreteStateB())
    }

    override func handle2() {
        print("ConcreteStateA: handle2")
    }
}

class ConcreteStateB: BaseState {

    override func handle1() {
        print("ConcreteStateB: handle1")
    }

    override func handle2() {
        print("ConcreteStateB: handle2")
        context?.transitionTo(state: ConcreteStateA())
    }
}
```

### 策略模式

```swift
class Context {

    func doSomething(strategy: Strategy) {
        let result = strategy.doAlgorithm(["a", "b", "c", "d", "e"])
        print(result.joined(separator: ","))
    }
}

protocol Strategy {

    func doAlgorithm<T: Comparable>(_ data: [T]) -> [T]
}

class ConcreteStrategyA: Strategy {

    func doAlgorithm<T: Comparable>(_ data: [T]) -> [T] {
        return data.sorted()
    }
}

class ConcreteStrategyB: Strategy {

    func doAlgorithm<T: Comparable>(_ data: [T]) -> [T] {
        return data.sorted(by: >)
    }
}
```

### 模版方法模式

```swift
protocol AbstractClass {

    func templateMethod()

    func baseOperation1()
    func baseOperation2()
    func baseOperation3()

    func requiredOperation1()
    func requiredOperation2()

    func hook1()
    func hook2()
}

extension AbstractClass {

    func templateMethod() {
        baseOperation1()
        requiredOperation1()
        baseOperation2()
        hook1()
        requiredOperation2()
        baseOperation3()
        hook2()
    }

    func baseOperation1() {
        print("AbstractClass: baseOperation1")
    }

    func baseOperation2() {
        print("AbstractClass: baseOperation2")
    }

    func baseOperation3() {
        print("AbstractClass: baseOperation3")
    }

    func hook1() {
    }

    func hook2() {
    }
}

class ConcreteClass1: AbstractClass {

    func requiredOperation1() {
        print("ConcreteClass1: requiredOperation1")
    }

    func requiredOperation2() {
        print("ConcreteClass1: requiredOperation2")
    }

    func hook2() {
        print("ConcreteClass1: hook2")
    }
}

class ConcreteClass2: AbstractClass {

    func requiredOperation1() {
        print("ConcreteClass2: requiredOperation1")
    }

    func requiredOperation2() {
        print("ConcreteClass2: requiredOperation2")
    }

    func hook1() {
        print("ConcreteClass2: hook1")
    }
}
```

### 访问者模式

```swift
protocol Component {

    func accept(_ visitor: Visitor)
}

class ConcreteComponentA: Component {

    func accept(_ visitor: Visitor) {
        visitor.visitConcreteComponentA(element: self)
    }
}

class ConcreteComponentB: Component {

    func accept(_ visitor: Visitor) {
        visitor.visitConcreteComponentB(element: self)
    }
}

protocol Visitor {

    func visitConcreteComponentA(element: ConcreteComponentA)
    func visitConcreteComponentB(element: ConcreteComponentB)
}

class ConcreteVisitor1: Visitor {

    func visitConcreteComponentA(element: ConcreteComponentA) {
        print("ConcreteVisitor1: visitConcreteComponentA")
    }

    func visitConcreteComponentB(element: ConcreteComponentB) {
        print("ConcreteVisitor1: visitConcreteComponentB")
    }
}

class ConcreteVisitor2: Visitor {

    func visitConcreteComponentA(element: ConcreteComponentA) {
        print("ConcreteVisitor2: visitConcreteComponentA")
    }

    func visitConcreteComponentB(element: ConcreteComponentB) {
        print("ConcreteVisitor2: visitConcreteComponentB")
    }
}
```
