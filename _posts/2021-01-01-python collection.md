---
layout: post
title: "Python知识整理"
description: Python知识整理
modified: 2021-01-01
category: Resources
tags: [Resources]
---

# Python基础

1.面向过程

(1)数据类型，Python默认拥有以下内置数据类型，可以使用type()函数获取任何对象的数据类型

文本类型：str
数值类型：int, float, complex
序列类型：list, tuple, range
映射类型：dict
集合类型：set, frozenset
布尔类型：bool
二进制类型：bytes, bytearray, memoryview

    x = "Hello World"
    x = 29
    x = 29.5 #浮点数也可以是带有“e”的科学数字，例如x = 15e2
    x = 1j
    x = ["apple", "banana", "cherry"]
    x = ("apple", "banana", "cherry")
    x = range(6)
    x = {"name" : "Bill", "age" : 63}
    x = {"apple", "banana", "cherry"}
    x = frozenset({"apple", "banana", "cherry"})
    x = True
    x = b"Hello"
    x = bytearray(5)
    x = memoryview(bytes(5))
    
    # 指定具体的数据类型
    x = str("Hello World")
    x = int(29)
    x = float(29.5)
    x = complex(1j)
    x = list(("apple", "banana", "cherry"))
    x = tuple(("apple", "banana", "cherry"))
    x = range(6)
    x = dict(name="Bill", age=36)
    x = set(("apple", "banana", "cherry"))
    x = frozenset(("apple", "banana", "cherry"))
    x = bool(5)
    x = bytes(5)
    x = bytearray(5)
    x = memoryview(bytes(5))

(2)判断

    # 基本
    if condition1
        # 不能为空，使用pass语句来避免错误:
        pass
    elif condition2:
        do something2
    else:
        do something3
        
    # 评估任何值返回True或者False，除空值（例如 ()、[]、{}、""、数字 0 和值 None），其他返True
    bool(var)

(3)循环

    # while
    while condition :
        do something
    else:
      print("run once")
        
    // 正序，range()函数返回的不是数组，而是一个整数序列的对象，len()返回对象（字符、列表、元组等）长度或项目个数
    for i in range(0,len(nums)):
        # 不能为空，使用pass语句来避免错误
        do_something(nums[i])
    else:
        print("run once")
    
    // 倒序
    for i in range(len(arr) - 1,-1,-1)
        do_something(nums[i])

(4)运算符

    # 取整
    a // b
    
    # 取余
    a % b
    
    # 幂
    2**31

    # 与或非
    Ture and False
    True or False
    not True
    
    # 身份运算符，比较两个变量是否同一个对象
    a is None
    a is not None
    x is y
    x is not y
    
    # 成员运算符
    "abc" in ["abc","def"]
    "abc" not in ["abc","def"]
    
    # 位运算，与、或、非、异或、左移、右移
    result = a & b
    result = a | b
    result = ~ b
    result = a ^ b
    result = a << 2 
    result = a >> 2 
    
(5)函数

    def my_function(param1,param2):
        do something
        return something
    
    # 默认参数值
    def my_function(country = "China"):
        print("I am from " + country)
    
    # 任意参数个数，传递的是一个元组
    def my_function(*kids):
      print("The youngest child is " + kids[2])
    
(6)lambda函数是一种小的匿名函数，可接受任意数量的参数，但只能有一个表达式。

    x = lambda a, b, c : a + b + c
    print(x(5, 6, 2))
    
(7)迭代器

列表、元组、字典和集合都是可迭代的对象。它们是可迭代的容器，您可以从中获取迭代器（Iterator）。

    class MyNumbers:
      # 返回可迭代对象本身
      def __iter__(self):
        self.a = 1
        return self
    
      def __next__(self):
        if self.a <= 20:
          x = self.a
          self.a += 1
          return x
        else:
          # 为了防止迭代永远进行，可以使用StopIteration语句。
          raise StopIteration
    
    myclass = MyNumbers()
    myiter = iter(myclass)
    
    for x in myiter:
      print(x)
    
    # 或者
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    print(next(myiter))
    
(8)对于字典和列表是传递的对象引用，即可以修改原对象，对于数字、字符串、元组是传递的值。

(9)global变量

通常在函数内部创建变量时，该变量是局部变量，只能在该函数内部使用。要在函数内部创建全局变量，可以使用global关键字。

    def myfunc():
      global x
      x = "fantastic"
    
    myfunc()
    # 输出结果为Python is fantastic
    print("Python is " + x)

2.面向对象
    
(1)类的示例

    class Person:
      # 每次使用类创建新对象时，都会自动调用 __init__() 函数
      def __init__(self, name, age):
        self.name = name
        self.age = age
    
      # self参数是对类的当前实例的引用，它必须是类中任意函数的首个参数
      def myfunc(self):
        print("Hello my name is " + self.name)
        
      # 如果一个类表现得像一个list，要获取有多少个元素，就得用len()函数。要让len()函数工作正常，类必须提供一个特殊方法__len__()，它返回元素的个数。 
      def __len__(self):
      
    p1 = Person("Bill", 63)
    p1.myfunc()
    # 删除对象属性
    del p1.age
    # 删除对象
    del p1
    
(2)继承

    class Student(Person):
      def __init__(self, fname, lname, year):
        super().__init__(fname, lname)
        self.graduationyear = year
    
    x = Student("Elon", "Musk", 2019)
    

(2)类方法：@staticmethod，方法参数中没有self
(3)引入：from src.session1.common.PrintUtil import PrintUtil
(4)调用：类中的某个方法调用其他方法self.another_method()
(5)私有属性：self.__attr_name

3.IO
(1)print(list)
(2)输出不换行print("something",end=' '),自定义输出末尾是什么

4.字符串

    # 字符串长度
    len(str)
    
    # 比较
    s1 == s2
    s1 != s2

    # 类型转换
    newstr = str(123)
    num = int("9")

    # 多行字符串，使用三个单引号、双引号将多行字符包围。
    """
    aaa
    bbbb
    ccccc
    """
    
    '''
    aaa
    bbbb
    ccccc
    ''' 
    
    # 分割，其中max为-1表示分割所有，表示最大分割次数，返回列表。
    str.split("/",max)

    # 拼接
    "".join(list)
    str1 + str2

    # 返回大小写
    str.lower()
    str.uppper()
    
    # 替换
    str.replace("world", "kitty")
    
    # 检查字符串
    result = "abc" in "abc is string"
    result = "abc" not in "abc is string"
    
    # 字符串格式化
    quantity = 5
    itemno = 789
    price = 24.36
    myorder = "I want to pay {2} dollars for {0} pieces of item {1}."
    print(myorder.format(quantity, itemno, price))
    
    # 是否为空字符串
    str.isspace()
    
    # 是否为字母
    str.isalpha()
    
    # 是否为数字
    str.isdigit()

    # 去除两侧、左侧、右侧空格
    str.strip()
    str.lstrip()
    str.rstrip()

    # 截取：字符串是表示unicode字符的字节数组，某一位
    a = str[i]
    b = str[i:j]

5.列表

    # 访问项目
    item = thislist[3]
    listb = thislist[2:5]
    
    # 遍历
    for i in thislist
        print(i)
    
    # 添加
    thislist.append(i)
    thislist.insert(1, "orange")
    
    # 删除
    thislist.remove("banana")
    # 删除指定的索引（如果未指定索引，则删除最后一项）
    thislist.pop() 
    del thislist[0]
    del thislist
    # 清空
    thislist.clear()
    
    # 浅拷贝传递对象的引用
    listb = lista
    
    # 浅拷贝
    mylist = thislist.copy()
    
    listb=list(lista)
    
    listb=[i for i in lista]
    
    listb = lista[:]
    
    import copy
    listb=copy.copy(lista)
    
    # 深拷贝，包含列表里面的子对象的拷贝，所以原始对象的改变不会造成深拷贝里任何子元素的改变
    import copy
    listb=copy.deepcopy(lista)

    # 合并
    list3 = list1 + list2
    list1.extend(list2)
    
    # 判断是否存在
    if ele in arr
    lista.count(obj) == 0
    
    # 反转，该方法没有返回值，需要注意
    lista.reverse()
    
    # 排序
    lista.sort()
    # 倒序
    lista.sort(reverse = True)
    # 对象列表排序，其中sortMethod为排序方法，return的是排序依据的字段。
    list.sort(key = sortMethod)
    
    # 二维列表初始化
    initMatrix = [[0 for i in range(m)] for j in range(n)]

6.集合
    
    # 添加
    thisset.add("orange")
    # 添加多个
    thisset.update(["orange", "mango", "grapes"])
    
    # 删除
    thisset.clear()
    del thisset
    # 两个方法如果banana不存在，都将引发错误
    thisset.remove("banana")
    thisset.discard("banana")
    
    # 合并
    set3 = set1.union(set2)
    set1.update(set2)
    
7.字典

    # 访问
    x = thisdict["model"]
    x = thisdict.get("model")
    
    # 遍历
    for key in thisdict:
      print(x)
      print(thisdict[x])
      
    for value in thisdict.values():
      print(value)
      
    for key, value in thisdict.items():
      print(key, value)
    
    # 添加
    dict[key] = value
    
    # 删除
    thisdict.pop("model")
    del thisdict["model"]
    del thisdict
    thisdict.clear()
    
    # 浅拷贝
    mydict = thisdict.copy()
    mydict = dict(thisdict)
    
    # 创建
    thisdict = dict(brand="Porsche", model="911", year=1963)
    thisdict =	{
      "brand": "Porsche",
      "model": "911",
      "year": 1963
    }
    
    # 判断是否存在key
    if key in dict

8.队列：from collections import deque，有append(),popleft()方法

