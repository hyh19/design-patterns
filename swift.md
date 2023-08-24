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
from abc import ABC, abstractmethod


class Subject(ABC):
    @abstractmethod
    def request(self) -> None:
        pass


class RealSubject(Subject):
    def request(self) -> None:
        print("RealSubject: request")


class Proxy(Subject):
    def __init__(self, real_subject: RealSubject) -> None:
        self._real_subject = real_subject

    def request(self) -> None:
        print("Proxy: request")
        self._real_subject.request()
```

## 行为模式

### 责任链模式

```swift
from __future__ import annotations

from abc import ABC, abstractmethod
from typing import Optional


class Handler(ABC):
    @abstractmethod
    def set_next(self, handler: Handler) -> Handler:
        pass

    @abstractmethod
    def handle(self, request: str) -> Optional[str]:
        pass


class BaseHandler(Handler):
    def __init__(self) -> None:
        self._next_handler: Optional[Handler] = None

    def set_next(self, handler: Handler) -> Handler:
        self._next_handler = handler
        return handler

    @abstractmethod
    def handle(self, request: str) -> Optional[str]:
        if self._next_handler:
            return self._next_handler.handle(request)

        return None


class ConcreteHandler1(BaseHandler):
    def handle(self, request: str) -> Optional[str]:
        if request == "request1":
            return f"ConcreteHandler1: {request}"
        else:
            return super().handle(request)


class ConcreteHandler2(BaseHandler):
    def handle(self, request: str) -> Optional[str]:
        if request == "request2":
            return f"ConcreteHandler2: {request}"
        else:
            return super().handle(request)


class ConcreteHandler3(BaseHandler):
    def handle(self, request: str) -> Optional[str]:
        if request == "request3":
            return f"ConcreteHandler3: {request}"
        else:
            return super().handle(request)
```

### 命令模式

```swift
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import Optional


class Command(ABC):
    @abstractmethod
    def execute(self) -> None:
        pass


class SimpleCommand(Command):
    def __init__(self, payload: str) -> None:
        self._payload = payload

    def execute(self) -> None:
        print(f"SimpleCommand: execute ({self._payload})")


class ComplexCommand(Command):
    def __init__(self, receiver: Receiver, a: str, b: str) -> None:
        self._receiver = receiver
        self._a = a
        self._b = b

    def execute(self) -> None:
        print("ComplexCommand: execute")
        self._receiver.do_something(self._a)
        self._receiver.do_something_else(self._b)


class Receiver:
    def do_something(self, a: str) -> None:
        print(f"Receiver: do_something ({a}.)")

    def do_something_else(self, b: str) -> None:
        print(f"Receiver: do_something_else ({b}.)")


class Invoker:
    def __init__(self) -> None:
        self._on_start: Optional[Command] = None
        self._on_finish: Optional[Command] = None

    def set_on_start(self, command: Command) -> None:
        self._on_start = command

    def set_on_finish(self, command: Command) -> None:
        self._on_finish = command

    def do_something_important(self) -> None:
        if isinstance(self._on_start, Command):
            self._on_start.execute()
        if isinstance(self._on_finish, Command):
            self._on_finish.execute()
```

### 迭代器模式

```swift
from __future__ import annotations

from collections.abc import Iterable, Iterator
from typing import List


class AlphabeticalOrderIterator(Iterator):
    def __init__(self, collection: WordsCollection, reverse: bool = False) -> None:
        self._collection = collection
        self._reverse = reverse
        self._position = -1 if reverse else 0

    def __next__(self) -> str:
        try:
            value = self._collection[self._position]
            self._position += -1 if self._reverse else 1
        except IndexError:
            raise StopIteration()

        return value


class WordsCollection(Iterable):
    def __init__(self, collection: List[str] = []) -> None:
        self._collection = collection

    def __iter__(self) -> AlphabeticalOrderIterator:
        return AlphabeticalOrderIterator(self)

    def __getitem__(self, index: int) -> str:
        return self._collection[index]

    def get_reverse_iterator(self) -> AlphabeticalOrderIterator:
        return AlphabeticalOrderIterator(self, True)

    def add_item(self, item: str) -> None:
        self._collection.append(item)
```

### 中介者模式

```swift
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import Optional


class Mediator(ABC):
    @abstractmethod
    def notify(self, sender: Component, event: str) -> None:
        pass


class ConcreteMediator(Mediator):
    def __init__(self, component1: ConcreteComponent1, component2: ConcreteComponent2) -> None:
        self._component1 = component1
        self._component2 = component2
        self._component1.mediator = self
        self._component2.mediator = self

    def notify(self, sender: Component, event: str) -> None:
        if event == "A":
            self._component2.do_c()
        elif event == "D":
            self._component1.do_b()
            self._component2.do_c()


class Component(ABC):
    def __init__(self, mediator: Optional[Mediator] = None) -> None:
        self._mediator = mediator

    @property
    def mediator(self) -> Optional[Mediator]:
        return self._mediator

    @mediator.setter
    def mediator(self, mediator: Mediator) -> None:
        self._mediator = mediator


class ConcreteComponent1(Component):
    def do_a(self) -> None:
        print("ConcreteComponent1: do_a")
        assert self.mediator is not None
        self.mediator.notify(self, "A")

    def do_b(self) -> None:
        print("ConcreteComponent1: do_b")
        assert self.mediator is not None
        self.mediator.notify(self, "B")


class ConcreteComponent2(Component):
    def do_c(self) -> None:
        print("ConcreteComponent2: do_c")
        assert self.mediator is not None
        self.mediator.notify(self, "C")

    def do_d(self) -> None:
        print("ConcreteComponent2: do_d")
        assert self.mediator is not None
        self.mediator.notify(self, "D")

```

### 备忘录模式

```swift
from __future__ import annotations
from random import sample
from string import ascii_letters
from typing import List


class Originator:
    def __init__(self, state: str) -> None:
        self._state = state

    def do_something(self) -> None:
        self._state = self._generate_random_string(30)

    def save(self) -> Memento:
        return Memento(self._state)

    def restore(self, memento: Memento) -> None:
        self._state = memento.state

    def _generate_random_string(self, length: int = 10) -> str:
        return "".join(sample(ascii_letters, length))


class Memento:
    def __init__(self, state: str) -> None:
        self._state = state

    @property
    def state(self) -> str:
        return self._state


class Caretaker:
    def __init__(self, originator: Originator) -> None:
        self._mementos: List[Memento] = []
        self._originator = originator

    def backup(self) -> None:
        self._mementos.append(self._originator.save())

    def undo(self) -> None:
        if not len(self._mementos):
            return

        self._originator.restore(self._mementos.pop())
```

### 观察者模式

```swift
from __future__ import annotations
from abc import ABC, abstractmethod
from random import randrange
from typing import List


class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def detach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def notify(self) -> None:
        pass


class ConcreteSubject(Subject):
    def __init__(self) -> None:
        self._state: int = randrange(0, 10)
        self._observers: List[Observer] = []

    def attach(self, observer: Observer) -> None:
        self._observers.append(observer)

    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)

    def notify(self) -> None:
        for observer in self._observers:
            observer.update(self._state)

    def some_business_logic(self) -> None:
        self._state = randrange(0, 10)
        self.notify()


class Observer(ABC):
    @abstractmethod
    def update(self, state: int) -> None:
        pass


class ConcreteObserverA(Observer):
    def update(self, state: int) -> None:
        print("ConcreteObserverA: update")


class ConcreteObserverB(Observer):
    def update(self, state: int) -> None:
        print("ConcreteObserverB: update")
```

### 状态模式

```swift
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import Optional


class Context:
    def __init__(self, state: State) -> None:
        self.transition_to(state)

    def transition_to(self, state: State) -> None:
        self._state = state
        self._state.context = self

    def request1(self) -> None:
        self._state.handle1()

    def request2(self) -> None:
        self._state.handle2()


class State(ABC):
    def __init__(self) -> None:
        self._context: Optional[Context] = None

    @property
    def context(self) -> Optional[Context]:
        return self._context

    @context.setter
    def context(self, context: Context) -> None:
        self._context = context

    @abstractmethod
    def handle1(self) -> None:
        pass

    @abstractmethod
    def handle2(self) -> None:
        pass


class ConcreteStateA(State):
    def handle1(self) -> None:
        print("ConcreteStateA handle1")
        assert self.context is not None
        self.context.transition_to(ConcreteStateB())

    def handle2(self) -> None:
        print("ConcreteStateA handle2")


class ConcreteStateB(State):
    def handle1(self) -> None:
        print("ConcreteStateB handle1")

    def handle2(self) -> None:
        print("ConcreteStateB handle2")
        assert self.context is not None
        self.context.transition_to(ConcreteStateA())
```

### 策略模式

```swift
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Strategy(ABC):
    @abstractmethod
    def do_algorithm(self, data: List[str]) -> List[str]:
        pass


class ConcreteStrategyA(Strategy):
    def do_algorithm(self, data: List[str]) -> List[str]:
        return sorted(data)


class ConcreteStrategyB(Strategy):
    def do_algorithm(self, data: List[str]) -> List[str]:
        return sorted(data, reverse=True)


class Context:
    def do_some_business_logic(self, strategy: Strategy) -> None:
        result = strategy.do_algorithm(["a", "b", "c", "d", "e"])
        print(",".join(result))
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
