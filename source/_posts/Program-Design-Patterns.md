---
title: 程序设计模式 Program Design Patterns
date: 2024-02-02 17:17:92
updated: 2024-02-02 17:17:92
categories:
  - Python
tags:
  - Program
  - 高级编程
  - Python
  - 设计模式
  - DesignPattern
description: 
thumbnail: 
banner_img:
---

# 程序设计模式 Design Patterns

## Start

在写一些复杂度、集成度高的程序时，常常会写很多 class 很多 methods，如何将他们很好的组合起来，后期方便扩展和维护，避免写出 **屎山代码**，这时候就需要一个约定俗称的 **程序设计模式** （Program Design Patterns）。

本文以 Python 和 Typescript 为例子，介绍 22 种设计模式的实现和在实际开发中如何去高效使用。

Reference：

1. Refactoring guru 图解设计模式/重构 教程网站：[常用设计模式有哪些？](https://refactoringguru.cn/design-patterns/)

## 工厂方法模式 factory method

Python：

```python
from __future__ import annotations
from abc import ABC, abstractmethod

class Creator(ABC):
    """
    The Creator class declares the factory method that is supposed(被假设) to return an
    object of a Product class. The Creator's subclasses usually provide the
    implementation of this method.

	Creator 类声明了一个工厂方法，该方法用于返回 Product 类的对象。
	工厂方法的子类通常会提供该方法的实现，
    """

    @abstractmethod
    def factory_method(self):
        """
        Note that the Creator may also provide some default implementation of
        the factory method.

		注意：Creator 还可能提供工厂方法的一些默认实现
        """
        pass

    def some_operation(self) -> str:
        """
        Also note that, despite(尽管) its name, the Creator's primary responsibility
        is not creating products. Usually, it contains(包含) some core business logic(核心业务)
        that relies on(依赖于) Product objects, returned by the factory method.
        Subclasses(子类) can indirectly(间接) change that business logic by / overriding(重写) the
        factory method and returning a different type of product from it.
        
        注意：Creator 工厂方法的主要职责不是创建产品。
        通常，它包含一些依赖于工厂方法返回的 Product 对象的核心业务逻辑。 
        子类可以通过重写工厂方法并从中返回不同类型的产品来间接更改该业务逻辑。
        """

        # Call the factory method to create a Product object.
        # 调用工厂方法生产产品
        product = self.factory_method()

        # Now, use the product.
        # 使用工厂方法
        result = f"Creator: The same creator's code has just worked with {product.operation()}"

        return result

"""
Concrete(具体的) Creators override the factory method in order to change the resulting
product's type.

具体的创建者重写工厂方法以更改生成的产品的类型
"""

class ConcreteCreator1(Creator):
    """
    Note that the signature of the method still uses the abstract product type,
    even though the concrete product is actually returned from the method. This
    way the Creator can stay independent of concrete product classes.

	
    """

    def factory_method(self) -> Product:
        return ConcreteProduct1()

class ConcreteCreator2(Creator):
    def factory_method(self) -> Product:
        return ConcreteProduct2()

class Product(ABC):
    """
    The Product interface declares the operations that all concrete products
    must implement.
    """

    @abstractmethod
    def operation(self) -> str:
        pass

"""
Concrete Products provide various implementations of the Product interface.
"""

class ConcreteProduct1(Product):
    def operation(self) -> str:
        return "{Result of the ConcreteProduct1}"

class ConcreteProduct2(Product):
    def operation(self) -> str:
        return "{Result of the ConcreteProduct2}"

def client_code(creator: Creator) -> None:
    """
    The client code works with an instance of a concrete creator, albeit through
    its base interface. As long as the client keeps working with the creator via
    the base interface, you can pass it any creator's subclass.
    """

    print(f"Client: I'm not aware of the creator's class, but it still works.\n"
          f"{creator.some_operation()}", end="")

if __name__ == "__main__":
    print("App: Launched with the ConcreteCreator1.")
    client_code(ConcreteCreator1())
    print("\n")

    print("App: Launched with the ConcreteCreator2.")
    client_code(ConcreteCreator2())
```
