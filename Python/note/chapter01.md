<!-- TOC -->

- [基本的输出函数print](#%E5%9F%BA%E6%9C%AC%E7%9A%84%E8%BE%93%E5%87%BA%E5%87%BD%E6%95%B0print)
- [基本的输入函数input](#%E5%9F%BA%E6%9C%AC%E7%9A%84%E8%BE%93%E5%85%A5%E5%87%BD%E6%95%B0input)
- [Python中的注释](#python%E4%B8%AD%E7%9A%84%E6%B3%A8%E9%87%8A)
- [代码缩进](#%E4%BB%A3%E7%A0%81%E7%BC%A9%E8%BF%9B)
- [字符串](#%E5%AD%97%E7%AC%A6%E4%B8%B2)
    - [1. 格式化字符串](#1-%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2)
    - [2. 编码与解码](#2-%E7%BC%96%E7%A0%81%E4%B8%8E%E8%A7%A3%E7%A0%81)

<!-- /TOC -->
# 基本的输出函数print
语法结构：
```python
print(输出内容)
```
print()函数完整的语法格式
```python
print(value,...,sep=' ',end='\n',file=None)
```
示例
```python
a = 100 # 变量a，值为100
b = 50 # 变量b，值为50
print(a,b,'要么出众，要么出局')
# 100 50 要么出众，要么出局

print("北京", end='-->')
print('欢迎你') # 没有修改结束符，所以print之后会有一个空行
# 北京-->欢迎你

fp = open('note.txt', 'w')
print('北京欢迎你',file=fp) # 将“北京欢迎你”输出到note.txt文件中
fp.close()
```

# 基本的输入函数input
语法结构：
```python
x=input('提示文字')
```
注：无论输入的数据是什么，x的数据类型都是**字符串类型**

示例
```python
num = input("请输入您的幸运数字：")
print("我的幸运数字是："+num) # 连接成功，说明num是字符串类型
# 我的幸运数字是：8
num = int(num) # 使用内置函数int()将num转成整数类型
print("我的幸运数字是：",num)
# 我的幸运数字是： 8  # 多了空格，这是因为seq=' '
```

# Python中的注释
* 单行注释：以`#`开头，`#`右边的所有文字当作说明，起辅助说明作用。`#`号和注释内容一般建议以一个空格隔开.

```python
# 单行注释
```

* 多行注释： 以一对三个双引号 引起来 (“”" “”")来解释说明一段代码的作用使用方法
```python
'''
多行注释1
'''

"""
多行注释2
"""
```
* 中文声明注释
```python
# coding = utf-8
# 中文声明注释，一定写在第一行
```

# 代码缩进
Python使用代码缩进来表示程序间的包含和层次关系；类定义、函数定义、流程控制语句以及异常处理语句等**行尾的冒号**和**下一行的缩进**表示一个代码块的开始，而所进结束，则表示一个代码块的结束；通常情况下采用4个空格作为一个缩进量

# 字符串
字符串是Python中的**不可变数据类型**
| 方法 | 描述说明 |
|---|---|
| str.lower() | 将str字符串全部转成小写字母，结果为一个新的字符串 |
| str.upper() | 将str字符串全部转成大写字母，结果为一个新的字符串 |
| str.split(sep=None) | 把str按照指定的分隔符sep进行分隔，结果为**列表类型**
| str.count(sub)      | 结果为sub在str中出现的次数
| str.find(sub)       | 查询sub这个字符串在str中是否存在，如果不存在结果为-1，如果存在结果为sub首次出现的索引
| str.index(sub)      | 功能与find()相同，区别在于要查询的子串sub不存在时，程序**报错**
| str.startswith(s)   | 查询字符串str是否以子串s开头
| str.endswith(s)     | 查询字符串str是否以子串s结尾        
| str.replace(old,news) | 使用news替换字符串s中所有的old字符串，结果是一个新的字符串
| str.center(width,fillchar) | 字符串str在指定的宽度范围内居中，可以使用fillchar进行填充
| str.join(iter) |  在iter中的每个元素的后面都增加一个新的字符串str
| str.strip(chars)  |         从字符串中去掉左侧和右侧chars中列出的字符
| str.lstrip(chars) |         从字符串中去掉左侧和右侧chars中列出的字符
| str.rstrip(chars) |         从字符串中去掉左侧和右侧chars中列出的字符

## 格式化字符串

* 占位符
    * `%s`:字符串格式
    * `%d`:十进制整数格式
    * `%f`:浮点数格式
* f-string:
    * Python3.6引入的格式化字符串的方式，`{}`标明被替换的字符
* `str.format()`方法
    * 模板字符串.format(逗号分隔的参数)

```python
# 使用占位符进行格式化
name='Qin'
age=18
score=80.5
print('name:%s, age:%d, score:%f' % (name,age,score))
print('name:%s, age:%d, score:%.1f' % (name,age,score))

# f-string
print(f'name:{name}, age:{age}, score:{score}')

# 字符串的format方法
print('name:{0}, age:{1}, score:{2}'.format(name,age,score))
print('name:{2}, age:{0}, score:{1}'.format(age,score,name))
```

格式化字符串的详细格式
| : | 填充 | 对齐方式 | 宽度 | ， | .精度 | 类型 |
|---|---|---|---|---|---|---|
|引导符号| 用于填充单个字符 |< 左对齐; > 右对齐; ^ 居中对齐 |字符串的输出宽度|数字的前卫分隔符| 浮点数小数部分的精度或字符串的最大输出长度| 整数类型：b\d\o\x\X 浮点数类型：e\E\f\%

```python
s='helloworld'
print('{0:*<20}'.format(s))
print('{0:*<20}'.format(s))
print('{0:*^20}'.format(s))
# 千位分隔符 （只适用于整数和浮点数）
print('{0:,}'.format(987654321))
# 浮点数小数部分的精度
print('{0:.2f}'.format(3.1415926))
# 字符串类型，表示最大显示长度
print('{0:.5}'.format('helloworld')) # hello
# 整数进制
print('{0:b}, {0:d}, {0:o}, {0:x}'.format(425))
```
## 编码与解码
* 将字符串转换成bytes类型，需要用到字符串的encode()方法
`str.encode(encoding='utf-8', errors='strict/ignore/replace')`
* 将bytes类型转换成字符串类型，需要用到字符串的decode()方法
`bytes.decode(encoding='utf-8', errors='strict/ignore/replace')`

```python
# 去重
s='hellohelloworldworld'
new_s=set(s)
lst=list(new_s)
lst.sort(key=s.index)
print(''.join(lst))
```