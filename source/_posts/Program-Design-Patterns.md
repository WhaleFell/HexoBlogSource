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

工厂方法将创建产品的代码与实际使用产品的代码分离， 从而能在不影响其他代码的情况下扩展产品创建部分代码。

例如， 如果需要向应用中添加一种新产品， 你只需要开发新的创建者子类， 然后重写其工厂方法即可。

**开闭原则:** 在扩展新的类时，尽量不要修改原有代码。

伪代码:

```java
// 工厂方法基类,描述产品的大类型
class Dialog is
    // 创建者类声明的工厂方法必须返回一个具体产品类的对象。
    // 创建者的子类通常会提供该方法的实现。
    // 创建者还可提供一些工厂方法的默认实现。
    abstract method createButton():Button

    // 请注意，创建者的主要职责并非是创建产品。其中通常会包含一些核心业务逻辑
    // 这些逻辑依赖于由工厂方法返回的产品对象。
    // 子类可通过重写工厂方法并使其返回不同类型的产品来间接修改业务逻辑。

    method render() is
        // 调用工厂方法创建一个产品对象.
        // 子类可以重写 createButton 方法以更改产品类型
        Button okButton = createButton()
        // 现在使用产品。
        okButton.onClick(closeDialog)
        // 间接调用产品的 render 方法
        okButton.render()


// 具体创建者将重写工厂方法以改变其所返回的产品类型。
class WindowsDialog extends Dialog is
    method createButton():Button is
        return new WindowsButton()

class WebDialog extends Dialog is
    method createButton():Button is
        return new HTMLButton()


// 产品接口中将声明所有具体产品都必须实现的操作。
interface Button is
    method render()
    method onClick(f)

// 具体产品需提供产品接口的各种实现。
class WindowsButton implements Button is
    method render(a, b) is
        // 根据 Windows 样式渲染按钮。
    method onClick(f) is
        // 绑定本地操作系统点击事件。

class HTMLButton implements Button is
    method render(a, b) is
        // 返回一个按钮的 HTML 表述。
    method onClick(f) is
        // 绑定网络浏览器的点击事件。


class Application is
    field dialog: Dialog

    // 程序根据当前配置或环境设定选择创建者的类型。
    method initialize() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            dialog = new WindowsDialog()
        else if (config.OS == "Web") then
            dialog = new WebDialog()
        else
            throw new Exception("错误！未知的操作系统。")

    // 当前客户端代码会与具体创建者的实例进行交互，但是必须通过其基本接口
    // 进行。只要客户端通过基本接口与创建者进行交互，你就可将任何创建者子
    // 类传递给客户端。
    method main() is
        this.initialize()
        dialog.render()
```

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
        Subclasses(子类) can indirectly(间接) change that business logic by / overriding(重写) the factory method and returning a different type of product from it.

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

    请注意，该方法的签名仍然使用抽象产品类型、
    尽管该方法实际返回的是具体产品。这样
    这样，创建者就可以独立于具体的产品类别。
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

    产品接口定义了一个 operation 方法，所有具体产品都必须实现该方法.
    """

    @abstractmethod
    def operation(self) -> str:
        pass

"""
Concrete Products provide various implementations of the Product interface.
具体产品提供了产品接口的各种实现。
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

    客户端代码与具体创建者的实例一起工作，尽管是通过其基本接口。
    只要客户端继续通过基本接口工作，就可以将创建者的任何子类传递给它。
    """

    # 客户端: 我不知道创建者的类，但它仍然有效。
    print(f"Client: I'm not aware of the creator's class, but it still works.\n"
          f"{creator.some_operation()}", end="")

if __name__ == "__main__":
    # APP: 启动 ConcreteCreator1
    print("App: Launched with the ConcreteCreator1.")
    client_code(ConcreteCreator1())
    print("\n")

    # APP: 启动 ConcreteCreator2
    print("App: Launched with the ConcreteCreator2.")
    client_code(ConcreteCreator2())
```
