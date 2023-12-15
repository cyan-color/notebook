<!-- TOC -->

- [分支结构](#%E5%88%86%E6%94%AF%E7%BB%93%E6%9E%84)
- [循环结构](#%E5%BE%AA%E7%8E%AF%E7%BB%93%E6%9E%84)
    - [1. 遍历循环for](#1-%E9%81%8D%E5%8E%86%E5%BE%AA%E7%8E%AFfor)
    - [2. 无限循环while](#2-%E6%97%A0%E9%99%90%E5%BE%AA%E7%8E%AFwhile)
- [模式匹配](#%E6%A8%A1%E5%BC%8F%E5%8C%B9%E9%85%8D)

<!-- /TOC -->
# 分支结构
按照条件选择执行不同的代码段
* 单分支结构
```python
if 表达式:
    语句块
```
* 双分支结构
```python
if 表达式:
    语句块1
else:
    语句块2
```
* 多分支结构
```python
if 表达式:
    语句块1
elif 表达式:
    语句块2
else:
    语句块3
```
* 嵌套分支

单分支结构、双分支结构和多分支结构在实际开发中是可以互相嵌套使用的，内层的分支结构将作为外层分支结构的语句块使用
# 循环结构
## 遍历循环for
```python
for 循环变量 in 遍历对象:
    语句块

for 循环变量 in 遍历对象:
    语句块
else:
    语句块
```

```python
for i in 'hello':
    print(i)

sum=0;
for i in range(1,11):  # range()函数，产生一个[n，m)的整数序列
    sum+=i
else:
    print('sum:{sum}') # 正常执行完毕后运行此句
```
## 无限循环while
```python
while 表达式:
    语句块

while 表达式:
    语句块
else:
    语句块
```
```python
sum=0
i=1             # (1) 初始化变量
while i<=100:   # (2) 条件判断
    sum+=i      # (3) 语句块
    i+=1        # (4) 改变变量
else:
    print('1-100的累加和为：', sum)
```

# 模式匹配

```python
# 3.11新特性 模式匹配
score = 'A'
match score:
    case 'A':
        print('A')
    case 'B':
        print('B')
    case 'C':
        print('C')
    case 'D':
        print('D')
```

结构模式匹配
```python
match data:
    case {}:
        pass
    case []:
        pass
    case ():
        pass
    case _:
        pass
```
同步迭代
```python
match data1,data2:
    case data1,data2:
        pass
```