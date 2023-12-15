<!-- TOC -->

- [异常处理](#%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86)
    - [1. raise](#1-raise)
    - [2. 常见的异常类型](#2-%E5%B8%B8%E8%A7%81%E7%9A%84%E5%BC%82%E5%B8%B8%E7%B1%BB%E5%9E%8B)
- [函数的定义及使用](#%E5%87%BD%E6%95%B0%E7%9A%84%E5%AE%9A%E4%B9%89%E5%8F%8A%E4%BD%BF%E7%94%A8)
- [函数的参数传递](#%E5%87%BD%E6%95%B0%E7%9A%84%E5%8F%82%E6%95%B0%E4%BC%A0%E9%80%92)
- [函数的返回值](#%E5%87%BD%E6%95%B0%E7%9A%84%E8%BF%94%E5%9B%9E%E5%80%BC)
- [变量的作用域](#%E5%8F%98%E9%87%8F%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F)
- [匿名函数lambda](#%E5%8C%BF%E5%90%8D%E5%87%BD%E6%95%B0lambda)
- [常用函数](#%E5%B8%B8%E7%94%A8%E5%87%BD%E6%95%B0)

<!-- /TOC -->
# 异常处理

```python
try:
    pass
    raise Exception('Error')
except Exception as e:
    print(e)
except ZeroDivisionError:
    pass
except ValueError:
    pass
except BaseException:
    pass
else:
    pass # 没有抛异常要执行的操作
finally:
    pass # 无论是否产生异常都要执行的操作
```
## raise
抛出一个异常，从而提醒程序出现了异常情况，程序能够正确地处理这些异常情况
`raise [Exception类型(异常描述信息)]`
## 常见的异常类型
| 异常类型 | 描述说明 |
|---|---|
| ZeroDivisionError | 当除数为0时，引发的异常
| IndexError | 索引超出范围所引发的异常
| KeyError | 字典取值时key不存在的异常
| NameError | 使用一个没有声明的变量时引发的异常
| SyntaxError | Python中的语法错误
| ValueError | 传入的值错误
| AttributeError | 属性或方法不存在的异常
| TypeError | 类型不合适引发的异常
| IndentationError | 不正确的缩进所引发的异常


# 函数的定义及使用


```python
# 自定义函数
def  函数名称(参数列表):
    函数体
    [return 返回值列表]
# 函数调用
函数名(参数列表)
```
# 函数的参数传递
* 位置参数
    * 是指调用时的参数个数和顺序必须与定义的参数个数和顺序相同
* 关键字参数
    * 是在函数调用时，使用“形参名称=值”的方式进行传参，传递参数的顺序可以与定义时参数的顺序不同
* 默认值参数
    * 是在函数定义时直接对形式参数进行赋值，在调用时如果该参数不传值，将使用默认值
* 可变参数
    * 个数可变的位置参数
        * 在参数前加`*`（`*para`），para形式参数的名称
        * 函数调用时可接收任意个数的实际参数，并放在一个**元组**中
    * 个数可变的关键字参数
        * 在参数前加`**`（`**para`），para形式参数的名称
        * 函数调用时可接收任意多个“参数=值”形式的参数，并放在一个**字典**中
```python
# 个数可变的位置参数
def fun(*pare):
    print(type(pare)) # <class 'tuple'>
    for item in pare:
        print(item)

fun(10,20,30)
fun(10)
fun([10,20,30])
fun(*[10,20,30]) # 调用时参数前加*，将对列表解包

# 个数可变的关键字参数
def fun2(**kwpara):
    print(type(kwpara)) # <class 'dict'>
    for key,value in kwpara.items():
        print(key,'--->',value)

fun2(name='Qin',age=18)
d = {'name':'Qin', 'age':18}
#fun2(d) # TypeError: fun2() takes 0 positional arguments but 1 wai given
fun2(**d) # 调用时参数前加**，将对字典解包
```
# 函数的返回值
* 如果函数的运行结果需要在其他函数中使用，那么这个函数就应该被定义为带返回值的函数
* 函数的运行结果使用return关键字进行返回
* return可以出现在函数中的任意一个位置，用于结束函数
* 返回值可以是一个值，或多个值，如果返回的值是多个，结果是一个**元组**类型

# 变量的作用域
* 局部变量
    * 在函数定义处的参数和函数内部定义的变量
    * 函数执行结束，局部变量的生命周期也结束，仅在函数内部
* 全局变量
    * 在函数外定义的变量或函数内部使用`global`关键字修饰的变量
    * 程序运行结束全局变量的生命周期才结束
# 匿名函数lambda
lambda:是指没有名字的函数，这种函数只能使用一次，一般是在函数的函数体只有一句代码且只有一个返回值时，可以使用匿名函数来简化
```python
result=lambda 参数列表:表达式
```
# 常用函数
数据类型转换函数
| 函数 | 描述说明 |
|---|---|
|bool(obj)|获取指定对象obj的布尔值
|str(obj)|将指定对象obj转成字符串类型
|int(x)|将x转成int类型
|float(x)|将x转成float类型
|list(sequence)|将序列转成列表类型
|tuple(sequence)|将序列转成元组类型
|set(sequence)|将序列转成集合类型

数学函数
| 函数 | 描述说明 |
|---|---|
|abs(x)| 获取x的绝对值
|divmod(x,y)| 获取x与y的商和余数
|max(sequence)| 获取序列的最大值
|min(sequence)| 获取序列的最小值
|sum(iter)| 对可迭代对象进行求和操作
|pow(x,y)| 获取x的y次幂
|round(x,d)| 对x进行保留d位小数（四舍五入）

迭代器操作函数
| 函数 | 描述说明 |
|---|---|
|sorted(iter) | 对可迭代对象进行排序
|reversed(sequence)| 反转序列生成新的迭代器对象
|zip(iter1,iter2)| 将iter1与iter2打包成元组并返回一个可迭代的zip对象
|enumerate(iter)| 根据iter随想创建一个enumerate对象
|all(iter)| 判断可迭代对象iter中所有元素的布尔值是否都为True
|any(iter)| 判断可迭代对象iter中所有元素的布尔值是否都为False
|next(iter)| 获取迭代器的下一个元素
|filter(function,iter)| 通过指定条件过滤序列并返回一个迭代器对象
|map(function,iter)| 通过函数function对可迭代对象iter的操作返回一个迭代器对象