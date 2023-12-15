<!-- TOC -->

- [序列](#%E5%BA%8F%E5%88%97)
- [列表](#%E5%88%97%E8%A1%A8)
    - [1. 列表的创建](#1-%E5%88%97%E8%A1%A8%E7%9A%84%E5%88%9B%E5%BB%BA)
    - [2. 列表的遍历](#2-%E5%88%97%E8%A1%A8%E7%9A%84%E9%81%8D%E5%8E%86)
    - [3. 列表的相关操作方法](#3-%E5%88%97%E8%A1%A8%E7%9A%84%E7%9B%B8%E5%85%B3%E6%93%8D%E4%BD%9C%E6%96%B9%E6%B3%95)
    - [4. 列表排序](#4-%E5%88%97%E8%A1%A8%E6%8E%92%E5%BA%8F)
- [元组](#%E5%85%83%E7%BB%84)
    - [1. 元组的创建](#1-%E5%85%83%E7%BB%84%E7%9A%84%E5%88%9B%E5%BB%BA)
    - [2. 元组的遍历](#2-%E5%85%83%E7%BB%84%E7%9A%84%E9%81%8D%E5%8E%86)
- [字典](#%E5%AD%97%E5%85%B8)
    - [1. 字典的创建](#1-%E5%AD%97%E5%85%B8%E7%9A%84%E5%88%9B%E5%BB%BA)
    - [2. 字典的操作](#2-%E5%AD%97%E5%85%B8%E7%9A%84%E6%93%8D%E4%BD%9C)
    - [3. 字典的遍历](#3-%E5%AD%97%E5%85%B8%E7%9A%84%E9%81%8D%E5%8E%86)
    - [4. 新特性](#4-%E6%96%B0%E7%89%B9%E6%80%A7)
- [集合](#%E9%9B%86%E5%90%88)
    - [1. 集合的创建](#1-%E9%9B%86%E5%90%88%E7%9A%84%E5%88%9B%E5%BB%BA)
    - [2. 集合的操作](#2-%E9%9B%86%E5%90%88%E7%9A%84%E6%93%8D%E4%BD%9C)
    - [3. 集合的遍历](#3-%E9%9B%86%E5%90%88%E7%9A%84%E9%81%8D%E5%8E%86)
- [enumerate函数](#enumerate%E5%87%BD%E6%95%B0)

<!-- /TOC -->
# 序列
序列是一个用于存储多个值的连续空间，每个值都对应一个整数的编号（索引）
```python
s='helloworld'
# 正向递增
for i in range(0:len(s)):
    print(i,s[i],end='\t\t')
print()
# 反向递减
for i in range(-len(s),0):
    print(i,s[i],end='\t\t')
print()    
```

切片操作的语法结构：
```python
序列[start : end : step]
"""
start: 切片的开始索引（包含）
end: 切片的结束索引（不包含）
step：步长，默认为 1
""""
```
```python
# 步长为 -1
print(s[::-1]) # 可以使用那句代码替换呢
print(s[-1:-11:-1])
```

| 操作符/函数 | 描述说明 |
|---|---|
| x in s |
| x not in s |
| len(s) | 序列 s 中元素的个数（即序列的长度）
| max(s) | 序列 s 中元素的最大值
| min(s) | 序列 s 中元素的最小值
| s.index(s) | 序列 s 中首次出现元素 x 的索引（**值不存在会报错**）
| s.count(x) | 序列 s 中出现 x 的总次数
# 列表
* 是指以系列的按特定顺序排列的元素组成
* 是Python中内置的**可变**序列
* 在Python中使用`[]`定义列表，元素与元素之间使用逗号分隔
* 列表的元素可以是任意的数据类型
* 列表是序列中的一种，对序列的操作符、运算符、函数均可以使用

## 列表的创建

* 使用`[]`直接创建列表
```python
列表名=[element1, element2,...elementN]
```
* 使用内置函数`list()`创建列表
```python
列表名=list(序列)
```
* 列表生成式
```python
lst = [expression for item in range]
lst = [expression for item in range if condition]
```
## 列表的遍历
```python
# 使用遍历循环for遍历列表元素
for item in lst:
    print(item)

# 使用for循环，range()函数，len()函数，根据索引进行遍历
for i in range(0,len(lst)):
    print(i,'-->',lst[i])

# enumerate函数
for index,item in enumerate(lst):
    print(index,'-->',item) # index是enumerate的序号，不是索引
# 手动修改序号的起始值
for index,item in enumerate(lst,start=1):
    print(index,'-->',item)
```
## 列表的相关操作方法
| 列表的方法 | 描述说明 |
|---|---|
| lst.append(x) | 在列表lst最后增加一个元素
| lst.insert(index,x) | 在列表lst中index位置增加一个元素
| lst.clear() | 清除列表lst中所有元素
| lst.pop(index) | 将列表lst中第index位置的元素取出，并从列表中将其删除
| lst.remove(x) | 将列表lst中出现的第一个元素x删除
| lst.reverse() | 将列表lst中的元素反转(在原列表上进行)
| lst.copy() | 拷贝列表lst中的所有元素，生成一个新的列表
| lst.sort(key=None,reverse=False) | 列表排序(在原列表上进行)

## 列表排序
* 列表对象的`sort`方法
```python
# 排序在原列表的基础上进行，不产生新的列表对象
lst.sort(key=None,reverse=False) # 默认升序

# 忽略大小写进行比较
lst.sort(key=str.lower,reverse=False)
```
* 内置函数`sorted`方法
```python
sorted(iterable,key=None,reverse=False)

# 忽略大小写进行比较
new_lst=sorted(lst,key=str.lower,reverse=False)
```

# 元组
* 是Python中内置的**不可变序列**
* 在Python中使用`()`定义元组，元素与元素之间使用逗号分隔
* 元组中**只有一个**元素时，逗号也不能省略

## 元组的创建

* 使用`()`直接创建列表
```python
元组名=(element1, element2,...elementN)
```
* 使用内置函数`tuple()`创建列表
```python
元组名=tuple(序列)
```
* 元组生成式
```python
t = (expression for item in range)
t = (expression for item in range if condition)
t = tuple(t)
```
示例
```python
t=(i for i in range(1,4))
print(t)            # <generator object <genexpr> at 0x>

# 遍历
print(t.__next__()) # 1
print(t.__next__()) # 2
print(t.__next__()) # 3

t=tuple(t)
print(t)            # ()
```

## 元组的遍历
```python
# 使用遍历循环for遍历列表元素
for item in t:
    print(item)

# 使用for循环，range()函数，len()函数，根据索引进行遍历
for i in range(0,len(t)):
    print(i,'-->',t[i])

# enumerate函数
for index,item in enumerate(t):
    print(index,'-->',item) # index是enumerate的序号，不是索引
# 手动修改序号的起始值
for index,item in enumerate(t,start=1):
    print(index,'-->',item)
```
# 字典
字典类型时根据一个信息查找另一个信息的方式构成了“键值对”，它表示索引用的键和对应的值构成的成对关系

## 字典的创建
* 使用`{}`直接创建字典 
```python
d = {key1:value1, key2:value2,...}
```
* 使用内置函数`dict()`创建字典
```python
dict(key1=value1, key2=value2,...)
```
* 通过映射函数创建字典 
```python
zipobj = zip(lst1,lst2) 
print(zipobj) # <zip object at 0x000...>
print(list(zipobj)) # [(key1,value1), (key2,value2),...]
d=dict(zipobj)
```
* 字典生成式
```python
lst = [key:value for item in range]
lst = [key:value for key,value in zip(lst1,lst2)]
```
## 字典的操作
* 字典属于序列
* 字典元素的取值：`d[key]` 或 `d.get(key)`
```python
# 两者之间是有去别的，如果key不存在，d[key]报错；d.get(key)可以指定默认值
print(d.get('java')) # None
print(d.get('java','不存在'))
```

| 方法 | 描述说明 |
|---|---|
| d.keys() | 获取所有key数据
| d.values() | 获取所有value数据
| d.pop(key,default) | key存在则获取相应的value同时删除该键值对；否则获取默认值
| d.popitem() |        **随机**从字典中取出一个键值对，结果为元组类型，同时将该键值对从字典中删除
| d.clear() | 清空字典中的所有键值对

```python
# 获取字典中所有的key
keys = d.keys()
print(keys) # dict_keys([1001,1002,1003,1004])
print(list(keys))  # [1001,1002,1003,1004]
print(tuple(keys)) # (1001,1002,1003,1004)

# 获取字典中所有value
values = d.values()
print(values) # dict_values([1001,1002,1003,1004])
print(list(values))  # [1001,1002,1003,1004]
print(tuple(values)) # (1001,1002,1003,1004)

# 如果将字典中的数组转成key-value的形式，以元组的方式展现
lst=list(d.items())
print(lst)
```
## 字典的遍历
```python
# 使用遍历循环for遍历列表元素
for item in d.items():
    print(item) # 元组 (key,value)

for key,value in d.items():
    print(key,'-->',value)

for key in list(d.keys()):
    print(key,'-->',d[key])
```
## 3.11新特性
字典合并运算符 `|`
# 集合
* Python中的集合与数学中的集合的概念一致
* Python中的集合是一个**无序的不重复**元素序列
* 集合中只能存储**不可变数据类型**
* 在Python中集合使用`{}`定义
* 与列表、字典一样，都是Python中的可变数据类型
## 集合的创建
* 使用`{}`直接创建字典 
```python
s = {element1, element2,...}
```
* 使用内置函数`set()`创建字典
```python
s=set(可迭代对象,...)
```
* 集合生成式
```python
s = {item for item in range}
s = {item for item in range if condition}
```

## 集合的操作
集合属于序列的一种

| 操作符 | 描述说明 | 示例 |
|---|---|---|
| & | 交集 | A&B |
| \| | 并集 | A\|B |
| - | 差集 | A-B |
| ^ | 补集 | A^B |

| 方法 | 描述说明 |
|---|---|
| s.add(x)  | 如果x不在集合s中，则将x添加到集合s中
| s.remove(x) | 如果x在集合中，将其删除；如果不在集合中，程序**报错**
| s.clear() | 清除集合中所有元素
## 集合的遍历
```python
for item in s:
    print(item) 

for index,item in enumerate(s):
    输出 index 和 item
```
# enumerate函数

```python
for index,item in enumerate(lst):
    输出 index 和 item
```
