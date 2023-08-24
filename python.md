<!-- omit in toc -->
# 设计模式：Python 实现

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

```py
from __future__ import annotations
from abc import ABC, abstractmethod


class Creator(ABC):
    @abstractmethod
    def factory_method(self) -> Product:
        pass

    def some_operation(self) -> str:
        product = self.factory_method()
        return product.operation()


class ConcreteCreator1(Creator):
    def factory_method(self) -> Product:
        return ConcreteProduct1()


class ConcreteCreator2(Creator):
    def factory_method(self) -> Product:
        return ConcreteProduct2()


class Product(ABC):
    @abstractmethod
    def operation(self) -> str:
        pass


class ConcreteProduct1(Product):
    def operation(self) -> str:
        return "ConcreteProduct1: operation"


class ConcreteProduct2(Product):
    def operation(self) -> str:
        return "ConcreteProduct2: operation"
```

### 抽象工厂模式

```py
from __future__ import annotations
from abc import ABC, abstractmethod


class AbstractFactory(ABC):
    @abstractmethod
    def create_product_a(self) -> AbstractProductA:
        pass

    @abstractmethod
    def create_product_b(self) -> AbstractProductB:
        pass


class ConcreteFactory1(AbstractFactory):
    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA1()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB1()


class ConcreteFactory2(AbstractFactory):
    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA2()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB2()


class AbstractProductA(ABC):
    @abstractmethod
    def useful_function_a(self) -> str:
        pass


class ConcreteProductA1(AbstractProductA):
    def useful_function_a(self) -> str:
        return "ConcreteProductA1: useful_function_a"


class ConcreteProductA2(AbstractProductA):
    def useful_function_a(self) -> str:
        return "ConcreteProductA2: useful_function_a"


class AbstractProductB(ABC):
    @abstractmethod
    def useful_function_b(self) -> str:
        pass

    @abstractmethod
    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        pass


class ConcreteProductB1(AbstractProductB):
    def useful_function_b(self) -> str:
        return "ConcreteProductB1: useful_function_b"

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        collaborator.useful_function_a()
        return "ConcreteProductB1: another_useful_function_b"


class ConcreteProductB2(AbstractProductB):
    def useful_function_b(self) -> str:
        return "ConcreteProductB2: useful_function_b"

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        collaborator.useful_function_a()
        return "ConcreteProductB2: another_useful_function_b"
```

### 生成器模式

```py
from __future__ import annotations

from abc import ABC, abstractmethod
from typing import List


class Builder(ABC):
    @abstractmethod
    def produce_part_a(self) -> None:
        pass

    @abstractmethod
    def produce_part_b(self) -> None:
        pass

    @abstractmethod
    def produce_part_c(self) -> None:
        pass


class ConcreteBuilder(Builder):
    def __init__(self) -> None:
        self._reset()

    def produce_part_a(self) -> None:
        self._product.add_part("PartA")

    def produce_part_b(self) -> None:
        self._product.add_part("PartB")

    def produce_part_c(self) -> None:
        self._product.add_part("PartC")

    def get_result(self) -> Product:
        result = self._product
        self._reset()
        return result

    def _reset(self) -> None:
        self._product: Product = Product()


class Product:
    def __init__(self) -> None:
        self._parts: List[str] = []

    def add_part(self, part: str) -> None:
        self._parts.append(part)


class Director:
    @staticmethod
    def build_minimal_viable_product(builder: Builder) -> None:
        builder.produce_part_a()

    @staticmethod
    def build_full_featured_product(builder: Builder) -> None:
        builder.produce_part_a()
        builder.produce_part_b()
        builder.produce_part_c()
```

### 原型模式

```py
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

```py
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

```py
class Target:
    def request(self) -> str:
        return "Target: request"


class Adaptee:
    def specific_request(self) -> str:
        return "Adaptee: specific_request"


class Adapter(Target):
    def __init__(self, adaptee: Adaptee) -> None:
        super().__init__()
        self._adaptee = adaptee

    def request(self) -> str:
        print("Adapter: request")
        return self._adaptee.specific_request()
```

### 桥接模式

```py
from __future__ import annotations
from abc import ABC, abstractmethod


class Abstraction:
    def __init__(self, implementation: Implementation) -> None:
        self._implementation = implementation

    def operation(self) -> str:
        print("Abstraction: operation")
        return self._implementation.operation_implementation()


class ExtendedAbstraction(Abstraction):
    def operation(self) -> str:
        super().operation()
        return "ExtendedAbstraction: operation"


class Implementation(ABC):
    @abstractmethod
    def operation_implementation(self) -> str:
        pass


class ConcreteImplementationA(Implementation):
    def operation_implementation(self) -> str:
        return "ConcreteImplementationA: operation_implementation"


class ConcreteImplementationB(Implementation):
    def operation_implementation(self) -> str:
        return "ConcreteImplementationB: operation_implementation"
```

### 组合模式

```py
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

```py
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

```py
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

```py
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

```py
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

```py
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

```py
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

```py
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

```py
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

```py
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

```py
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import Optional


class Context:
    def __init__(self, state: State) -> None:
        self._state = state
        self._state.context = self

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

```py
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Context:
    def __init__(self, strategy: Strategy) -> None:
        self._strategy = strategy

    @property
    def strategy(self) -> Strategy:
        return self._strategy

    @strategy.setter
    def strategy(self, strategy: Strategy) -> None:
        self._strategy = strategy

    def do_some_business_logic(self) -> None:
        result = self._strategy.do_algorithm(["a", "b", "c", "d", "e"])
        print(",".join(result))


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
```

### 模版方法模式

```py
from abc import ABC, abstractmethod


class AbstractClass(ABC):
    def template_method(self) -> None:
        self.base_operation1()
        self.required_operation1()
        self.base_operation2()
        self.hook1()
        self.required_operation2()
        self.base_operation3()
        self.hook2()

    # These operations already have implementations.

    def base_operation1(self) -> None:
        print("AbstractClass: base_operation1")

    def base_operation2(self) -> None:
        print("AbstractClass: base_operation2")

    def base_operation3(self) -> None:
        print("AbstractClass: base_operation3")

    @abstractmethod
    def required_operation1(self) -> None:
        pass

    @abstractmethod
    def required_operation2(self) -> None:
        pass

    def hook1(self) -> None:
        pass

    def hook2(self) -> None:
        pass


class ConcreteClass1(AbstractClass):
    def required_operation1(self) -> None:
        print("ConcreteClass1: required_operation1")

    def required_operation2(self) -> None:
        print("ConcreteClass1: required_operation2")


class ConcreteClass2(AbstractClass):
    def required_operation1(self) -> None:
        print("ConcreteClass2: required_operation1")

    def required_operation2(self) -> None:
        print("ConcreteClass2: required_operation2")

    def hook1(self) -> None:
        print("ConcreteClass2: hook1")
```

### 访问者模式

```py
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Component(ABC):
    @abstractmethod
    def accept(self, visitor: Visitor) -> None:
        pass


class ConcreteComponentA(Component):
    def accept(self, visitor: Visitor) -> None:
        visitor.visit_concrete_component_a(self)


class ConcreteComponentB(Component):
    def accept(self, visitor: Visitor):
        visitor.visit_concrete_component_b(self)


class Visitor(ABC):

    @abstractmethod
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        pass

    @abstractmethod
    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        pass


class ConcreteVisitor1(Visitor):
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        print("ConcreteVisitor1: visit_concrete_component_a")

    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        print("ConcreteVisitor1: visit_concrete_component_b")


class ConcreteVisitor2(Visitor):
    def visit_concrete_component_a(self, element: ConcreteComponentA) -> None:
        print("ConcreteVisitor2: visit_concrete_component_a")

    def visit_concrete_component_b(self, element: ConcreteComponentB) -> None:
        print("ConcreteVisitor2: visit_concrete_component_b")
```
