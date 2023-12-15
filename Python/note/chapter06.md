<!-- TOC -->

- [类的定义](#%E7%B1%BB%E7%9A%84%E5%AE%9A%E4%B9%89)
    - [1. 动态绑定属性和方法](#1-%E5%8A%A8%E6%80%81%E7%BB%91%E5%AE%9A%E5%B1%9E%E6%80%A7%E5%92%8C%E6%96%B9%E6%B3%95)
    - [2. 权限控制](#2-%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6)
- [继承](#%E7%BB%A7%E6%89%BF)
    - [1. 方法的重写](#1-%E6%96%B9%E6%B3%95%E7%9A%84%E9%87%8D%E5%86%99)
- [object类](#object%E7%B1%BB)
- [深拷贝和浅拷贝](#%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D)

<!-- /TOC -->
# 类的定义
在Python中一切皆对象
```python
# 自定义数据类型
class 类名():
    pass
# 创建对象的语言格式为
对象名=类名()
```

* 类属性
    * 直接定义在类中，方法外的变量
* 实例属性
    * 定义在`__init__()`方法中，使用`self`打点的变量
* 实例方法
    * 定义在类中的函数，而且自带参数`self`
* 静态方法
    * 使用修饰器`@staticmethod`修饰的方法
* 类方法
    * 使用修饰器`@classmethod`修饰的方法

```python
class Student():
    # 类属性：定义在类中，方法外的变量
    school='xxx'
    #初始方法
    def __init__(self,xm,age):
        self.name=xm
        self.age=age
    # 定义在类中的函数，称为方法，自带一个参数self
    def show(self):
        print(self.name)
        print(self.age)
    # 静态方法
    @staticmethod
    def sm():
        # print(self.name)
        # self.show()
        print('静态方法不能调用实例属性，也不能调用实例方法')
    # 类方法
    @classmethod
    def cm(clf)
        print('类方法不能调用实例属性，也不能调用实例方法')

# 创建类的对象
stu=Student('Qin',18)
# 实例属性，使用对象名进行打点调用
print(stu.name,stu.age)
# 类属性，直接使用类名打点调用
print(Student.school)
# 实例方法，使用对象名进行打点调用
stu.show()
# 类方法，直接使用类名打点调用
Student.cm()
# 静态方法
Student.sm()

# 给类的类属性赋值
Student.school='123'
```

## 动态绑定属性和方法
可以为某个对象绑定独有的属性或方法
```python
stu2=Student('XU',20)
# 为stu2动态绑定一个实例属性
stu2.gender='man'
print(stu2.name,stu2.age,stu2.gender)

# 动态绑定方法
def introduce():
    print()
stu2.fun=introduce
# fun就是stu2对象的方法
stu2.fun()
```
## 权限控制
通过对属性或方法添加单下划线、双下划线以及首尾双下划线来实现
* 单下划线
    * 表示protected受保护的成员
    * 被视为内部使用，允许类本身和子类访问，但实际上它可以被外部代码访问
* 双下划线
    * 表示private私有的成员
    * 只允许定义该属性或方法的类本身进行访问
* 首尾双下划线
    * 一般表示特殊的方法

```python
class Student():
    # 首尾双下划线
    def __init__(self,xm,age,gender):
        self._name=xm      # 表示受保护的，只能本类和子类访问
        self.__age=age     # 表示私有的，只能类本身去访问
        self.gender=gender # 普通的实例属性，类的内部、外部、子类都可以访问
    
    def _fun1(): # 受保护的
        print('子类及本身可以访问')

    def __fun2(): # 私有的
        print('只有本身可以访问')

    def show(self):
        self._fun1()      # 类本身访问受保护方法
        self.__fun2()     # 类本身访问私有方法
        print(self._name) # 受保护的实例属性
        print(self.__age) # 私有的实例属性
        print(self.gender)

stu=Student('Qin',18,'man')
# print(stu._name)
# print(stu.__age)
print(stu._Student__age)
```

```python
class Student():
    def __init__(self,xm,age,gender):
        self._name=xm      
        self.__age=age    
        self.gender=gender 

    # 使用@property 修改方法，将方法转成属性使用
    @property
    def age(self):
        return self.__age
    # 可写属性
    @age.setter
    def age(self,value):
        self.__age=value
stu=Student('Qin',18,'man')
print(stu.age) # stu.age 会去执行 stu.age()
stu.age=20
```

# 继承
* 在Python中一个子类可以继承多个父类
* 如果一个类没有继承任何类，那么这个类默认继承的是object类
```python
class 类名(父类1,父类2):
    pass
```

单继承
```python
class Person:
    def __init__(self,name,age):
        self.name=name
        self.age=age
    def show(self):
        pass

class Student(Person):
    def __init(self,name,age,stuno):
        super().__init__(name,age)
        self.stuno=stuno
```

多继承
```python
class BaseA:
    def __init__(self,name):
        self.name=name

class BaseB:
    def __init__(self,age):
        self.age=age

class D(BaseA,BaseB):
    def __init__(self,name,age):
        # 需要调用两个父类的初始化方法
        BaseA.__init__(name)
        BaseB.__init__(age)
```

## 方法的重写

```python
class Person:
    def __init__(self,name,age):
        self.name=name
        self.age=age
    def show(self):
        print('Person')

class Student(Person):
    def __init(self,name,age,stuno):
        super().__init__(name,age)
        self.stuno=stuno

    def show(self):
        print('Student')
```

# object类
| object类中特殊的方法 | 功能描述 |
|---|---|
| `__new__()` | 由系统调用，用于创建对象
| `__init__()` | 创建对象时手动调用
| `__str__()` | 对象的描述，返回值时str类型，默认输出对象的内存地址

对象的特殊方法
| 运算符 | 特殊方法 | 功能描述 |
|---|---|---|
| + | `__add__()` |
| - | `__sub__()` |
| <,<=,== | `__lt__()`  `__le__()` `__eq__()`|
| >,>=,!= | `__gt__()`  `__ge__()` `__ne__()`|
| *,/ | `__mul__()`  `__truediv__()`|
| %,// | `__mod__()`  `__floordiv__()`|
| ** | `__pow__()` |

```python
a=10
b=20
print(dir(a)) 
print(a+b)
print(a.__add__(b))
```

特殊属性

| 特殊属性 | 功能描述 |
|---|---|
|`obj.__dict__` | 对象的属性字典
|`obj.__class__` | 对象所属的类
|`class.__bases__` | 类的父类元组
|`class.__base__` | 类的父类(如果继承了多个类，结果只显示第一个父类)
|`class.__mro__` | 类的层次结构
|`class._subclasses_()` | 类的子类列表

# 深拷贝和浅拷贝

变量的赋值：只是形成两个变量，实际上还是指向**同一对象**

浅拷贝：拷贝时，对象包含的子对象内容不拷贝，源对象与拷贝对象会引用同一个子对象

深拷贝：使用copy模块的deepcopy函数

```python
cpu=CPU()   # 40D0
disk=Disk() # 4110

com=Computer(cpu,disk) # 4150
com1=com               # 4150
print(com,'子对象的内存地址',com.cpu,com.disk)
print(com1,'子对象的内存地址',com1.cpu,com1.disk)

# 类对象的浅拷贝
import copy
com2=copy.copy(com) # com2是新产生的对象，com2的子对象cpu和disk不变

# 类对象的深拷贝
com3=copy.deepcopy(com)
```