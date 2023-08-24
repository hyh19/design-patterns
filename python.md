# 设计模式：Python 实现

## 抽象工厂模式

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
        return "ConcreteProductA1"


class ConcreteProductA2(AbstractProductA):
    def useful_function_a(self) -> str:
        return "ConcreteProductA2"


class AbstractProductB(ABC):
    @abstractmethod
    def useful_function_b(self) -> str:
        pass

    @abstractmethod
    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        pass


class ConcreteProductB1(AbstractProductB):
    def useful_function_b(self) -> str:
        return "ConcreteProductB1"

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        return "ConcreteProductB1" + collaborator.useful_function_a()


class ConcreteProductB2(AbstractProductB):
    def useful_function_b(self) -> str:
        return "ConcreteProductB2"

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        return "ConcreteProductB2" + collaborator.useful_function_a()
```

## 生成器模式

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

## 工厂方法模式

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
        return "ConcreteProduct1"


class ConcreteProduct2(Product):
    def operation(self) -> str:
        return "ConcreteProduct2"
```

## 适配器模式

```py
class Target:
    def request(self) -> str:
        return "Target"


class Adaptee:
    def specific_request(self) -> str:
        return "Adaptee"


class Adapter(Target):
    def __init__(self, adaptee: Adaptee) -> None:
        super().__init__()
        self._adaptee = adaptee

    def request(self) -> str:
        return "Adapter" + self._adaptee.specific_request()
```

## 桥接模式

```py
from __future__ import annotations
from abc import ABC, abstractmethod


class Abstraction:
    def __init__(self, implementation: Implementation) -> None:
        self._implementation = implementation

    def operation(self) -> str:
        return self._implementation.operation_implementation()


class ExtendedAbstraction(Abstraction):
    def operation(self) -> str:
        return "ExtendedAbstraction" + super().operation()


class Implementation(ABC):
    @abstractmethod
    def operation_implementation(self) -> str:
        pass


class ConcreteImplementationA(Implementation):
    def operation_implementation(self) -> str:
        return "ConcreteImplementationA"


class ConcreteImplementationB(Implementation):
    def operation_implementation(self) -> str:
        return "ConcreteImplementationB"
```

## 中介者模式

```py
from __future__ import annotations

from abc import ABC, abstractmethod
from typing import Optional


class Mediator(ABC):
    @abstractmethod
    def notify(self, sender: BaseComponent, event: str) -> None:
        pass


class ConcreteMediator(Mediator):
    def __init__(self, component1: Component1, component2: Component2) -> None:
        self._component1 = component1
        self._component2 = component2
        self._component1.mediator = self
        self._component2.mediator = self

    def notify(self, sender: BaseComponent, event: str) -> None:
        if event == "A":
            self._component2.do_c()
        elif event == "D":
            self._component1.do_b()
            self._component2.do_c()


class BaseComponent(ABC):
    def __init__(self, mediator: Optional[Mediator] = None) -> None:
        self._mediator = mediator

    @property
    def mediator(self) -> Optional[Mediator]:
        return self._mediator

    @mediator.setter
    def mediator(self, mediator: Mediator) -> None:
        self._mediator = mediator


class Component1(BaseComponent):
    def do_a(self) -> None:
        assert self.mediator is not None
        self.mediator.notify(self, "A")

    def do_b(self) -> None:
        assert self.mediator is not None
        self.mediator.notify(self, "B")


class Component2(BaseComponent):
    def do_c(self) -> None:
        assert self.mediator is not None
        self.mediator.notify(self, "C")

    def do_d(self) -> None:
        assert self.mediator is not None
        self.mediator.notify(self, "D")
```

## 备忘录模式

```py
from __future__ import annotations

from abc import ABC, abstractmethod
from datetime import datetime
from random import sample
from string import ascii_letters
from typing import List


class Originator:

    def __init__(self, state: str) -> None:
        self._state = state

    def do_something(self) -> None:
        self._state = self._generate_random_string(30)

    def _generate_random_string(self, length: int = 10) -> str:
        return "".join(sample(ascii_letters, length))

    def save(self) -> Memento:
        return ConcreteMemento(self._state)

    def restore(self, memento: Memento) -> None:
        self._state = memento.get_state()


class Memento(ABC):
    @abstractmethod
    def get_name(self) -> str:
        pass

    @abstractmethod
    def get_date(self) -> str:
        pass

    @abstractmethod
    def get_state(self) -> str:
        pass


class ConcreteMemento(Memento):
    def __init__(self, state: str) -> None:
        self._state = state
        self._date = str(datetime.now())[:19]

    def get_state(self) -> str:
        return self._state

    def get_name(self) -> str:
        return f"{self._date} / ({self._state[0:9]}...)"

    def get_date(self) -> str:
        return self._date


class Caretaker:
    def __init__(self, originator: Originator) -> None:
        self._mementos: List[Memento] = []
        self._originator = originator

    def backup(self) -> None:
        self._mementos.append(self._originator.save())

    def undo(self) -> None:
        if not len(self._mementos):
            return

        memento = self._mementos.pop()
        try:
            self._originator.restore(memento)
        except Exception:
            self.undo()

    def show_history(self) -> None:
        for memento in self._mementos:
            print(memento.get_name())
```

## 观察者模式

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
        print("ConcreteObserverA")


class ConcreteObserverB(Observer):
    def update(self, state: int) -> None:
        print("ConcreteObserverB")
```

## 状态模式

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

## 策略模式

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

## 模版方法模式

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

## 访问者模式

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
