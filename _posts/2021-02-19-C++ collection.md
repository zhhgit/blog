---
layout: post
title: "C++知识整理"
description: C++知识整理
modified: 2021-02-19
category: Resources
tags: [Resources]
---

# C++基础

1.指针
(1)this为指向当前对象的，this->method()，this->filed。
(2)使用指针时必须对其初始化。

2.面向过程
(1)三种函数调用方式：(a)传值调用swap(int x, int y)。(b)指针调用swap(int *a, int *b)。(c)引用调用swap(int &a, int &b)。
(2)long int 在windows 64位占4字节，在Linux 64位占8字节。可以使用long long。

3.数组
(1)初始化：注意与Java的差别，int nums[] = {1,2,3};
(2)获取长度：int len = sizeof(nums) / sizeof(nums[0]);
(3)数组作为参数传递进函数时会退化为指针，获取长度错误；

4.运算
(1)类型最大最小值：(a)#include<limits>。(b)最大(numeric_limits::max)()，最小(numeric_limits::min)()
(2)比较最大最小值： #include<algorithm> max(a,b); min(a,b);

5.面向对象
(1)类的静态方法：(a)头文件中声明static void printNumVector(vector<int> &nums);(b)cpp文件中定义，void PrintUtil::printNumVector(vector<int> &nums){} (c)调用PrintUtil::printNumVector(nums);
(2)对象创建两种方式：(a)ListNode node;或者ListNode node(4);在栈内存创建，退出代码块{}后会释放内存。(2)ListNode node = new ListNode(4);在堆内存创建，一般需要delete手动释放内存。
(3)new操作会返回指针。

6.IO
(1)bool类型输出为true/false字符串，cout << boolalpha << var << endl;

7.string：
(1)#include <string>
(2)获取长度s.size()
(3)遍历：a).s[i]；b).for(auto c:str)
(4)构造：string("str"),string(1,'c')
(5)拼接：str1.append("aaa");
(6)数字转字符串：to_string(5);
(7)查找包含字符位置：s.find_first_not_of(" ");s.find_last_not_of(" ");查找不到就返回-1。

8.stack：
(1)#include<stack>
(2)初始化：stack<char> stack1;
(3)入栈：stack1.push(ele)
(4)出栈：stack1.pop()
(5)栈顶元素：stack1.top()

9.vector：
(1)头部应该有#include<vector>及using namespace std;
(2)初始化：vector<int> ret {1,2,3};
(3)获取长度：nums.size()
(4)获取某元素：nums[3]
(5)插入，末尾插入ret.push_back(1);

10.unordered_map：
(1)初始化；unordered_map<int,int> map;
(2)查找：map.find("key") != map.end()，end()方法返回迭代器指向尾后，如果不等即在map中存在为key的键。
(3)插入：map.insert({"key","value"});
(4)获取：map.at("key");