# 设计模式：Python 实现

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


class ConcreteBuilder1(Builder):
    def __init__(self) -> None:
        self._reset()

    @property
    def product(self) -> Product1:
        product = self._product
        self._reset()

        return product

    def produce_part_a(self) -> None:
        self._product.add_part("PartA1")

    def produce_part_b(self) -> None:
        self._product.add_part("PartB1")

    def produce_part_c(self) -> None:
        self._product.add_part("PartC1")

    def _reset(self) -> None:
        self._product: Product1 = Product1()


class Product1:
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
