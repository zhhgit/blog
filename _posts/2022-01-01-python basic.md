---
layout: post
title: "Python系列 -- Python基础"
description: Python系列 -- Python基础
modified: 2022-01-01
category: Python
tags: [Python]
---

# 一、面向过程

1.数据类型，Python默认拥有以下内置数据类型，可以使用type()函数获取任何对象的数据类型

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

    # 类型最大值
    # int最大值
    import sys
    i = sys.maxsize

2.判断

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

3.循环

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

4.运算符

    # 取整除
    a // b

    # 除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数：
    a / b
    
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
    
5.函数

    def my_function(param1,param2):
        do something
        return something
    
    # 默认参数值
    def my_function(country = "China"):
        print("I am from " + country)
    
    # 可变参数
    # 传递的是一个元组，可变参数既可以直接传入：func(1, 2, 3)，又可以先组装list或tuple，再通过*args传入：func(*(1, 2, 3))；
    def my_function(*kids):
        print("The youngest child is " + kids[2])

    # 关键字参数
    # 关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过**kw传入：func(**{'a': 1, 'b': 2})。
    def person(name, age, **kw):
        print('name:', name, 'age:', age, 'other:', kw)

    # 命名关键字参数
    # 如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。和关键字参数**kw不同，命名关键字参数需要一个特殊分隔符*，*后面的参数被视为命名关键字参数。
    def person(name, age, *, city, job):
        print(name, age, city, job)
    # 调用方式如下：
    person('Jack', 24, city='Beijing', job='Engineer')
    # 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了
    def person(name, age, *args, city, job):
        print(name, age, args, city, job)
    # 命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错。
    # 使用命名关键字参数时，要特别注意，如果没有可变参数，就必须加一个*作为特殊分隔符。如果缺少*，Python解释器将无法识别位置参数和命名关键字参数。
    
    # 重要！！在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
    
6.迭代器

字符串、列表、元组、字典和集合都是可迭代的对象。它们是可迭代的容器，可以从中获取迭代器（Iterator）。

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

    # 如何判断一个对象是可迭代对象呢？方法是通过collections.abc模块的Iterable类型判断：
    from collections.abc import Iterable
    isinstance('abc', Iterable) # str是否可迭代

    # 凡是可作用于for循环的对象都是Iterable类型；
    # 凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；
    # 集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。
    # Python的for循环本质上就是通过不断调用next()函数实现的，例如：
    for x in [1, 2, 3, 4, 5]:
        pass

    # 实际上完全等价于：
    # 首先获得Iterator对象:
    it = iter([1, 2, 3, 4, 5])
    # 循环:
    while True:
        try:
            # 获得下一个值:
            x = next(it)
        except StopIteration:
            # 遇到StopIteration就退出循环
            break

7.生成器

如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？
这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。
我们创建了一个generator后，基本上永远不会调用next()，而是通过for循环来迭代它，并且不需要关心StopIteration的错误。

    # 注意最外面是小括号
    g = (x * x for x in range(10))
    for n in g:
        print(n)

定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。
这里最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。
而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

    def fib(max):
        n, a, b = 0, 0, 1
        while n < max:
            yield b
            a, b = b, a + b
            n = n + 1
        return 'done'

8.字符串

    # 字符串长度。len()函数计算的是str的字符数。但是如果换成bytes，len()函数就计算字节数。
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

    # 由于Python的字符串类型是str，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把str变为以字节为单位的bytes。
    # Python对bytes类型的数据用带b前缀的单引号或双引号表示。要注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节。
    x = b'ABC'
    # 以Unicode表示的str通过encode()方法可以编码为指定的bytes。（也就是说"中文"正常在python中是Unicode编码的，但是可以使用其他编码格式，编码后用一个个字节bytes表示）
    '中文'.encode('utf-8')

    # 如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法。如果bytes中包含无法解码的字节，decode()方法会报错。
    b'ABC'.decode('ascii')
    b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')

9.列表

    # 访问项目
    item = thislist[3]
    # 切片
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
    thislist.pop(0) 
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
    
    # 列表生成式
    # 二维列表初始化
    initMatrix = [[0 for i in range(m)] for j in range(n)]

10.集合

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

11.字典

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

12.元组

tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向'a'，就不能改成指向'b'，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的。
    
    classmates = ('Michael', 'Bob', 'Tracy')

13.对于字典和列表是传递的对象引用，即可以修改原对象，对于数字、字符串、元组是传递的值。

14.global变量

通常在函数内部创建变量时，该变量是局部变量，只能在该函数内部使用。要在函数内部创建全局变量，可以使用global关键字。

    def myfunc():
      global x
      x = "fantastic"
    
    myfunc()
    # 输出结果为Python is fantastic
    print("Python is " + x)

# 二、函数式编程

1.高阶函数：变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

    def add(x, y, f):
        return f(x) + f(y)

2.map函数

    def f(x):
        return x * x
    
    newList = list(map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9]))

2.reduce函数，作用为reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

    from functools import reduce
    
    def f(x, y):
        return x + y
    
    ret = reduce(f, [1, 2, 3])
    print(ret)

3.filter函数

filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

    def is_odd(n):
        return n % 2 == 1

    list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))

4.sorted函数

sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序，例如按绝对值大小排序。key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。

    a = [36, 5, -12, 9, -21]
    print(sorted(a, key=abs))

5.lambda函数是一种小的匿名函数，可接受任意数量的参数，但只能有一个表达式。

    x = lambda a, b, c : a + b + c
    print(x(5, 6, 2))

6.装饰器

在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。本质上，decorator就是一个返回函数的高阶函数。把@log放到now()函数的定义处，相当于执行了语句：now = log(now)

    import functools

    def log(func):
        # 因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。
        # 不需要编写wrapper.__name__ = func.__name__这样的代码，Python内置的functools.wraps就是干这个事的
        @functools.wraps(func) 
        def wrapper(*args, **kw):
            print('call %s():' % func.__name__)
            return func(*args, **kw)
        return wrapper
    @log
    def now():
        print('2015-3-25')

    now()

针对带参数的decorator：

    import functools
    
    def log(text):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kw):
                print('%s %s():' % (text, func.__name__))
                return func(*args, **kw)
            return wrapper
        return decorator

7.偏函数：简单总结functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

    import functools
    int2 = functools.partial(int, base=2)

相当于：

    def int2(x, base=2):
        return int(x, base)

# 三、面向对象
    
1.类的示例

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
    
2.继承

    class Student(Person):
      def __init__(self, fname, lname, year):
        super().__init__(fname, lname)
        self.graduationyear = year
    
    x = Student("Elon", "Musk", 2019)

3.调用：类中的某个方法调用其他方法self.another_method()

4.私有属性

self.__attr_name，实例的变量名如果以__开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。确实不能访问，访问会报错！！
变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。

5.优先使用isinstance()判断类型，可以将指定类型及其子类“一网打尽”。

    isinstance(123, int)

6.dir()

如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list。

7.类方法：@staticmethod，方法参数中没有self

8.类属性，归Student类所有。

    class Student(object):
        name = 'Student'

9.动态绑定属性和方法

    class Student(object):
        pass

给实例绑定：

    from types import MethodType

    s = Student()
    # 绑定属性
    s.name = 'Michael'
    
    # 绑定方法
    def set_age(self, age): # 定义一个函数作为实例方法
        self.age = age

    s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
    s.set_age(25) # 调用实例方法

给类绑定：

    # 绑定方法
    def set_score(self, score):
        self.score = score

    Student.set_score = set_score

10.getattr()、setattr()以及hasattr()

    hasattr(obj, 'y') # 有属性'y'吗？
    setattr(obj, 'y', 19) # 设置一个属性'y'
    getattr(obj, 'y') # 获取属性'y'

    hasattr(obj, 'power') # 有属性'power'吗？
    fn = getattr(obj, 'power') # 获取属性'power'并赋值到变量fn
    fn() # 调用fn()与调用obj.power()是一样的

11.使用__slots__

如果我们想要限制实例的属性怎么办？比如，只允许对Student实例添加name和age属性。 为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性，试图绑定score将得到AttributeError的错误。
__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的。

    class Student(object):
        __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称

12.装饰器@property

有没有既能检查参数，又可以用类似属性这样简单的方式来访问类的变量呢？Python内置的@property装饰器就是负责把一个方法变成属性调用的。而不是通过getter，setter！！
@property的实现比较复杂，我们先考察如何使用。把一个getter方法变成属性，只需要加上@property就可以了。
此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作。

    class Student(object):
    
        @property
        def score(self):
            return self._score
    
        @score.setter
        def score(self, value):
            if not isinstance(value, int):
                raise ValueError('score must be an integer!')
            if value < 0 or value > 100:
                raise ValueError('score must between 0 ~ 100!')
            self._score = value
    
    s = Student()
    s.score = 60 # OK，实际转化为s.set_score(60)
    s.score # OK，实际转化为s.get_score()

13.多重继承

一个子类就可以同时获得多个父类的所有功能。
MixIn，在设计类的继承关系时，通常，主线都是单一继承下来的，例如，Ostrich继承自Bird。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让Ostrich除了继承自Bird外，再同时继承Runnable。这种设计通常称之为MixIn。
由于Python允许使用多重继承，因此，MixIn就是一种常见的设计。只允许单一继承的语言（如Java）不能使用MixIn的设计。

    class Dog(Mammal, Runnable):
        pass
    
    class Bat(Mammal, Flyable):
        pass

14.友好输出，__str__()返回用户看到的字符串(作用于print)，而__repr__()返回程序开发者看到的字符串，也就是说，__repr__()是为调试服务的(作用于交互式)。

    class Student(object):
        def __init__(self, name):
            self.name = name
        def __str__(self):
            return 'Student object (name=%s)' % self.name
        __repr__ = __str__

15.方法__getitem__

迭代器Fib实例虽然能作用于for循环，看起来和list有点像，但是，把它当成list来使用还是不行，比如，取第5个元素Fib()[5]将报错。

    class Fib(object):
        def __getitem__(self, n):
            if isinstance(n, int): # n是索引
                a, b = 1, 1
                for x in range(n):
                    a, b = b, a + b
                return a
            if isinstance(n, slice): # n是切片
                start = n.start
                stop = n.stop
                if start is None:
                    start = 0
                a, b = 1, 1
                L = []
                for x in range(stop):
                    if x >= start:
                        L.append(a)
                    a, b = b, a + b
                return L
    
    f = Fib()
    f[10]
    f[0:5]

16.方法__getattr__

注意与getattr()区分！！
当调用不存在的属性时，比如score，Python解释器会试图调用__getattr__(self, 'score')来尝试获得属性，这样，我们就有机会返回score的值：
注意，只有在没有找到属性的情况下，才调用__getattr__，已有的属性，比如name，不会在__getattr__中查找。

class Student(object):

    def __init__(self):
        self.name = 'Michael'

    def __getattr__(self, attr):
        if attr=='score':
            return 99

要让class只响应特定的几个属性，我们就要按照约定，抛出AttributeError的错误：

    class Student(object):
    
        def __getattr__(self, attr):
            if attr=='age':
                return lambda: 25
            raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)

17.方法__call__

一个对象实例可以有自己的属性和方法，当我们调用实例方法时，我们用instance.method()来调用。能不能直接在实例本身上调用呢？在Python中，答案是肯定的。任何类，只需要定义一个__call__()方法，就可以直接对实例进行调用。
也就是说，正常是调用实例的某个方法，但是函数即函数，函数即对象。可以直接执行对象本身。

    class Student(object):
        def __init__(self, name):
            self.name = name

        def __call__(self):
            print('My name is %s.' % self.name)

调用方式如下：

    s = Student('Michael')
    s() # self参数不要传入

怎么判断一个变量是对象还是函数呢？其实，更多的时候，我们需要判断一个对象是否能被调用，能被调用的对象就是一个Callable对象，比如函数和我们上面定义的带有__call__()的类实例。通过callable()函数，我们就可以判断一个对象是否是“可调用”对象。

    callable(Student()) # True
    callable(max) # True
    callable([1, 2, 3]) # False
    callable(None) # False

18.枚举类

    from enum import Enum, unique

    @unique # 装饰器可以帮助我们检查保证没有重复值。
    class Gender(Enum):
        Male = 0
        Female = 1
    
    class Student(object):
        def __init__(self, name, gender):
            self.name = name
            self.gender = gender
    
    # 测试:
    bart = Student('Bart', Gender.Male)
    if bart.gender == Gender.Male:
        print('测试通过!')
    else:
        print('测试失败!')

19.type()

动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。
当Python解释器载入hello模块时，就会依次执行该模块的所有语句，执行结果就是动态创建出一个Hello的class对象。
type()函数可以查看一个类型或变量的类型，Hello是一个class，它的类型就是type，而h是一个实例，它的类型就是class Hello。

    from hello import Hello
    h = Hello()
    h.hello()
    print(type(Hello))
    # <class 'type'>
    print(type(h))
    # <class 'hello.Hello'>

type()函数既可以返回一个对象的类型，又可以创建出新的类型，比如，我们可以通过type()函数创建出Hello类，而无需通过class Hello(object)...的定义。
要创建一个class对象，type()函数依次传入3个参数：class的名称；继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；class的方法名称与函数绑定，这里我们把函数fn绑定到方法名hello上。
通过type()函数创建的类和直接写class是完全一样的，因为Python解释器遇到class定义时，仅仅是扫描一下class定义的语法，然后调用type()函数创建出class。

    def fn(self, name='world'): # 先定义函数
        print('Hello, %s.' % name)

    Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
    h = Hello()
    h.hello()

# 四、多进程与多线程

1.Process

由于Python是跨平台的，自然也应该提供一个跨平台的多进程支持。multiprocessing模块就是跨平台版本的多进程模块。
multiprocessing模块提供了一个Process类来代表一个进程对象，下面的例子演示了启动一个子进程并等待其结束。

    from multiprocessing import Process
    import os
    
    # 子进程要执行的代码
    def run_proc(name):
        print('Run child process %s (%s)...' % (name, os.getpid()))
    
    if __name__=='__main__':
        print('Parent process %s.' % os.getpid())
        p = Process(target=run_proc, args=('test',))
        print('Child process will start.')
        p.start()
        # join()方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。
        p.join()
        print('Child process end.')

2.进程池Pool

如果要启动大量的子进程，可以用进程池的方式批量创建子进程。

    from multiprocessing import Pool
    import os, time, random
    
    def long_time_task(name):
        print('Run task %s (%s)...' % (name, os.getpid()))
        start = time.time()
        time.sleep(random.random() * 3)
        end = time.time()
        print('Task %s runs %0.2f seconds.' % (name, (end - start)))
    
    if __name__=='__main__':
        print('Parent process %s.' % os.getpid())
        p = Pool(4)
        for i in range(5):
            p.apply_async(long_time_task, args=(i,))
            print('Waiting for all subprocesses done...')
        # Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前必须先调用close()，调用close()之后就不能继续添加新的Process了。
        p.close()
        p.join()
        print('All subprocesses done.')

3.外部子进程

很多时候子进程并不是自身，而是一个外部进程。我们创建了子进程后，还需要控制子进程的输入和输出。
subprocess模块可以让我们非常方便地启动一个子进程，然后控制其输入和输出。
下面的例子演示了如何在Python代码中运行命令nslookup www.python.org，这和命令行直接运行的效果是一样的。

    import subprocess
    
    print('$ nslookup www.python.org')
    r = subprocess.call(['nslookup', 'www.python.org'])
    print('Exit code:', r)

4.Process通信

操作系统提供了很多机制来实现进程间的通信。Python的multiprocessing模块包装了底层的机制，提供了Queue、Pipes等多种方式来交换数据。
我们以Queue为例，在父进程中创建两个子进程，一个往Queue里写数据，一个从Queue里读数据。

    from multiprocessing import Process, Queue
    import os, time, random
    
    # 写数据进程执行的代码:
    def write(q):
        print('Process to write: %s' % os.getpid())
        for value in ['A', 'B', 'C']:
            print('Put %s to queue...' % value)
            q.put(value)
            time.sleep(random.random())
    
    # 读数据进程执行的代码:
    def read(q):
        print('Process to read: %s' % os.getpid())
        while True:
            value = q.get(True)
            print('Get %s from queue.' % value)
    
    if __name__=='__main__':
        # 父进程创建Queue，并传给各个子进程：
        q = Queue()
        pw = Process(target=write, args=(q,))
        pr = Process(target=read, args=(q,))
        # 启动子进程pw，写入:
        pw.start()
        # 启动子进程pr，读取:
        pr.start()
        # 等待pw结束:
        pw.join()
        # pr进程里是死循环，无法等待其结束，只能强行终止:
        pr.terminate()

5.threading

Python的标准库提供了两个模块：_thread和threading，_thread是低级模块，threading是高级模块，对_thread进行了封装。
绝大多数情况下，只需要使用threading这个高级模块。
启动一个线程就是把一个函数传入并创建Thread实例，然后调用start()开始执行：

    import time, threading
    
    # 新线程执行的代码:
    def loop():
        print('thread %s is running...' % threading.current_thread().name)
        n = 0
        while n < 5:
            n = n + 1
            print('thread %s >>> %s' % (threading.current_thread().name, n))
            time.sleep(1)
        print('thread %s ended.' % threading.current_thread().name)
    
    print('thread %s is running...' % threading.current_thread().name)
    t = threading.Thread(target=loop, name='LoopThread')
    t.start()
    t.join()
    print('thread %s ended.' % threading.current_thread().name)

6.Lock

多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。
来看看多个线程同时操作一个变量怎么把内容给改乱了，我们定义了一个共享变量balance，初始值为0，并且启动两个线程，先存后取，理论上结果应该为0，但是，由于线程的调度是由操作系统决定的，当t1、t2交替执行时，只要循环次数足够多，balance的结果就不一定是0了。

    # multithread
    import time, threading
    
    # 假定这是你的银行存款:
    balance = 0
    
    def change_it(n):
        # 先存后取，结果应该为0:
        global balance
        balance = balance + n
        balance = balance - n
    
    def run_thread(n):
        for i in range(2000000):
            change_it(n)
    
    t1 = threading.Thread(target=run_thread, args=(5,))
    t2 = threading.Thread(target=run_thread, args=(8,))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print(balance)

如果我们要确保balance计算正确，就要给change_it()上一把锁，当某个线程开始执行change_it()时，我们说，该线程因为获得了锁，因此其他线程不能同时执行change_it()，只能等待，直到锁被释放后，获得该锁以后才能改。由于锁只有一个，无论多少线程，同一时刻最多只有一个线程持有该锁，所以，不会造成修改的冲突。创建一个锁就是通过threading.Lock()来实现：

    balance = 0
    lock = threading.Lock()
    
    def run_thread(n):
        for i in range(100000):
            # 先要获取锁:
            lock.acquire()
            try:
                # 放心地改吧:
                change_it(n)
            finally:
                # 改完了一定要释放锁:
                lock.release()

7.GIL锁

试试用Python写个死循环：

    import threading, multiprocessing
    
    def loop():
        x = 0
        while True:
            x = x ^ 1
    
    for i in range(multiprocessing.cpu_count()):
        t = threading.Thread(target=loop)
        t.start()

启动与CPU核心数量相同的N个线程，在4核CPU上可以监控到CPU占用率仅有102%，也就是仅使用了一核。
但是用C、C++或Java来改写相同的死循环，直接可以把全部核心跑满，4核就跑到400%，8核就跑到800%，为什么Python不行呢？
因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。
这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。
GIL是Python解释器设计的历史遗留问题，通常我们用的解释器是官方实现的CPython，要真正利用多核，除非重写一个不带GIL的解释器。
所以，在Python中，可以使用多线程，但不要指望能有效利用多核。如果一定要通过多线程利用多核，那只能通过C扩展来实现，不过这样就失去了Python简单易用的特点。
不过，也不用过于担心，Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。

8.ThreadLocal

在多线程环境下，每个线程都有自己的数据。一个线程使用自己的局部变量比使用全局变量好，因为局部变量只有线程自己能看见，不会影响其他线程，而全局变量的修改必须加锁。
全局变量local_school就是一个ThreadLocal对象，每个Thread对它都可以读写student属性，但互不影响。你可以把local_school看成全局变量，但每个属性如local_school.student都是线程的局部变量，可以任意读写而互不干扰，也不用管理锁的问题，ThreadLocal内部会处理。
可以理解为全局变量local_school是一个dict，不但可以用local_school.student，还可以绑定其他变量，如local_school.teacher等等。
ThreadLocal最常用的地方就是为每个线程绑定一个数据库连接，HTTP请求，用户身份信息等，这样一个线程的所有调用到的处理函数都可以非常方便地访问这些资源。

    import threading
    
    # 创建全局ThreadLocal对象:
    local_school = threading.local()
    
    def process_student():
        # 获取当前线程关联的student:
        std = local_school.student
        print('Hello, %s (in %s)' % (std, threading.current_thread().name))
    
    def process_thread(name):
        # 绑定ThreadLocal的student:
        local_school.student = name
        process_student()
    
    t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
    t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
    t1.start()
    t2.start()
    t1.join()
    t2.join()

9.优缺点

多进程模式最大的优点就是稳定性高，因为一个子进程崩溃了，不会影响主进程和其他子进程。（当然主进程挂了所有进程就全挂了，但是Master进程只负责分配任务，挂掉的概率低）著名的Apache最早就是采用多进程模式。
多进程模式的缺点是创建进程的代价大，在Unix/Linux系统下，用fork调用还行，在Windows下创建进程开销巨大。另外，操作系统能同时运行的进程数也是有限的，在内存和CPU的限制下，如果有几千个进程同时运行，操作系统连调度都会成问题。

多线程模式通常比多进程快一点，但是也快不到哪去，而且，多线程模式致命的缺点就是任何一个线程挂掉都可能直接造成整个进程崩溃，因为所有线程共享进程的内存。在Windows上，如果一个线程执行的代码出了问题，你经常可以看到这样的提示：“该程序执行了非法操作，即将关闭”，其实往往是某个线程出了问题，但是操作系统会强制结束整个进程。
在Windows下，多线程的效率比多进程要高，所以微软的IIS服务器默认采用多线程模式。由于多线程存在稳定性的问题，IIS的稳定性就不如Apache。为了缓解这个问题，IIS和Apache现在又有多进程+多线程的混合模式，真是把问题越搞越复杂。

计算密集型任务的特点是要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。这种计算密集型任务虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。
计算密集型任务由于主要消耗CPU资源，因此，代码运行效率至关重要。Python这样的脚本语言运行效率很低，完全不适合计算密集型任务。对于计算密集型任务，最好用C语言编写。

第二种任务的类型是IO密集型，涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。对于IO密集型任务，任务越多，CPU效率越高，但也有一个限度。常见的大部分任务都是IO密集型任务，比如Web应用。
IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少，因此，用运行速度极快的C语言替换用Python这样运行速度极低的脚本语言，完全无法提升运行效率。对于IO密集型任务，最合适的语言就是开发效率最高（代码量最少）的语言，脚本语言是首选，C语言最差。

# 五、其他

1.模块

    # 导入类
    from src.session1.common.PrintUtil import PrintUtil

    # 导入模块中的函数
    import mymodule
    mymodule.greeting("Bill")

    # 导入模块中的对象
    import mymodule
    a = mymodule.person1["age"]
    print(a)

    # 选择仅从模块导入部件，在使用from关键字导入时，请勿在引用模块中的元素时使用模块名称。示例：person1["age"]，而不是 mymodule.person1["age"]
    from mymodule import person1
    print (person1["age"])

2.日期

    import datetime

    # 2019-08-14 12:52:55.817273
    x = datetime.datetime.now()

    # 2020-05-17 00:00:00
    x = datetime.datetime(2020, 5, 17)

    # 格式化输出日期
    x.strftime("%Y")
    
    # 把datetime转换为timestamp，注意Python的timestamp是一个浮点数，整数位表示秒。
    x.timestamp() 

    # str转换为datetime
    cday = datetime.strptime('2015-6-1 18:19:59', '%Y-%m-%d %H:%M:%S')
    print(cday)

    # datetime加减，需要导入timedelta这个类：
    from datetime import datetime, timedelta
    now = datetime.now()
    now + timedelta(hours=10)
    now - timedelta(days=1)
    now + timedelta(days=2, hours=12)

3.正则表达式

    import re
    
    str = "China is a great country"
    # 返回包含所有匹配项的列表
    x = re.findall("a", str)
    
    # 搜索字符串中的匹配项，如果存在匹配则返回Match对象。如果有多个匹配，则仅返回首个匹配项。如果未找到匹配，则返回值None
    # Match 对象提供了用于取回有关搜索及结果信息的属性和方法：span()返回的元组包含了匹配的开始和结束位置。.string返回传入函数的字符串。group()返回匹配的字符串部分
    x = re.search("\s", str)

    # 返回一个列表，其中字符串在每次匹配时被拆分
    x = re.split("\s", str)
    
    # 把匹配替换为您选择的文本9
    x = re.sub("\s", "9", str)

4.包管理

    pip --version
    pip install camelcase
    pip uninstall camelcase
    pip list

    # anaconda安装后，配置如下几个到环境变量Path
    D:\anaconda3
    D:\anaconda3\Scripts
    D:\anaconda3\Library\bin

5.异常处理

    import logging

    try:
        x = "demofile.txt"
        f = open(x)
        f.write("something")
        raise Exception("Sorry")
    except NameError as e:
        print("Variable x is not defined")
        logging.exception(e)
    except:
        print("Something went wrong when writing to the file")
    else:
        print("Nothing went wrong")
    finally:
        f.close()

6.IO

(1)基础

    # 直接打印列表 
    print(list)

    # 输出不换行，自定义输出末尾是空格
    print("something",end=' ')

    # 输入
    x = input()

    # 文件打开个参数文件名称，模式。
    '''
    "r" - 读取，默认值，打开文件进行读取，如果文件不存在则报错。
    "a" - 追加 - 打开供追加的文件，如果不存在则创建该文件。
    "w" - 写入 - 打开文件进行写入，如果文件不存在则创建该文件。会覆盖任何已有的内容。
    "x" - 创建 - 创建指定的文件，如果文件存在则返回错误。
    "t" - 文本 - 默认值。文本模式。
    "b" - 二进制 - 二进制模式（例如图像）。
    '''
    f = open("demofile.txt", "rt")

    # 文件读取
    # 读取全部文本
    print(f.read())
    # 读取5个字符
    print(f.read(5))
    # 读取一行
    print(f.readline())
    # 遍历所有行
    for x in f:
        print(x)

    # 关闭文件
    f.close()

    # 写入文件
    f = open("demofile3.txt", "w")
    f.write("something")
    
    # 删除，注意必须导入os
    import os
    if os.path.exists("demofile.txt"):
        os.remove("demofile.txt")
    else:
        print("The file does not exist")
    # 删除空目录
    import os
    os.rmdir("myfolder")

    # 前面的通常都要在try ... finally代码块中。可以如下方式，更佳简洁，并且不必调用f.close()方法。
    with open('/path/to/file', 'r') as f:
        print(f.read())

(2)StringIO/BytesIO

StringIO：在内存中读写str。
BytesIO：在内存中读写二进制数据。

    # 写
    from io import StringIO
    f = StringIO()
    f.write('hello')
    f.write(' ')
    f.write('world!')
    print(f.getvalue())

    # 读
    from io import StringIO
    f = StringIO('Hello!\nHi!\nGoodbye!')
    while True:
        s = f.readline()
        if s == '':
            break
            print(s.strip())

    # 写
    from io import BytesIO
    f = BytesIO()
    f.write('中文'.encode('utf-8'))
    print(f.getvalue())
    
    # 读
    from io import BytesIO
    f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
    f.read()

(3)序列化

Python提供了pickle模块来实现序列化。

    # 把一个对象序列化并写入文件：
    import pickle
    d = dict(name='Bob', age=20, score=88)
    f = open('dump.txt', 'wb')
    pickle.dump(d, f)
    f.close()

    f = open('dump.txt', 'rb')
    d = pickle.load(f)
    f.close()
    print(d)

(4)JSON处理

    import json
    
    # 字符串转化为字典对象
    x =  '{ "name":"Bill", "age":63, "city":"Seatle"}'
    y = json.loads(x)
    print(y["age"])

    # 对象转化为JSON字符串,indent定义缩进的字符数
    x = {
        "name": "Bill",
        "age": 63,
        "married": True,
        "divorced": False,
        "children": ("Jennifer","Rory","Phoebe"),
        "pets": None,
        "cars": [
            {"model": "Porsche", "mpg": 38.2},
            {"model": "BMW M5", "mpg": 26.9}
        ]
    }
    
    print(json.dumps(x，indent=4))

    '''
    输出结果为：
    {"name": "Bill", "age": 63, "married": true, "divorced": false, "children": ["Jennifer", "Rory", "Phoebe"], "pets": null, "cars": [{"model": "Porsche", "mpg": 38.2}, {"model": "BMW M5", "mpg": 26.9}]}
    '''

7.collections是Python内建的一个集合模块，提供了许多有用的集合类。

(1)namedtuple

namedtuple是一个函数，它用来创建一个自定义的tuple对象，并且规定了tuple元素的个数，并可以用属性而不是索引来引用tuple的某个元素。
这样一来，我们用namedtuple可以很方便地定义一种数据类型，它具备tuple的不变性，又可以根据属性来引用，使用十分方便。

    from collections import namedtuple
    Point = namedtuple('Point', ['x', 'y'])
    p = Point(1, 2)
    print(p.x)
    print(p.y)
    
    # namedtuple('名称', [属性list]):
    Circle = namedtuple('Circle', ['x', 'y', 'r'])

(2)deque

deque是为了高效实现插入和删除操作的双向列表，适合用于队列和栈。
deque除了实现list的append()和pop()外，还支持appendleft()和popleft()，这样就可以非常高效地往头部添加或删除元素。

    q = deque(['a', 'b', 'c'])
    q.append('x')
    q.appendleft('y')
    print(q)
    q.popleft()
    print(q)

(3)defaultdict

使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict。

    from collections import defaultdict
    dd = defaultdict(lambda: 'N/A')
    dd['key1'] = 'abc'
    print(dd['key1']) # key1存在
    print(dd['key2']) # key2不存在，返回默认值'N/A'

(4)OrderedDict

使用dict时，Key是无序的。在对dict做迭代时，我们无法确定Key的顺序。如果要保持Key的顺序，可以用OrderedDict。
OrderedDict可以实现一个FIFO（先进先出）的dict，当容量超出限制时，先删除最早添加的Key。

    from collections import OrderedDict
    d = dict([('a', 1), ('b', 2), ('c', 3)])
    # OrderedDict的Key是有序的，注意，OrderedDict的Key会按照插入的顺序排列，不是Key本身排序：
    od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])

(5)Counter

Counter是一个简单的计数器，例如，统计字符出现的个数。Counter实际上也是dict的一个子类，上面的结果可以看出每个字符出现的次数。

    from collections import Counter
    c = Counter()
    for ch in 'programming':
        c[ch] = c[ch] + 1
    print(c)
    c.update('hello') # 也可以一次性update
    print(c)

(6)ChainMap

ChainMap可以把一组dict串起来并组成一个逻辑上的dict。ChainMap本身也是一个dict，但是查找的时候，会按照顺序在内部的dict依次查找。
什么时候使用ChainMap最合适？举个例子：应用程序往往都需要传入参数，参数可以通过命令行传入，可以通过环境变量传入，还可以有默认参数。我们可以用ChainMap实现参数的优先级查找，即先查命令行参数，如果没有传入，再查环境变量，如果没有，就使用默认参数。
下面的代码演示了如何查找user和color这两个参数。

    from collections import ChainMap
    import os, argparse
    
    # 构造缺省参数:
    defaults = {
        'color': 'red',
        'user': 'guest'
    }
    
    # 构造命令行参数:
    parser = argparse.ArgumentParser()
    parser.add_argument('-u', '--user')
    parser.add_argument('-c', '--color')
    namespace = parser.parse_args()
    command_line_args = { k: v for k, v in vars(namespace).items() if v }
    
    # 组合成ChainMap:
    combined = ChainMap(command_line_args, os.environ, defaults)
    
    # 打印参数:
    print('color=%s' % combined['color'])
    print('user=%s' % combined['user'])

    '''
    没有任何参数时，打印出默认参数：
    
    $ python3 use_chainmap.py
    color=red
    user=guest
    当传入命令行参数时，优先使用命令行参数：
    
    $ python3 use_chainmap.py -u bob
    color=red
    user=bob
    同时传入命令行参数和环境变量，命令行参数的优先级较高：
    
    $ user=admin color=green python3 use_chainmap.py -u bob
    color=green
    user=bob
    '''

8.上下文管理

(1)with

任何对象，只要正确实现了上下文管理，就可以用于with语句。
实现上下文管理是通过__enter__和__exit__这两个方法实现的。例如，下面的class实现了这两个方法。

    class Query(object):
    
        def __init__(self, name):
            self.name = name
    
        def __enter__(self):
            print('Begin')
            return self
    
        def __exit__(self, exc_type, exc_value, traceback):
            if exc_type:
                print('Error')
            else:
                print('End')
    
        def query(self):

    with Query('Bob') as q:
        q.query()

(2)使用@contextmanager

编写__enter__和__exit__仍然很繁琐，因此Python的标准库contextlib提供了更简单的写法，上面的代码可以改写如下。

    from contextlib import contextmanager
    
    class Query(object):
    
        def __init__(self, name):
            self.name = name
    
        def query(self):
            print('Query info about %s...' % self.name)
    
    @contextmanager
    def create_query(name):
        print('Begin')
        q = Query(name)
        yield q
        print('End')

    # @contextmanager这个decorator接受一个generator，用yield语句把with ... as var把变量输出出去，然后，with语句就可以正常地工作了：
    # @contextmanager让我们通过编写generator来简化上下文管理。
    with create_query('Bob') as q:
        q.query()

# 六、Python Web

1.数据库访问

    import mysql.connector
    mydb = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      passwd="yourpassword",
      database="mydatabase"
    )
    
    # 新增一条数据
    mycursor = mydb.cursor()
    sql = "INSERT INTO customers (name, address) VALUES (%s, %s)"
    val = ("John", "Highway 21")
    mycursor.execute(sql, val)
    mydb.commit()
    # 获取刚插入的行的id
    print(mycursor.rowcount, "record inserted. ID: ", mycursor.lastrowid)
    
    # 新增多条数据
    mycursor = mydb.cursor()
    sql = "INSERT INTO customers (name, address) VALUES (%s, %s)"
    val = [
      ('Peter', 'Lowstreet 4'),
      ('Amy', 'Apple st 652')
    ]
    mycursor.executemany(sql, val)
    mydb.commit()
    print(mycursor.rowcount, "was inserted.")
    
    # 查询
    import mysql.connector
    mydb = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      passwd="yourpassword",
      database="mydatabase"
    )
    mycursor = mydb.cursor()
    mycursor.execute("SELECT name, address FROM customers")
    # 返回所有行
    myresult = mycursor.fetchall()
    for x in myresult:
      print(x)
      
    # 返回结果的第一行
    myresult = mycursor.fetchone()
    print(myresult)
    
    # 带条件查询
    sql = "SELECT * FROM customers WHERE address = %s"
    adr = ("some address", )
    mycursor.execute(sql, adr)
    myresult = mycursor.fetchall()
    for x in myresult:
      print(x)
      
    # 删除
    sql = "DELETE FROM customers WHERE address = %s"
    adr = ("some address", )
    mycursor.execute(sql, adr)
    mydb.commit()
    print(mycursor.rowcount, "record(s) deleted")
    
    # 修改
    sql = "UPDATE customers SET address = %s WHERE address = %s"
    val = ("Valley 345", "Canyon 123")
    mycursor.execute(sql, val)
    mydb.commit()
    print(mycursor.rowcount, "record(s) affected")

# 七、参考

1.[W3school Python教程](https://www.w3school.com.cn/python/index.asp)

2.[菜鸟Python教程](https://www.runoob.com/python3/python3-tutorial.html)

3.[廖雪峰Python教程](https://www.liaoxuefeng.com/wiki/1016959663602400)

4.[厦门大学数据库实验室Python入门教程](http://dblab.xmu.edu.cn/blog/python/)

5.[C语言中文网Python教程](http://c.biancheng.net/python/)

6.[Unofficial Windows Binaries for Python Extension Packages](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

7.[W3school Numpy](https://www.w3school.com.cn/python/numpy_intro.asp)

8.[NumPy官网](http://www.numpy.org.cn/)

9.[菜鸟NumPy教程](https://www.runoob.com/numpy/numpy-tutorial.html)

10.[莫烦Python](https://mofanpy.com/)

