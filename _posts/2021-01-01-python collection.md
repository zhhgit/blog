---
layout: post
title: "Python知识整理"
description: Python知识整理
modified: 2021-01-01
category: Resources
tags: [Resources]
---

# Python基础

1.字符串
(1)分割：str.split("/",-1)，其中-1表示分割所有
(2)比较：s1 == s2,s1 != s2
(3)转化为小写、大写字母：str.lower(),str.uppper()
(4)拼接："".join(list)
(5)去除两侧、左侧、右侧空格：str.strip(), str.lstrip(), str.rstrip()
(6)str.isspace()是否为空字符串，str.isalpha()是否为字母，str.isdigit()是否为数字
(7)数字转字符串str("123")，字符串转数字int("9")
(8)拼接：str1 + str2
(9)截取：某一位str[i]，某一段str[i:j]

2.列表
(1)浅拷贝：(a)listb=lista[:],(b)listb=list(lista),(c)listb=[i for i in lista],(d)import copy;listb=copy.copy(lista)
(2)深拷贝：import copy;listb=copy.deepcopy(lista)
(3)对象列表排序，list.sort(key = sortMethod)，其中sortMethod为排序方法，return的是排序依据的字段。
(4)列表排序list.sort()，倒序list.sort(reverse = True)
(5)判断是否包含某元素：if ele in arr，或者判断list.count(obj) == 0
(6)反转：list.reverse(),该方法没有返回值，需要注意
(7)末尾添加：arr.append(i)
(8)数字数组遍历
    for i in arr
        print(i)
(9)二维数组初始化：initMatrix = [[0 for i in range(m)] for j in range(n)]

3.字典
(1)判断是否存在key： key in dict，返回True或者False
(2)添加：dict[key] = value
(3)删除：dict.pop(key)

4.面向过程
(1)函数
def funcName(self,param1,param2):
    do something
    return something

(2)判断
  (a)
  if condition1:
    do something1
  elif condition2:
    do something2
  else:
    do something3

  (b)与或非and or not
  (c)is None, is not None 返回True或者False

(3)循环
  (a)
  while condition :
    do something

  (b)正序
  for i in range(0,len(nums)):
    do_something(nums[i])
  其中，.range()函数返回的不是数组，而是一个整数序列的对象，len()返回对象（字符、列表、元组等）长度或项目个数

  (c)倒序
  for i in range(len(arr) - 1,-1,-1)

(4)对于字典和列表是传递的对象引用，即可以修改原对象，对于数字、字符串、元组是传递的值。

5.面向对象
(1)初始化：s = Solution()
(2)类方法：@staticmethod，方法参数中没有self
(3)引入：from src.session1.common.PrintUtil import PrintUtil
(4)调用：类中的某个方法调用其他方法self.another_method()
(5)私有属性：self.__attr
(6)构造函数
    def __init__(self, x):
        self.val = x
        self.next = None
(7)函数命名：尽量用下划线命名的方式 def some_method(self)

6.IO
(1)print(list)
(2)输出不换行print("something",end=' '),自定义输出末尾是什么

7.运算
(1)取整 //,取余 %
(2)数字次方：2**31
(3)最大、最小值函数max(),min()

8.集合
(1)队列：from collections import deque，有append(),popleft()方法