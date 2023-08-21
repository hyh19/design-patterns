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
