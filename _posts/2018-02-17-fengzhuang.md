---
layout: post
title:  "python之封装"
date:   2018-02-17 22:14:54
categories: python
tags: 封装 装饰器
author: ZYC
---

* content
{:toc}



# OOP三大特性之封装

### 1.什么是封装

封装指的是隐藏对象的属性和实现细节，仅对外公开接口，控制程序中属性的访问权限；

python中的权限分为两种

	1.公开 外界可以直接访问和修改
	
	2.私有 外界不能直接访问和修改,在当前类中可以直接修改和访问       
????           	

### 2.为什么需要封装

一.**封装属性**

对于属性而言,封装就为了限制属性的访问和修改,其目的是为了**保护数据安全**

例如:

学生对象拥有,姓名,性别,年龄,和身份证号,分数;其中身份证是一个相对隐私的数据,不应该让外界访问到;

分数属性,是一个非常关键的数据,决定学员能不能正常毕业,不应被随意修改;

二.**封装方法**

一个大的功能很多情况下是由很多个小功能组合而成的,而这些内部的小功能对于用户而言是没有意义的,所以封装方法的目的是为了**隔离复杂度**;

例如:

电脑的开机功能,内部需要启动BIOS,读取系统配置,启动硬盘,载入操作系统,等等一系列复杂的操作,但是用户不需要关心这些实现逻辑,只要按下开机键等待开机即可;

### 3.如何封装

在属性名前添加两个下划线`__`,将其设置为私有的

1.封装数据属性实例:网页中折叠

```python
class Student:
    def __init__(self,name,gender,age,id,score): # 初始化函数
        self.name = name
        self.gender = gender
        self.age = age
        self.__id = id  # 将id设置为私有的
        self.__score = score # 将score设置为私有的
    def test(self):
        print(self.__id)
        print(self.__score)

        
stu = Student("Jack","man",20,"320684198901010001",780)
#1.访问私有属性测试
#print(stu.id) #	直接访问到隐私数据
#print(stu.__id) # 换种写法
#以上两行代码均输出相似的错误 
#Traceback (most recent call last):
#  File "/Users/jerry/PycharmProjects/备课/写课件/test.py", line 102, in <module>
#    print(stu.id)
#AttributeError: 'Student' object has no attribute 'id'
#错误含义 在Student类的对象中没有一个id或__id属性

#2.修改私有属性测试
stu.score = 1 #	直接修改私有属性 由于语法特点,相当于给stu对象增加score属性
stu.__score = 2 #	直接修改私有属性 由于语法特点,相当于给stu对象增加__score属性
print(stu.score)
print(stu.__score)
#输出 1 
#输出 2

#看起来已经被修改了 调用函数来查看私有属性是否修改成功
stu.test()
#输出 320684198901010001
#输出 780
# 私有的数据没有被修改过
```

思考:封装可以**明确地区分内外**，封装的属性**可以直接在内部使用**，而**不能被外部直接使用**，然而定义属性的目的终归是要用，外部要想用类隐藏的属性，需要为其提供接口，让外部能够间接地使用到隐藏起来的属性，那这么做的意义何在?

答:可以在接口附加上对该数据操作的限制，以此完成对数据属性操作的严格控制。

```python
class Teacher:
    def __init__(self,name,age):
        # self.__name=name
        # self.__age=age
        self.set_info(name,age)

    def tell_info(self):
        print('姓名:%s,年龄:%s' %(self.__name,self.__age))
    def set_info(self,name,age):
        if not isinstance(name,str):
            raise TypeError('姓名必须是字符串类型')
        if not isinstance(age,int):
            raise TypeError('年龄必须是整型')
        self.__name=name
        self.__age=age


t=Teacher('egon',18)
t.tell_info()

t.set_info('egon',19)
t.tell_info()
```

2.封装函数属性实例:网页中折叠

```python
#取款是功能,而这个功能有很多功能组成:插卡、密码认证、输入金额、打印账单、取钱
#对使用者来说,只需要知道取款这个功能即可,其余功能我们都可以隐藏起来
#这么做即隔离了复杂度,同时也提升了安全性
class ATM:
    def __card(self):
        print('插卡')
    def __auth(self):
        print('用户认证')
    def __input(self):
        print('输入取款金额')
    def __bill(self):
        print('打印账单')
    def __take_money(self):
        print('取款')

    def withdraw(self):
        self.__card()
        self.__auth()
        self.__input()
        self.__print_bill()
        self.__take_money()

a=ATM()
a.withdraw()
```

### 4.python封装实现原理

```python
#其实这仅仅这是一种变形操作且仅仅只在类定义阶段发生变形
#类中所有双下划线开头的名称如__x都会在类定义时自动变形成：_类名__x的形式：

class A:
    __N=0 #类的数据属性就应该是共享的,但是语法上是可以把类的数据属性设置成私有的如__N,会变形为_A__N
    def __init__(self):
        self.__X=10 #变形为self._A__X
    def __foo(self): #变形为_A__foo
        print('from A')
    def bar(self):
        self.__foo() #只有在类内部才可以通过__foo的形式访问到.

#A._A__N是可以访问到的，
#这种，在外部是无法通过__x这个名字访问到。
a = A()
print(a.__dict__)
#输出 {'_A__X': 10}

#定义运行阶段的赋值操作
a.__Y = 1
print(a.__dict__)
#输出 {'_A__X': 10, '__Y': 1}    __y并没有发生变形

"""
变形原理总结:
1.这种机制也并没有真正意义上限制我们从外部直接访问属性，知道了类名和属性名就可以拼出名字：_类名__属性，然后就可以访问了，如a._A__N，即这种操作并不是严格意义上的限制外部访问，仅仅只是一种语法意义上的变形，主要用来限制外部的直接访问。
2.变形的过程只在类的定义时发生一次,在定义后的赋值操作，不会变形
"""
```

5.隐藏的函数不会被子类覆盖



### 6.封装之property

#### property是什么

property是一个装饰器,将一个方法伪装成普通属性,其特殊之处在于,该方法会在修改属性值时自动执行

与之对应的是setter与deleter装饰器:

	setter装饰的方法会在修改属性值时自动执行
	
	deleter装饰的方法会在删除属性值自动执行

#### 为什么需要property

当我们将一个属性设置为私有之后,就无法直接访问它们了,需要为其创建两个方法,一个用于访问,一个用于修改 ,但是对于使用者而言,私有的和普通都是属性,然而一个可以用点来访问,用等号来修改,另一个却要调用函数来存取,这就违反了**统一访问原则**

#### 使用property

```python
class Foo:
    def __init__(self,val):
        self.__NAME=val #将所有的数据属性都隐藏起来

    @property
    def name(self):
        return self.__NAME #obj.name访问的是self.__NAME(这也是真实值的存放位置)

    @name.setter
    def name(self,value):
        if not isinstance(value,str):  #在设定值之前进行类型检查
            raise TypeError('%s must be str' %value)
        self.__NAME=value #通过类型检查后,将值value存放到真实的位置self.__NAME

    @name.deleter
    def name(self):
        raise TypeError('Can not delete')

f=Foo('Jack') 

print(f.name) # 访问property属性
#输出 Jack
f.name="Rose" # 修改property属性 抛出异常'TypeError: 10 must be str'

del f.name # 删除property属性 抛出异常'TypeError: Can not delete' 
```

总结:property的作用是避免普通属性和私有属性使用方式不一致
