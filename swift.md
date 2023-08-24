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

    func someOperation() -> String
}

extension Creator {

    func someOperation() -> String {
        let product = factoryMethod()
        return product.operation()
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

    func operation() -> String
}

class ConcreteProduct1: Product {

    func operation() -> String {
        return "ConcreteProduct1"
    }
}

class ConcreteProduct2: Product {

    func operation() -> String {
        return "ConcreteProduct2"
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

    func usefulFunctionA() -> String
}

class ConcreteProductA1: AbstractProductA {

    func usefulFunctionA() -> String {
        return "ConcreteProductA1"
    }
}

class ConcreteProductA2: AbstractProductA {

    func usefulFunctionA() -> String {
        return "ConcreteProductA2"
    }
}

protocol AbstractProductB {

    func usefulFunctionB() -> String

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String
}

class ConcreteProductB1: AbstractProductB {

    func usefulFunctionB() -> String {
        return "ConcreteProductB1"
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String {
        return "ConcreteProductB1" + collaborator.usefulFunctionA()
    }
}

class ConcreteProductB2: AbstractProductB {

    func usefulFunctionB() -> String {
        return "ConcreteProductB2"
    }

    func anotherUsefulFunctionB(collaborator: AbstractProductA) -> String {
        return "ConcreteProductB2" + collaborator.usefulFunctionA()
    }
}
```

### 生成器模式

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
        product.add(part: "PartA")
    }

    func producePartB() {
        product.add(part: "PartB")
    }

    func producePartC() {
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
        builder.producePartA()
    }

    static func buildFullFeaturedProduct(_ builder: Builder) {
        builder.producePartA()
        builder.producePartB()
        builder.producePartC()
    }
}
```

### 原型模式

```swift
from __future__ import annotations
import copy
from typing import List, Optional, Dict


class Component:
    def __init__(self, some_int: int, some_list_of_objects: List) -> None:
        self.some_int = some_int
        self.some_list_of_objects = some_list_of_objects

    def __copy__(self) -> Component:
        some_list_of_objects = copy.copy(self.some_list_of_objects)
        new = self.__class__(self.some_int, some_list_of_objects)

        return new

    def __deepcopy__(self, memo: Optional[Dict] = None) -> Component:
        if memo is None:
            memo = {}

        some_list_of_objects = copy.deepcopy(self.some_list_of_objects, memo)
        new = self.__class__(self.some_int, some_list_of_objects)

        return new
```

### 单例模式

```swift
from __future__ import annotations
from typing import Dict, Any


class SingletonMeta(type):
    _instances: Dict[SingletonMeta, Any] = {}

    def __call__(cls, *args, **kwargs):
        if cls not in SingletonMeta._instances:
            instance = super().__call__(*args, **kwargs)
            SingletonMeta._instances[cls] = instance
        return SingletonMeta._instances[cls]


class Singleton(metaclass=SingletonMeta):
    def some_business_logic(self):
        print("Singleton: some_business_logic")
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
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List, Optional


class Component(ABC):
    def __init__(self) -> None:
        self._parent: Optional[Component] = None

    @property
    def parent(self) -> Optional[Component]:
        return self._parent

    @parent.setter
    def parent(self, parent: Component) -> None:
        self._parent = parent

    def add(self, component: Component) -> None:
        pass

    def remove(self, component: Component) -> None:
        pass

    def is_composite(self) -> bool:
        return False

    @abstractmethod
    def operation(self) -> str:
        pass


class Leaf(Component):

    def operation(self) -> str:
        return "Leaf: operation"


class Composite(Component):

    def __init__(self) -> None:
        super().__init__()
        self._children: List[Component] = []

    def add(self, component: Component) -> None:
        self._children.append(component)
        component.parent = self

    def remove(self, component: Component) -> None:
        self._children.remove(component)
        component.parent = None

    def is_composite(self) -> bool:
        return True

    def operation(self) -> str:
        results = []
        for child in self._children:
            results.append(child.operation())
        return ",".join(results)
```

### 装饰模式

```swift
from abc import ABC, abstractmethod


class Component(ABC):
    @abstractmethod
    def operation(self) -> str:
        pass


class ConcreteComponent(Component):
    def operation(self) -> str:
        return "ConcreteComponent: operation"


class Decorator(Component):
    def __init__(self, component: Component) -> None:
        self._component = component

    @abstractmethod
    def operation(self) -> str:
        return self._component.operation()


class ConcreteDecoratorA(Decorator):
    def operation(self) -> str:
        super().operation()
        return "ConcreteDecoratorA: operation"


class ConcreteDecoratorB(Decorator):
    def operation(self) -> str:
        super().operation()
        return "ConcreteDecoratorB: operation"
```

### 享元模式

```swift
from typing import Dict, List


class Flyweight:
    def __init__(self, shared_state: List[str]) -> None:
        self._shared_state = shared_state

    def operation(self, unique_state: List[str]) -> None:
        print(f"Flyweight: operation ({self._shared_state}) ({unique_state})")


class FlyweightFactory:
    def __init__(self, flyweights: List[List[str]]) -> None:
        self._flyweights: Dict[str, Flyweight] = {}
        for state in flyweights:
            self._flyweights[self._get_key(state)] = Flyweight(state)

    def get_flyweight(self, shared_state: List[str]) -> Flyweight:
        key = self._get_key(shared_state)
        if not self._flyweights.get(key):
            self._flyweights[key] = Flyweight(shared_state)
        return self._flyweights[key]

    def _get_key(self, state: List[str]) -> str:
        return "_".join(state)
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
        self.nextHandler = handler
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
            return "ConcreteHandler1: \(request)"
        } else {
            return nextHandler?.handle(request: request)
        }
    }
}

class ConcreteHandler2: Handler {

    var nextHandler: Handler?

    func handle(request: String) -> String? {
        if request == "request2" {
            return "ConcreteHandler2: \(request)"
        } else {
            return nextHandler?.handle(request: request)
        }
    }
}

class ConcreteHandler3: Handler {

    var nextHandler: Handler?

    func handle(request: String) -> String? {
        if request == "request3" {
            return "ConcreteHandler3: \(request)"
        } else {
            return nextHandler?.handle(request: request)
        }
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

    func notify(sender: Component, event: String)
}

class ConcreteMediator: Mediator {

    private var component1: ConcreteComponent1
    private var component2: ConcreteComponent2

    init(_ component1: ConcreteComponent1, _ component2: ConcreteComponent2) {
        self.component1 = component1
        self.component2 = component2
        component1.update(mediator: self)
        component2.update(mediator: self)
    }

    func notify(sender: Component, event: String) {
        if event == "A" {
            self.component2.doC()
        } else if (event == "D") {
            self.component1.doB()
            self.component2.doC()
        }
    }
}

class Component {

    fileprivate weak var mediator: Mediator?

    init(mediator: Mediator? = nil) {
        self.mediator = mediator
    }

    func update(mediator: Mediator) {
        self.mediator = mediator
    }
}

class ConcreteComponent1: Component {

    func doA() {
        print("ConcreteComponent1: doA")
        mediator?.notify(sender: self, event: "A")
    }

    func doB() {
        print("ConcreteComponent1: doA")
        mediator?.notify(sender: self, event: "B")
    }
}

class ConcreteComponent2: Component {

    func doC() {
        print("ConcreteComponent2: doC")
        mediator?.notify(sender: self, event: "C")
    }

    func doD() {
        print("ConcreteComponent2: doD")
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
