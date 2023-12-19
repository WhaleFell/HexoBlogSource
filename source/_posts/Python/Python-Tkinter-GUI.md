---

title: Python Tkinter 框架学习
date: 2021-08-18 19:37:48
updated: 2021-08-18 19:37:48
categories:
- Python
tags: [Python, Coding, GUI]
description: Python Tkinter 是一个 Python 自带GUI包，十分轻量。
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210630201524.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210630201524.png

---

# Tkinter 框架学习

## GUI Tkinter 框架结构

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704162742.png&webp=true)

## 常用组件汇总列表

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704162352.png&webp=true)

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704162631.png&webp=true)

## 面向对象的方式写 GUI 框架的经典实例

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704170311.png&webp=true)

### 具体实现代码

```python
#!/usr/bin/python python3
# coding=utf-8
'''
Author: whalefall
Date: 2021-07-04 16:50:10
LastEditTime: 2021-07-04 17:25:55
Description: 利用面向对象的方式写一个经典的 GUI 程序
'''
from tkinter import *
# Frame: 一个布局容器,里面可以放置组件.
from tkinter import Frame
# messagebox: 一个消息框组件
from tkinter import messagebox


class Application(Frame):
    '''
    通过 Application 类组织整个 GUI 程序,继承自 Frame.
    '''

    def __init__(self, master=None):
        '''
        初始化类,要传入根窗口对象.
        '''
        # 调用父类 Frame 的初始化方法
        super().__init__(master)
        # 将 master(传入的根窗口对象) 添加到 APP 类的属性
        # 使 self 拥有 root 根窗口对象的所有方法.
        self.master = master

        self.pack()  # 布局管理器,用于添加组件到布局
        # 创建各组件
        self.createWidget()

    def createWidget(self):
        '''
        创建一个组件,在初始化类中调用
        '''
        # 用父类的 Master 即 self 对象创建一个按钮组件
        self.btn1 = Button(self)
        self.btn1["text"] = "点击提交"  # 设置按钮文字属性
        self.btn1["command"] = self.lover  # 点击按钮所执行的函数
        self.btn1.pack()  # 添加到布局管理器

        # 创建一个退出按钮
        # command: 命令 (点击按钮要执行的函数)
        # root: 全局变量(根窗口)
        # destroy 方法: 消灭所有窗体(退出程序)
        self.btnQuit = Button(self, text="退出", command=root.destroy)
        self.btnQuit.pack()  # 添加到布局管理器

    def lover(self):
        '''
        点击 btn1 所执行的函数
        '''
        messagebox.showinfo("表白信息", "颖怡我喜欢你.")


if __name__ == "__main__":
    root = Tk()  # 创建主窗体
    root.geometry("400x100+200+300")  # 设置程序长宽位置
    root.title("面向对象经典 GUI 程序")

    app = Application(master=root)

    root.mainloop()  # 调用组件的方法,进入事件循环.
```

## Label ( 标签 ) 用法

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704193103.png&webp=true)

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704193502.png&webp=true)

## 组件 Option 选项设置

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210704203439.png&webp=true)

### 选项大全

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210705220646.png&webp=true)

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210705220636.png&webp=true)

## Button Anchor 位置控制

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210705221052.png&webp=true)

## Entry StrinngVar 输入框

> 输入框接收变量的类型

[image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210705223051.png&webp=true)

属性的双向关联

![image-20210705223506326](C:/Users/WhaleFall/AppData/Roaming/Typora/typora-user-images/image-20210705223506326.png)
