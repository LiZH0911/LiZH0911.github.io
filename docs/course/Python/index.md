# Python

相关链接：

- [黑马程序员 Python+AI](https://www.bilibili.com/video/BV1sHU9BmEne/?spm_id_from=333.1387.favlist.content.click&vd_source=46f99c7c1ed609a31f70615a4551767f)
- [小白学 Python](https://walter201230.github.io/Python/)

## 一、数据存储与运算

### 1.1 **字面量**

**定义**：字面量是程序中直接书写的固定值（数据）。

**字面量的种类**：

- 字符串：`"Hello, World!"`
- 整数：`123`
- 浮点数：`3.14`
- 布尔值：`True/False`
- 空值：`None`

### 1.2 **变量**

**定义**：变量是程序中存储数据的容器（经常会发生改变的数据）。

**用途**：输出打印、参与计算、记录数据

> **注意事项**：

> 1. 一个变量只能存储一个值
> 2. 变量定义的时候必须赋值才可以使用
> 3. 一条语句可以定义多个变量，也可以连续赋值（`a,b=1,"Python"`）

### 1.3 **标识符**

**定义**：标识符是程序员在代码中为变量、函数、类等元素所起的名字。

**标识符的命名规则（规定）**：

1. 只能包含字母(`a-z,A-Z`)、数字(`0-9`)、下划线(`_`)
2. 不能以数字开头
3. 不能是关键字`True, False, None, and, or, if, else, elif, for, while`等
4. 区分大小写

**变量的命名规范**：

1. 见名知意
2. 多个部分使用下划线连接（<span style="color: red;">蛇形命名法</span>）
3. 英文字母全小写

### 1.4 **常见数据类型**

**基本数据类型**：

- 字符串：str
- 整数：int
- 浮点数：float
- 布尔值：bool
- 空值：None

**数据类型查看**：

1. `type()`
2. `isinstance()`

**数据类型转换**：

- `str()`：转换为字符串
- `int()`：转换为整数
- `float()`：转换为浮点数
- `bool()`：转换为布尔值
- `list()`：转换为列表
- `tuple()`：转换为元组
- `dict()`：转换为字典
- `set()`：转换为集合

### 1.5 **字符串**

**定义方式**：

1. 单引号：`'Hello, World!'`
2. 双引号：`"Hello, World!"`
3. 三引号：`'''Hello, World!'''`

**常见转义字符**：

- 换行符：`\n`
- 制表符：`\t`
- 单引号：`\'`
- 双引号：`\"`

**使用原则**：

1. 单引号与双引号是等效的，项目中保持一种写法即可
2. 三引号用于多行字符串场景

**字符串拼接**：`+, *`

**字符串格式化**：

1. `format()`

    示例：`print("Hello, {}!".format("World"))`

2. `%s`

    示例：`print("Hello, %s!" % "World")`

3. `f-string`

    示例：`print(f"Hello, {name}!")`

### 1.6 **输入与输出**

**输入**：`s = input("请输入：")`

> 注意：无论键盘输入什么类型的数据，获取到的数据都是字符串类型

**输出**：`print(s)`

### 1.7 **运算符**

**算术运算符**：`+, -, *, /, //, %, **`

**赋值运算符**：`=, +=, -=, *=, /=, //=, %=, **=`

**比较运算符**：`==, !=, >, <, >=, <=`

**逻辑运算符**：`and, or, not`


## 二、流程控制语句

### 2.1 **if 条件判断**

```Python
if 条件表达式1:
    执行操作1
elif 条件表达式2:
    执行操作2
else:
    执行操作3
```

### 2.2 **while 循环**

```Python
while 条件表达式:
    循环体
```

### 2.3 **for 循环**

```Python
for 元素 in 数据集:
    循环体
```

## 三、数据容器

### 3.1 **列表 list**

```Python
# 定义列表
列表名称 = [元素1, 元素2, ...]
```

**特点**：元素类型可不同，元素有序（支持索引访问、切片）、可重复、可修改

**切片**：对有序序列进行切片，返回一个新的有序序列。<span style="color: red;">左闭右开，步长为负表示逆向切片</span>。

```Python
序列数据[开始索引:结束索引:步长]
```

**列表常用方法**：

- `len()`：返回列表的长度
- `append()`：添加元素
- `insert()`：在指定位置添加元素
- `pop()`：删除指定位置的元素
- `remove()`：删除指定元素
- `index()`：返回指定元素的索引
- `count()`：返回指定元素的数量
- `reverse()`：反转列表
- `sort()`：排序列表
- `copy()`：复制列表
- `enumerate()`：返回一个枚举对象，枚举对象包含索引和元素
- `zip()`：返回一个迭代器，迭代器中包含两个列表中对应位置的元素
- `map()`：返回一个迭代器，迭代器中包含两个列表中对应位置的元素

### 3.2 **字符串 str**

```Python
# 定义字符串
字符串名称 = "字符串内容"
```

**特点**：元素均为字符，元素有序（支持索引访问、切片）、可重复、不可修改

**切片**：同列表

**字符串常用方法**：

- `find()`：查找字符串中指定的子串，返回第一次出现的索引位置
- `replace()`：将字符串中指定的子串替换为另一个子串
- `strip()`：去除字符串中两端的空白字符或指定字符
- `split()`：将字符串按指定分隔符分割为列表
- `upper()`：将字符串转换为大写
- `lower()`：将字符串转换为小写
- `swapcase()`：将字符串中的大写字母转换为小写，小写字母转换为大写
- `islower()`：判断字符串是否只包含小写字母
- `isupper()`：判断字符串是否只包含大写字母
- `capitalize()`：将字符串的第一个字符转换为大写
- `title()`：将字符串中每个单词的第一个字符转换为大写
- `index()`：返回字符串中指定的子串的索引
- `count()`：返回字符串中指定的子串出现的次数


### 3.3 **元组 tuple**

```Python
# 定义元组
元组名称 = (元素1, 元素2, ...)
# 定义空元组
元组名称 = ()
元组名称 = tuple()
```

**特点**：元素类型可不同，元素有序（支持索引访问、切片）、可重复、不可修改

**元组常用方法**：

- `index()`：返回元组中指定的元素索引
- `count()`：返回元组中指定的元素出现的次数

> 注意：单元素元组需要在结尾加逗号，如`(‘A’,)`

**元组的组包与解包**：

- 组包（Packing）：`t1 = (a,b,c,d)`
- 解包（Unpacking）：`x, y, *z = t1`，其中`*`表示收集剩余元素并存储为列表`z`

### 3.4 **集合 set**

```Python
# 定义集合
集合名称 = {元素1, 元素2, ...}
```

**特点**：元素类型可不同，元素无序（不支持索引访问、切片）、不可重复、可修改

> 注意：`{}`表示空字典，而不是空集合

**集合常用方法**：

- `add()`：添加元素
- `remove()`：删除指定元素
- `clear()`：清空集合
- `union()`：返回两个集合的并集
- `intersection()`：返回两个集合的交集
- `difference()`：返回两个集合的差集
- `issubset()`：判断一个集合是否是另一个集合的子集

### 3.5 **字典 dict**

```Python
# 定义字典
字典名称 = {key:value, key:value, ...}
# 定义空字典
字典名称 = {}
字典名称 = dict{}
# 访问字典元素
值 = 字典名称[key]
```

**特点**：

- 键值对（key : value）存储
- 通过键来查找值，且查询速度极快，内部使用了哈希表（Hash Table）技术
- 键不可重复且不可修改（键不能为列表、元组、字典等）
- 值可以是任意类型且可重复

**字典常用方法**：

- 增：`字典名称[key] = value`
- 删：`del 字典名称[key]`或`字典名称.pop(key)`
- 改：`字典名称[key] = value`
- 查：`字典名称[key]`或`字典名称.get(key)`
- 遍历：`for key, value in 字典名称.items():`
- `keys()`：返回字典中所有键的列表
- `values()`：返回字典中所有值的列表
- `items()`：返回字典中所有键值对的列表

## X、迭代器与生成器

### x.1 **迭代**

**迭代**：迭代（Iteration）就是“逐个取出”一个容器（比如列表、字符串）里的每一个元素，并对其执行相同操作的过程。

```python
# -*- coding: UTF-8 -*-

# 1、for 循环迭代字符串
for char in 'liangdianshui' :
    print ( char , end = ' ' )

print('\n')

# 2、for 循环迭代 list
list1 = [1,2,3,4,5]
for num1 in list1 :
    print ( num1 , end = ' ' )

print('\n')

# 3、for 循环也可以迭代 dict （字典）
dict1 = {'name':'两点水','age':'23','sex':'男'}

for key in dict1 :    # 迭代 dict 中的 key
    print ( key , end = ' ' )

print('\n')

for value in dict1.values() :   # 迭代 dict 中的 value
    print ( value , end = ' ' )

print ('\n')

# 如果 list 里面一个元素有两个变量，也是很容易迭代的
for x , y in [ (1,'a') , (2,'b') , (3,'c') ] :
    print ( x , y )
```

### x.2 **迭代器**

**迭代器**：一个可以记住遍历位置的对象。

**迭代器的两个基本方法**：`iter()` 和 `next()`

**迭代器的创建**：字符串、列表或元组对象都可用于创建迭代器

**迭代器的遍历**：可以使用常规 for 语句进行遍历，也可以使用 `next()` 函数来遍历。

```python
# 1、字符创创建迭代器对象
str1 = 'liangdianshui'
iter1 = iter(str1)

# 2、list对象创建迭代器
list1 = [1,2,3,4]
iter2 = iter(list1)

# 3、tuple(元祖) 对象创建迭代器
tuple1 = (1,2,3,4)
iter3 = iter(tuple1)

# for 循环遍历迭代器对象
for x in iter1:
    print (x, end = ' ')

print('\n------------------------')

# next() 函数遍历迭代器
while True:
    try:
        print (next(iter3))
    except StopIteration:
        break
```

输出结果：

```
l i a n g d i a n s h u i 
------------------------
1
2
3
4
```

### x.3 **列表生成式**

**列表生成式的创建**

```
[expr for iter_var in iterable] 
[expr for iter_var in iterable if cond_expr]
```

### x.4 **生成器**

**生成器**：生成器（Generator）是一种特殊的迭代器，它不一次性生成所有数据，而是按需生成（惰性计算）。

**生成器的使用场景**：不想同一时间将所有计算结果集分配到内存当中，特别是结果集里还包含循环。因为这样会耗费大量资源。

**生成器的创建（以函数形式）**：

实际运用中，大多数的生成器都是通过函数来实现的，使用 yield 关键字来返回数据并记住当前执行位置。

生成器和函数的执行流程不一样。函数是顺序执行，遇到 return 语句或者最后一行函数语句就返回。

而生成器在每次调用 next() 的时候执行，遇到 yield 语句返回，再次执行时从上次返回的 yield 语句处继续执行。

```python
def my_generator():
    print("开始")
    yield 1
    print("继续")
    yield 2
    print("结束")
    yield 3

# 调用函数不会执行代码，而是返回一个生成器对象
gen = my_generator()
print(gen)  # <generator object my_generator at 0x...>

# 用 next() 逐个触发执行
print(next(gen))  # 输出：开始 \n 1
print(next(gen))  # 输出：继续 \n 2
print(next(gen))  # 输出：结束 \n 3
print(next(gen))  # 报错 StopIteration
```

再比如一个计算斐波那契数列的生成器：

```python
# -*- coding: UTF-8 -*-
def fibon(n):
    a = b = 1
    for i in range(n):
        yield a
        a, b = b, a + b

# 引用函数
for x in fibon(1000000):
    print(x , end = ' ')
```

### x.5 **综合例子**

**反向迭代**

```python
list1 = [1,2,3,4,5]
for num1 in reversed(list1) :
    print (num1, end = ' ')
```

注意：反向迭代只有当**对象的大小可预先确定**或者**对象实现了 `__reversed__()` 的特殊方法**时才能生效。如果两者都不符合，那你必须先将对象转换为一个列表。

**同时迭代多个序列**

为了同时迭代多个序列，使用 `zip()` 函数

```python
# -*- coding: UTF-8 -*-
names = ['laingdianshui', 'twowater', '两点水']
ages = [18, 19, 20]
for name, age in zip(names, ages):
     print(name,age)
```


## 四、函数

### 4.1 **函数基础**

**函数介绍**：函数是组织好的、可重复使用的、用来实现特定功能的代码片段。

**函数的定义与调用**：
```Python
# 定义函数
def 函数名(参数列表):
   函数体
   return 返回值 # 返回值根据需求可有可无

#调用函数
函数名(参数)
```

**函数的说明文档**：函数开头用三个引号包裹的字符串，用于解释函数的功能、参数、返回值等信息，方便调用者清楚函数的具体作用及细节。

**函数的嵌套调用**：遵循栈结构，LIFO（后进先出）

### 4.2 **函数进阶**

**变量的作用域**：

- 局部变量：在函数内部定义的变量，只在函数内部有效
- 全局变量：在函数外部定义的变量，可以在整个程序中使用

**传参方式**：

1. 位置参数：按照参数定义的顺序传递参数
2. 关键字参数：按照参数名称传递参数 
3. 默认参数：在参数定义时赋默认值，调用时可不传递该参数 
4. 不定长参数（可变参数）：`def function(*args,**kwargs)`
    - 位置传递`*args`，元组类型
    - 关键字传递`**kwargs`，字典类型

**函数的参数类型**：

- 普通参数：数字、布尔、列表等
- 特殊参数：函数

**匿名函数**：匿名函数指的是没有名称的函数，需要通过 lambda 表达式来声明函数，可以简化函数的编写（单行表达式）。

```Python
# 定义匿名函数
lambda 参数列表 : 函数体

add = lambda x, y : x + y
print(add(3, 4))
```

### 4.2 **类型注解**

**类型注解**：类型注解是 Python 的一种语法特性，用于明确标识变量、函数参数和返回值的数据类型，但不会进行类型检查，只是作为代码的提示，从而使代码更清晰、更安全、更易维护。

```Python
# 变量类型注解
a: int = 100
# 函数类型注解
def calc(scores: list[int]) -> float
   return sum(scores) / len(scores)
```

> 常见类型：int、float、str、bool、None、list、tuple、dict、set、int | str

**类型推断**：类型推断是指 Python 解释器自动推断出变量、表达式或函数返回值的数据类型的能力，而无需开发者显式声明。

## 五、模块与包

**模块（module）**：一个.py 文件就是一个模块，模块是 Python 程序的基本组织单位。在模块中可以定义函数、类，以及可执行的代码。

- 使代码结构清晰，便于维护
- 提高代码复用性
- 避免命名冲突

**模块的导入方式**：

1. `import 模块名`
2. `import 模块名 as 别名`
3. `from 模块名 import 功能名`
4. `from 模块名 import 功能名 as 别名`
5. `from 模块名 import *`：从模块中导入所有函数，使用时不需要加模块名

**包（package）**：包即文件夹，包含若干 python 模块(.py 文件)，还包含了一个_init__.py。包用来管理多个模块（包本质也是一个模块）。

**包的导入方式**：

1. `import 包名.模块名`
2. `from 包名 import 模块名`
3. `from 包名 import *`
4. `from 包名.模块名 import 功能名`
5. `from 包名.模块名 import *`

## 六、面向对象基础

### 6.1 **概述**

**面向过程编程的思想**：把一个需求分解成一系列要执行的步骤，然后按照步骤依次执行这些任务（关注的是流程、步骤）。

**面向对象编程的思想**：把一个人/物的特征和功能打包到一起，是面向对象编程的基本单元（关注的是谁来帮我做这件事儿）。

### 6.2 **类与对象**

**类（class）**：描述的是一组具有相同属性（特征）和方法（功能/行为）的模板。

**对象（object）**：对象是类的实例，是基于类创建出来的（实例对象）。

**类的定义与实例化**：

```Python
# 定义类
class 类名：
   def __init__(self, 参数列表):
      self.属性名 = 参数值
   
   def 方法名(self, 参数列表):
      方法体
      
# 创建对象
对象名 = 类名(参数列表)
对象名.方法名(参数列表)
```

**类的命名规范**：类名应该使用大驼峰命名法（Camel Case），即每个单词的首字母大写，不使用下划线。

### 6.3 **方法**

**按归属划分**

- 实例方法：第一个参数必须是 self（代表对象本身），由对象（类的实例）调用
- 类方法：使用 @classmethod 装饰器。第一个参数必须是 cls（代表类本身，而不是实例）
- 静态方法：使用 @staticmethod 装饰器。不需要额外的参数（如 self 或 cls）

**按访问权限划分**

- 公有方法（Public）：普通命名，可以在类的内外部和子类中访问
- 保护方法（Protected）：命名以单下划线开头（如 _method），只能在类的内部和子类中访问
- 私有方法（Private）：命名以双下划线开头（如 __method），只能在类的内部访问

**魔法方法**：魔法方法是指 Python 中提供的以双下划线开头和结尾的特殊方法，用于定义类的特殊行为。

- `__init__`：初始化方法，用于初始化对象的属性
- `__str__`：默认输出对象的内存地址
- `__ep__`：默认基于对象的内存地址进行比较
- `__lt__`, `__le__`, `__gt__`, `__ge__`：默认自定义对象之间不能比较

### 6.4 **属性**

**按归属划分**

- 实例属性：实例属性是属于每个具体对象（实例）的属性
- 类属性：类属性是属于类本身的属性，所有对象（实例）共享

**按访问权限划分**

- 公有属性（Public）：普通命名，可以在类的内外部和子类中访问
- 保护属性（Protected）：命名以单下划线开头（如 _attribute），只能在类的内部和子类中访问
- 私有属性（Private）：命名以双下划线开头（如 __attribute），只能在类的内部访问

## 七、异常

### 7.1 **概述**

**异常（Exception）**：异常（也称为 Bug）是程序运行过程中出现的错误，如果异常没有被“捕获”并处理，它就会中断程序的正常执行流程。异常有助于开发者在开发阶段发现问题并解决问题，保障程序正常运行。

### 7.2 **常见异常**

- BaseException：所有异常的基类
- Exception：所有常规异常的基类
- ZeroDivisionError：除零异常
- TypeError：类型异常，如 `"1" + 1`, `len(5)`
- ValueError：值异常，操作或函数接收到了类型正确但值不合法的参数。如 `int("abc")`, `math.sqrt(-1)`
- ……

### 7.3 **异常处理**

**异常捕获**：使用 try-except 语句捕获异常

```Python
try:
    # 可能抛出异常的代码
    num = int(input("请输入数字: "))
    result = 10 / num
    print(result)
except ZeroDivisionError:
    # 捕获特定类型的异常
    print("错误：不能除以零！")
except ValueError:
    print("错误：请输入有效的数字！")
except Exception as e:
    # 捕获所有其他未知异常，并打印错误信息
    print(f"发生了未知错误: {e}")
else:
    # 如果没有发生异常，执行此块（可选）
    print("计算成功！")
finally:
    # 无论是否发生异常，都会执行（通常用于释放资源，如关闭文件）
    print("程序执行结束。")
```

## 八、面向对象高级

### 8.1 **封装**

**封装**：封装就是把数据（属性）和操作数据的函数（方法）捆绑在一起，形成一个独立的单元（类），并隐藏内部的实现细节（私有属性、私有方法），只对外暴露必要的功能（公共属性、公共方法）。

**私有**：私有的属性和方法只能在类的内部使用；<span style="color: red;">Python 中并没有真正的私有机制</span>。约定在私有属性名和方法名前加`__`（两个下划线），Python 会将`__var`偷偷改名为`_ClassName__var`

### 8.2 **继承**

**继承**：继承描述的是两个类之间的关系，子类继承父类，就可以获取到父类的属性和方法（非私有）。

```Python
# 定义父类
class ParentClass:
    def __init__(self, attr1, attr2):
        self.attr1 = attr1
        self.attr2 = attr2
    
    def method1(self):
        print("Parent method1")
    
    def method2(self):
        print("Parent method2")
# 定义子类
class ChildClass(ParentClass):
    def __init__(self, attr1, attr2, attr3):
        super().__init__(attr1, attr2)
        self.attr3 = attr3
    
    def method3(self):
        print("Child method3")
```

**重写**：重写是指子类继承父类后，如果父类中的方法不满足需求，可以在子类中重新定义父类中已有的方法（方法名相同），从而用子类的实现替换父类的实现。

> 在子类中调用父类方法：`父类名.方法名(self)` 或 `super().方法名()`

**多继承**：多继承指的是一个子类同时继承了多个父类的情况（会将多个父类中的非私有的属性和方法都继承下来）。

```Python
# 多继承
class 子类名(父类名1, 父类名2, 父类名3, ...)
```
> 注意：当一个类继承了多个父类时，默认优先使用第一个父类中的同名属性或方法，可以使用`类名.__mro__`属性或`类名.mro()`方法（方法解析顺序）查看调用顺序。

### 8.3 **多态**

**多态**：多态是指同一个方法，具有不同的形态、行为、表现。如：定义函数时，参数类型指定为父类类型，在执行的时候传入不同的子类对象，就具有不同的形态。

```Python
class Animal:
    def speak(self):
        pass  # 父类不实现具体逻辑

class Dog(Animal):
    def speak(self):
        return "汪汪"

class Cat(Animal):
    def speak(self):
        return "喵喵"

def animal_talk(animal: Animal):  # 传入任何Animal的子类
    print(animal.speak())

# 同一个函数，传入不同对象，表现不同
animal_talk(Dog())  # 输出：汪汪
animal_talk(Cat())  # 输出：喵喵
```

**鸭子类型**：鸭子类型是一种动态类型，它关注对象的属性和方法，而不关注对象的类型。

```Python
class Duck:
    def quack(self):
        print("呱呱呱")

class Person:
    def quack(self):
        print("我在模仿鸭子叫！")

def make_it_quack(thing):
    # 我不关心 thing 是 Duck 还是 Person，我只关心它能不能 quack()
    thing.quack()

d = Duck()
p = Person()
make_it_quack(d)  # 输出：呱呱呱
make_it_quack(p)  # 输出：我在模仿鸭子叫！（完全没问题，不会报错）
```

> Python 多态的特殊性：不需要继承。在 Java 中，多态必须建立在继承或接口实现的基础上。但在 Python 中，只要两个类有同名方法，它们就是“多态的”，哪怕毫无继承关系。

## 九、数据分析

### 9.1 **概述**

**数据分析**：从一堆看似杂乱的数据中，通过数据清洗、分析、可视化等手段，找出有价值的信息和结论，从而帮我们解决实际的问题（如:用户订单数据的分析、电影榜单数据分析、学校学生成绩分析等）

**基本流程**：

1. 数据收集：从各种渠道收集数据，如文件、数据库、网络等
2. 数据清洗：对数据进行清洗，如去除重复数据、处理缺失值、处理异常值、一致性检查等（基于 Pandas 库）
3. 数据分析：对数据进行分析，如统计分析、数据挖掘、机器学习等
4. 数据可视化：将数据可视化，如绘制图表、生成报告等（基于 Matplotlib 库）

**环境准备**：Jupyter Notebook

**Jupyter Notebook**：一个基于 Web 网页的、交互式的编程笔记本，可以把代码、运行结果、图表和笔记全部都放在一个文件里（在数据分析、机器学习、教学和科研等领域的数据实验室）

### 9.2 **Pandas**

**Pandas 介绍**：Pandas 是一个功能强大的结构化数据分析的工具集，底层是基于 Numpy 构建的，无论是在数据分析领域、还是大数据开发场景中都有显著的优势。

**Pandas 安装**：`pip install pandas` 或 `conda install pandas`

- [Pandas 官方文档](https://pandas.pydata.org/docs/user_guide/index.html#how-to-read-these-guides)

**核心类**： Series（一维数据），DataFrame（二维数据）

**DataFrame**：

```Python
import pandas as pd
df = pd.DataFrame({
    'A': [1, 2, 3],
    'B': [4, 5, 6]
})
print(df)
```

**Series**：

```Python
import pandas as pd
s = pd.Series([1, 3, 5, 7, 9])
print(s)
```

**数据读取和写入**：基于 Pandas 中提供的 API，可以很方便地读取和写入各类数据文件，如 CSV、Excel、数据库、网络数据等。

```Python
# 数据读取，以 csv 文件为例
import pandas as pd
df = pd.read_csv('data.csv')
# 数据写入
df.to_csv('data.csv', index=False)
```

**数据查看**：

- `df.head(n)`：查看前n行
- `df.tail(n)`：查看后n行
- `df.info()`：查看数据信息（列名、非空计数、数据类型、内存使用情况）
- `df.describe()`：查看数据统计信息（计数、均值、标准差、最小值、四分位数、最大值）
- `df.shape`：返回数据维度（行数、列数）
- `df.columns`：返回数据的列名

**数据选择**：

- `df['列名']`：选择单列
- `df[['列1','列2']]`：选择多列
- `df.loc[start:stop:step]`：选择行
- `df.iloc[start:stop:step]`：选择行，基于索引

**数据过滤**：

- `df[df['列名']条件]`：单条件过滤，常用条件：`>值`、`isin(['值1', '值2'])`、`between(值1, 值2)`
- `df[(条件1) & (条件2)]`：多条件过滤，与关系
- `df[(条件1) | (条件2)]`：多条件过滤，或关系

**数据清洗**：数据清洗是指发现并纠正数据中可识别的错误的过程，包括处理缺失值、重复值、异常值，统一数据格式，保证数据的一致性。

```Python
# 数据清洗
# 1. 缺失值处理
# 检查缺失值
df.isnull() # 检查缺失值，返回布尔值矩阵，True表示缺失值，False表示非缺失值
# 删除缺失值
df_new = df.dropna(axis=0, inplace=False)  # 删除包含缺失值的行，默认inplace=False表示返回一个新的表格
# 填充缺失值
df.fillna(0)  # 用0填充缺失值

# 2. 重复值处理
# 检查重复值
df.duplicated() # 检查重复值，返回布尔值矩阵，True表示重复值，False表示非重复值
df.duplicated(subset=['列1', '列2']) # 检查指定列的重复值
# 删除重复值
df.drop_duplicates(subset=['列1', '列2'], keep='first')  # 删除重复值，保留第一个

# 3. 异常值处理
# 检查异常值
df[(df['列名'] > 值1) & (df['列名'] < 值2)] # 检查异常值
# 删除异常值
df.drop(df[(df['列名'] > 值1) & (df['列名'] < 值2)].index)
# 修复异常值，根据具体需求灵活处理
df['列名'] = df['列名'].abs # 取绝对值

# 4. 数据格式统一
df['列名'].astype(数据类型) # 将列数据类型转换为指定数据类型
df['列名'].str.lower() # 将列数据转换为小写
df['列名'].str.upper() # 将列数据转换为大写
df['列名'].str.capitalize() # 将列数据转换为首字母大写
df['列名'].str.strip() # 将列数据两端的空格去掉
df['列名'].str.replace(旧值, 新值) # 将列数据中的旧值替换为新值
df['列名'].str.split(分隔符) # 将列数据按分隔符分割成列表
```

**数据排序**：

```Python
# 数据排序
df.sort_values(by='列名', ascending=False) # 按列名排序，ascending=False表示降序
# 多列排序
df.sort_values(by=['列1', '列2'], ascending=[True, False]) # 按多列排序，ascending=[True, False]表示按列1升序，列2降序
```

**数据分组**：

```Python
# 分组统计：根据‘分组列名’分组，计算每个类别 某个统计列名的统计值
df.groupby('分组列名').['统计列名'].sum() 
df.groupby('产品类别').agg({'销售量': 'sum', '销售额': 'sum'}) 
```

### 9.3 **Matplotlib**

**Matplotlib 介绍**：Matplotlib 是一个功能强大的数据可视化开源 Python 库，也是 Python 中使用的最多的图形绘图库，可以创建静态、动态、交互式的图表。

**Matplotlib 安装**：`pip install matplotlib` 或 `conda install matplotlib`

- [Matplotlib 官方文档](https://matplotlib.org/stable/contents.html)




## X、线程与进程

### x.1 **基本概念**

线程与进程是操作系统里面的术语，简单来讲，每一个应用程序都有一个自己的进程。

操作系统会为这些进程分配一些执行资源，例如内存空间等。

在进程中，又可以创建一些线程，他们共享这些内存空间，并由操作系统调用，以便并行计算。

我们都知道现代操作系统比如 Mac OS X，UNIX，Linux，Windows 等可以同时运行多个任务。

对于操作系统来说，**一个任务就是一个进程（Process）**，比如打开一个浏览器就是启动一个浏览器进程，打开 PyCharm 就是一个启动了一个 PtCharm 进程，打开 Markdown 就是启动了一个 Md 的进程。

虽然现在多核 CPU 已经非常普及了。

可是由于 CPU 执行代码都是顺序执行的，这时候我们就会有疑问，单核 CPU 是怎么执行多任务的呢？

其实就是操作系统轮流让各个任务交替执行，任务 1 执行 0.01 秒，切换到任务 2 ，任务 2 执行 0.01 秒，再切换到任务 3 ，执行 0.01秒……这样反复执行下去。

表面上看，每个任务都是交替执行的，但是，由于 CPU 的执行速度实在是太快了，我们肉眼和感觉上没法识别出来，就像所有任务都在同时执行一样。

真正的并行执行多任务只能在多核 CPU 上实现，但是，由于任务数量远远多于 CPU 的核心数量，所以，操作系统也会自动把很多任务轮流调度到每个核心上执行。

有些进程不仅仅只是干一件事的啊，比如浏览器，我们可以播放视频，播放音频，看文章，编辑文章等等，其实这些都是在浏览器进程中的子任务。在一个进程内部，要同时干多件事，就需要同时运行多个“子任务”，我们把**进程内的这些“子任务”称为线程（Thread）**。

由于每个进程至少要干一件事，所以，一个进程至少有一个线程。

当然，一个进程也可以有多个线程，多个线程可以同时执行，多线程的执行方式和多进程是一样的，也是由操作系统在多个线程之间快速切换，让每个线程都短暂地交替运行，看起来就像同时执行一样。

那么在 Python 中我们要同时执行多个任务怎么办？

多任务的实现有3种方式：

- 多进程模式；启动多个进程，每个进程虽然只有一个线程，但多个进程可以一块执行多个任务
- 多线程模式；启动一个进程，在一个进程内启动多个线程
- 多进程 + 多线程模式：启动多个进程，每个进程再启动多个线程，这样同时执行的任务就更多了，当然这种模型更复杂，实际很少采用。

同时执行多个任务通常各个任务之间并不是没有关联的，而是需要相互通信和协调，有时，任务 1 必须暂停等待任务 2 完成后才能继续执行，有时，任务 3 和任务 4 又不能同时执行，所以，多进程和多线程的程序的复杂度要远远高于我们前面写的单进程单线程的程序。

因为复杂度高，调试困难，所以，不是迫不得已，我们也不想编写多任务。

但是，有很多时候，没有多任务还真不行。

想想在电脑上看电影，就必须由一个线程播放视频，另一个线程播放音频，否则，单线程实现的话就只能先把视频播放完再播放音频，或者先把音频播放完再播放视频，这显然是不行的。

### x.2 **多线程编程**

**线程的状态**

* New 创建
* Runnable 就绪。等待调度
* Running 运行
* Blocked 阻塞。阻塞可能在 Wait Locked Sleeping
* Dead 消亡

**线程的类型**

* 主线程
* 子线程
* 守护线程（后台线程）
* 前台线程

**线程的创建**

Python 提供两个模块进行多线程的操作，分别是 `thread` 和 `threading`

`thread`是比较低级的模块，用于更底层的操作，一般应用级别的开发不常用。

因此，我们使用 `threading` 来举个例子：

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

import time
import threading


class MyThread(threading.Thread):
    def run(self):
        for i in range(5):
            print(f'thread {self.name}, @number: {i}')
            time.sleep(1)


def main():
    print("Start main threading")

    # 创建三个线程
    threads = [MyThread() for i in range(3)]
    # 启动三个线程
    for t in threads:
        t.start()

    print("End Main threading")


if __name__ == '__main__':
    main()
```

运行结果：

```
Start main threading
thread Thread-1, @number: 0
thread Thread-2, @number: 0
thread Thread-3, @number: 0
End Main threading
thread Thread-2, @number: 1
thread Thread-3, @number: 1
thread Thread-1, @number: 1
thread Thread-1, @number: 2
thread Thread-2, @number: 2
thread Thread-3, @number: 2
thread Thread-3, @number: 3
thread Thread-2, @number: 3
thread Thread-1, @number: 3
thread Thread-2, @number: 4
thread Thread-3, @number: 4
thread Thread-1, @number: 4
```

注意，这里不同的环境输出的结果肯定是不一样的。

**线程合并**

上面的示例打印出来的结果来看，主线程结束后，子线程还在运行。那么我们需要主线程要等待子线程运行完后，再退出，要怎么办呢？

这时候，就需要用到 `join` 方法了。

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

import time
import threading


class MyThread(threading.Thread):
    def run(self):
        for i in range(5):
            print(f'thread {self.name}, @number: {i}')
            time.sleep(1)


def main():
    print("Start main threading")

    # 创建三个线程
    threads = [MyThread() for i in range(3)]
    # 启动三个线程
    for t in threads:
        t.start()

    # 一次让新创建的线程执行 join
    for t in threads:
        t.join()

    print("End Main threading")


if __name__ == '__main__':
    main()
```

运行结果：

```
Start main threading
thread Thread-1, @number: 0
thread Thread-2, @number: 0
thread Thread-3, @number: 0
thread Thread-1, @number: 1
thread Thread-3, @number: 1
thread Thread-2, @number: 1
thread Thread-1, @number: 2
thread Thread-3, @number: 2
thread Thread-2, @number: 2
thread Thread-1, @number: 3
thread Thread-3, @number: 3
thread Thread-2, @number: 3
thread Thread-1, @number: 4
thread Thread-3, @number: 4
thread Thread-2, @number: 4
End Main threading
```

从打印的结果，可以清楚看到，相比上面示例打印出来的结果，主线程是在等待子线程运行结束后才结束的。

**线程同步与互斥锁**

使用线程加载获取数据，通常都会造成数据不同步的情况。当然，这时候我们可以给资源进行加锁，也就是访问资源的线程需要获得锁才能访问。

其中 `threading` 模块给我们提供了一个 `Lock` 功能。

```python
# 创建锁
lock = threading.Lock()
# 在线程中获取锁
lock.acquire()
# 释放锁
lock.release()
```

当然为了支持在同一线程中多次请求同一资源，Python 提供了可重入锁（RLock）。

RLock 内部维护着一个 Lock 和一个 counter 变量，counter 记录了 acquire 的次数，从而使得资源可以被多次 require。直到一个线程所有的 acquire 都被 release，其他的线程才能获得资源。

```python
# 创建可重入锁
r_lock = threading.RLock()
```

**Condition 条件变量**

实用锁可以达到线程同步，但是在更复杂的环境，需要针对锁进行一些条件判断。

Python 提供了 `Condition` 对象。

**使用 `Condition` 对象可以在某些事件触发或者达到特定的条件后才处理数据，`Condition` 除了具有 `Lock` 对象的 `acquire` 方法和 `release` 方法外，还提供了 `wait` 和 `notify` 方法。**

线程首先 acquire 一个条件变量锁。如果条件不足，则该线程 wait，如果满足就执行线程，甚至可以 notify 其他线程。其他处于 wait 状态的线程接到通知后会重新判断条件。

其中条件变量可以看成不同的线程先后 acquire 获得锁，如果不满足条件，可以理解为被扔到一个（ Lock 或 RLock ）的 waiting 池。直到其他线程 notify 之后再重新判断条件。不断的重复这一过程，从而解决复杂的同步问题。

该模式常用于生产者消费者模式，具体看看下面在线购物买家和卖家的示例：

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

import threading, time

class Consumer(threading.Thread):
    def __init__(self, cond, name):
        # 初始化
        super(Consumer, self).__init__()
        self.cond = cond
        self.name = name

    def run(self):
        # 确保先运行Seeker中的方法
        time.sleep(1)
        self.cond.acquire()
        print(self.name + ': 我这两件商品一起买，可以便宜点吗')
        self.cond.notify()
        self.cond.wait()
        print(self.name + ': 我已经提交订单了，你修改下价格')
        self.cond.notify()
        self.cond.wait()
        print(self.name + ': 收到，我支付成功了')
        self.cond.notify()
        self.cond.release()
        print(self.name + ': 等待收货')


class Producer(threading.Thread):
    def __init__(self, cond, name):
        super(Producer, self).__init__()
        self.cond = cond
        self.name = name

    def run(self):
        self.cond.acquire()
        # 释放对琐的占用，同时线程挂起在这里，直到被 notify 并重新占有琐。
        self.cond.wait()
        print(self.name + ': 可以的，你提交订单吧')
        self.cond.notify()
        self.cond.wait()
        print(self.name + ': 好了，已经修改了')
        self.cond.notify()
        self.cond.wait()
        print(self.name + ': 嗯，收款成功，马上给你发货')
        self.cond.release()
        print(self.name + ': 发货商品')


cond = threading.Condition()
consumer = Consumer(cond, '买家（两点水）')
producer = Producer(cond, '卖家（三点水）')
consumer.start()
producer.start()
```

输出的结果如下：

```
买家（两点水）: 我这两件商品一起买，可以便宜点吗
卖家（三点水）: 可以的，你提交订单吧
买家（两点水）: 我已经提交订单了，你修改下价格
卖家（三点水）: 好了，已经修改了
买家（两点水）: 收到，我支付成功了
买家（两点水）: 等待收货
卖家（三点水）: 嗯，收款成功，马上给你发货
卖家（三点水）: 发货商品
```

**线程间通信**

如果程序中有多个线程，这些线程避免不了需要相互通信的。那么我们怎样在这些线程之间安全地交换信息或数据呢？

从一个线程向另一个线程发送数据最安全的方式可能就是使用 queue 库中的队列了。创建一个被多个线程共享的 `Queue` 对象，这些线程通过使用 `put()` 和 `get()` 操作来向队列中添加或者删除元素。

```python
# -*- coding: UTF-8 -*-
from queue import Queue
from threading import Thread


def write(q):
    # 写数据进程
    for value in ['两点水', '三点水', '四点水']:
        print(f'写进 Queue 的值为：{value}')
        q.put(value)
    q.put(None)  # 写完放一个结束标记


def read(q):
    # 读取数据进程
    while True:
        value = q.get(True)
        if value is None:  # 读到结束标记就退出
            break
        print(f'从 Queue 读取的值为：{value}')


if __name__ == '__main__':
    q = Queue()
    t1 = Thread(target=write, args=(q,))
    t2 = Thread(target=read, args=(q,))
    t1.start()
    t2.start()
```

输出的结果如下：

```
写进 Queue 的值为：两点水
写进 Queue 的值为：三点水
从 Queue 读取的值为：两点水
写进 Queue 的值为：四点水
从 Queue 读取的值为：三点水
从 Queue 读取的值为：四点水
```

Python 还提供了 Event 对象用于线程间通信，它是由线程设置的信号标志，如果信号标志位真，则其他线程等待直到信号接触。

Event 对象实现了简单的线程通信机制，它提供了设置信号，清除信号，等待等用于实现线程间的通信。

- 设置信号：使用 Event 的 `set()` 方法可以设置 Event 对象内部的信号标志为真。Event 对象提供了 `is_set()` 方法来判断其内部信号标志的状态。当使用 event 对象的 `set()` 方法后，`is_set()` 方法返回真
- 清除信号：使用 Event 对象的 `clear()` 方法可以清除 Event 对象内部的信号标志，即将其设为假，当使用 Event 的 `clear()` 方法后，`is_set()` 方法返回假
- 等待：Event 对象 wait 的方法只有在内部信号为真的时候才会很快的执行并完成返回。当 Event 对象的内部信号标志位假时，则 wait 方法一直等待到其为真时才返回。

示例：

```python
# -*- coding: UTF-8 -*-

import threading


class mThread(threading.Thread):
    def __init__(self, threadname):
        threading.Thread.__init__(self, name=threadname)

    def run(self):
        # 使用全局Event对象
        global event
        # 判断Event对象内部信号标志
        if event.is_set():
            event.clear()
            # 加 timeout 兜底：is_set() 判断与 clear() 之间不是原子操作，
            # 两个线程可能都通过判断、都进 wait()，而只有一个 set() 来唤醒
            event.wait(timeout=1)
            print(self.name)
        else:
            print(self.name)
            # 设置Event对象内部信号标志
            event.set()

# 生成Event对象
event = threading.Event()
# 设置Event对象内部信号标志
event.set()
t1 = []
for i in range(10):
    t = mThread(str(i))
    # 生成线程列表
    t1.append(t)

for i in t1:
    # 运行线程
    i.start()
```

输出的结果如下：

```
1
0
3
2
5
4
7
6
9
8
```

**后台线程**

默认情况下，主线程退出之后，即使子线程没有 `join`。那么主线程结束后，子线程也依然会继续执行。

如果希望主线程退出后，其子线程也退出而不再执行，则需要设置子线程为后台线程。Thread 对象提供了 `daemon` 属性。

- daemon=True：守护线程。主线程结束时，它会被强制杀掉，不会阻止程序退出。
- daemon=False（默认）：非守护线程。主线程要等它跑完才退出。

```python
import threading, time

def worker():
    while True:
        print("running...")
        time.sleep(1)

t = threading.Thread(target=worker, name="w")
t.daemon = True      # 主线程退出时，这个线程自动结束
t.start()

time.sleep(3)
print("main done")   # 主线程结束后，程序直接退出，不会卡在 worker
```

总结：

- 默认（非守护线程）下，主线程退出后，子线程依然会继续执行，直到所有非守护线程都结束，进程才退出。
- `join()` 是阻塞主线程，先等子线程结束再继续主线程。可加超时 `join(timeout)`，超时后主线程继续。
- `daemon=True` 是截断子线程（守护线程）。当主线程结束后，所有非守护线程都结束，触发整个进程退出，导致守护线程被强制终止。

一句话记忆：

* 非守护：主线程可以先走，我留下收尾，进程等我。
* join：主线程你别走，我等你。
* daemon：进程一走，我陪葬。

### x.3 **进程**

Python 中的多线程其实并不是真正的多线程，如果想要充分地使用多核 CPU 的资源，在 Python 中大部分情况需要使用多进程。

Python 提供了非常好用的多进程包 `multiprocessing`，只需要定义一个函数，Python 会完成其他所有事情。

借助这个包，可以轻松完成从单进程到并发执行的转换。`multiprocessing` 支持子进程、通信和共享数据、执行不同形式的同步，提供了 `Process`、`Queue`、`Pipe`、`Lock` 等组件。

**创建进程类 Process**

下面看一个创建函数并将其作为多个进程的例子：

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

import multiprocessing
import time


def worker(interval, name):
    print(name + '【start】')
    time.sleep(interval)
    print(name + '【end】')


if __name__ == "__main__":
    p1 = multiprocessing.Process(target=worker, args=(2, '两点水1'))
    p2 = multiprocessing.Process(target=worker, args=(3, '两点水2'))
    p3 = multiprocessing.Process(target=worker, args=(4, '两点水3'))

    p1.start()
    p2.start()
    p3.start()

    print("The number of CPU is:" + str(multiprocessing.cpu_count()))
    for p in multiprocessing.active_children():
        print("child   p.name:" + p.name + "\tp.id" + str(p.pid))
    print("END!!!!!!!!!!!!!!!!!")
```

输出的结果：

```
两点水1【start】
两点水2【start】
The number of CPU is:24
child   p.name:Process-2	p.id15480
child   p.name:Process-3	p.id35916
child   p.name:Process-1	p.id38924
END!!!!!!!!!!!!!!!!!
两点水3【start】
两点水1【end】
两点水2【end】
两点水3【end】
```

**自定义进程类（继承 Process）**

当然我们也可以自定义进程类，如下面的例子，当进程 p 调用 `start()` 时，自动调用 `run()` 方法。

```python
# -*- coding: UTF-8 -*-

import multiprocessing
import time


class ClockProcess(multiprocessing.Process):
    def __init__(self, interval):
        multiprocessing.Process.__init__(self)
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print(f"当前时间: {time.ctime()}")
            time.sleep(self.interval)
            n -= 1


if __name__ == '__main__':
    p = ClockProcess(3)
    p.start()
```

输出结果如下：

```
当前时间: Thu Sep 10 17:05:02 2026
当前时间: Thu Sep 10 17:05:05 2026
当前时间: Thu Sep 10 17:05:08 2026
当前时间: Thu Sep 10 17:05:11 2026
当前时间: Thu Sep 10 17:05:14 2026
```

**daemon 属性**

如果在子进程中添加了 `daemon = True`，那么当主进程结束的时候，子进程也会跟着结束。所以没有打印子进程的信息。

```python
# -*- coding: UTF-8 -*-

import multiprocessing
import time


def worker(interval):
    print(f'工作开始时间：{time.ctime()}')
    time.sleep(interval)
    print(f'工作结果时间：{time.ctime()}')


if __name__ == '__main__':
    p = multiprocessing.Process(target=worker, args=(3,))
    p.daemon = True
    p.start()
    print('【EMD】')
```

输出结果：

```
【EMD】
```

**join 方法**

join 方法的主要作用是：阻塞当前进程，直到调用 join 方法的那个进程执行完，再继续执行当前进程。

```python
import multiprocessing
import time


def worker(interval):
    print(f'工作开始时间：{time.ctime()}')
    time.sleep(interval)
    print(f'工作结果时间：{time.ctime()}')


if __name__ == '__main__':
    p = multiprocessing.Process(target=worker, args=(3,))
    p.daemon = True
    p.start()
    p.join()
    print('【EMD】')
```

输出的结果：

```
工作开始时间：Thu Sep 10 17:08:27 2026
工作结果时间：Thu Sep 10 17:08:30 2026
【EMD】
```

**Pool 进程池**

如果需要很多的子进程，难道我们需要一个一个的去创建吗？

当然不用，我们可以使用进程池的方法批量创建子进程。

```python
# -*- coding: UTF-8 -*-

from multiprocessing import Pool
import os, time, random


def long_time_task(name):
    print(f'进程的名称：{name} ；进程的PID: {os.getpid()} ')
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print(f'进程 {name} 运行了 {end - start} 秒')


if __name__ == '__main__':
    print(f'主进程的 PID：{os.getpid()}')
    p = Pool(4)
    for i in range(6):
        p.apply_async(long_time_task, args=(i,))
    p.close()
    # 等待所有子进程结束后在关闭主进程
    p.join()
    print('【End】')
```

输出的结果如下：

```
主进程的 PID：41680
进程的名称：0 ；进程的PID: 38604 
进程的名称：1 ；进程的PID: 38480 
进程的名称：2 ；进程的PID: 8396 
进程的名称：3 ；进程的PID: 37300 
进程 3 运行了 0.45252037048339844 秒
进程的名称：4 ；进程的PID: 37300 
进程 1 运行了 1.4969210624694824 秒
进程的名称：5 ；进程的PID: 38480 
进程 0 运行了 1.7344286441802979 秒
进程 2 运行了 2.1402225494384766 秒
进程 5 运行了 0.9203391075134277 秒
进程 4 运行了 1.9575226306915283 秒
【End】
```

`Pool` 对象调用 `join()` 方法会等待所有子进程执行完毕.

调用 `join()` 之前必须先调用 `close()` ，调用 `close()` 之后就不能继续添加新的 Process 了。

请注意输出的结果，子进程 0，1，2，3是立刻执行的，而子进程 4 要等待前面某个子进程完成后才执行，这是因为 Pool 的默认大小在我的电脑上是 4，因此，最多同时执行 4 个进程。这是 Pool 有意设计的限制，并不是操作系统的限制。如果改成：

```python
p = Pool(5)
```

就可以同时跑 5 个进程。

建议 Pool 大小：

```python
import os
import multiprocessing
pool_size = os.cpu_count()
p = multiprocessing.Pool(pool_size)
```

**进程间通信**

Process 之间肯定是需要通信的，操作系统提供了很多机制来实现进程间的通信。

Python 的 multiprocessing 模块包装了底层的机制，提供了 Queue、Pipes 等多种方式来交换数据。

以 Queue 为例，在父进程中创建两个子进程，一个往 Queue 里写数据，一个从 Queue 里读数据：

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

from multiprocessing import Process, Queue
import os, time, random


def write(q):
    # 写数据进程
    print(f'写进程的PID:{os.getpid()}')
    for value in ['两点水', '三点水', '四点水']:
        print(f'写进 Queue 的值为：{value}')
        q.put(value)
        time.sleep(random.random())


def read(q):
    # 读取数据进程
    print(f'读进程的PID:{os.getpid()}')
    while True:
        value = q.get(True)
        print(f'从 Queue 读取的值为：{value}')


if __name__ == '__main__':
    # 父进程创建 Queue，并传给各个子进程
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程 pw
    pw.start()
    # 启动子进程pr
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr 进程里是死循环，无法等待其结束，只能强行终止
    pr.terminate()
```

输出的结果为：

```
写进程的PID:3220
写进 Queue 的值为：两点水
读进程的PID:39640
从 Queue 读取的值为：两点水
写进 Queue 的值为：三点水
从 Queue 读取的值为：三点水
写进 Queue 的值为：四点水
从 Queue 读取的值为：四点水
```


## X、正则表达式

### x.1 **初识**

正则表达式是一个特殊的字符序列，用于判断一个字符串是否与我们所设定的字符序列是否匹配，也就是说检查一个字符串是否与某种模式匹配。

Python 自 1.5 版本起增加了 `re` 模块，它提供 Perl 风格的正则表达式模式。`re` 模块使 Python 语言拥有全部的正则表达式功能。

下面通过实例，一步一步来初步认识正则表达式。

比如在一段字符串中寻找是否含有某个字符或某些字符，通常我们使用内置函数来实现，如下：

```python
# 设定一个常量
a = '两点水|twowater|liangdianshui|草根程序员|ReadingWithU'

# 判断是否有 “两点水” 这个字符串，使用 PY 自带函数

print(f'是否含有“两点水”这个字符串：{a.index("两点水") > -1}')
print(f'是否含有“两点水”这个字符串：{"两点水" in a}')
```

输出的结果如下：

```
是否含有“两点水”这个字符串：True
是否含有“两点水”这个字符串：True
```

那么，如果使用正则表达式呢？

刚刚提到过，Python 给我们提供了 `re` 模块来实现正则表达式的所有功能，那么我们先使用其中的一个函数：

```python
re.findall(pattern, string[, flags])
```

该函数实现了在字符串中找到正则表达式所匹配的所有子串，并组成一个列表返回,具体操作如下：

```python
import re

# 设定一个常量
a = '两点水|twowater|liangdianshui|草根程序员|ReadingWithU'

# 正则表达式

findall = re.findall('两点水', a)
print(findall)

if len(findall) > 0:
    print('a 含有“两点水”这个字符串')
else:
    print('a 不含有“两点水”这个字符串')
```

输出的结果：

```
['两点水']
a 含有“两点水”这个字符串
```

从输出结果可以看到，可以实现和内置函数一样的功能。

可是在这里也要强调一点，上面这个例子只是方便我们理解正则表达式，这个正则表达式的写法是毫无意义的。

因为用 Python 自带函数就能解决的问题，我们就没必要使用正则表达式了，这样做多此一举。

而且上面例子中的正则表达式设置成为了一个常量，并不是一个正则表达式的规则，正则表达式的灵魂在于规则，所以这样做意义不大。

那么正则表达式的规则怎么写呢？先不急，我们一步一步来，先来一个简单的，**找出字符串中的所有小写字母**。

首先我们**在 `findall` 函数中第一个参数写正则表达式的规则**，其中 `[a-z]` 就是匹配任何小写字母，第二个参数只要填写要匹配的字符串就行了。具体如下：

```python
import re

# 设定一个常量
a = '两点水|twowater|liangdianshui|草根程序员|ReadingWithU'

# 选择 a 里面的所有小写英文字母

re_findall = re.findall('[a-z]', a)

print(re_findall)
```

输出的结果：

```
['t', 'w', 'o', 'w', 'a', 't', 'e', 'r', 'l', 'i', 'a', 'n', 'g', 'd', 'i', 'a', 'n', 's', 'h', 'u', 'i', 'e', 'a', 'd', 'i', 'n', 'g', 'i', 't', 'h']
```

这样我们就拿到了字符串中的所有小写字母了。

### x.2 **字符集**

我们初步认识了 Python 的正则表达式，可能你就会问，正则表达式还有什么规则，什么字母代表什么意思呢？

字符集是由一对方括号 “[]” 括起来的字符集合。使用字符集，可以匹配多个字符中的一个，即字符关系是“**或（OR）**”关系。

比如：

- `C[ET]O` 匹配到的是 `CEO` 或 `CTO` ，即 `[ET]` 代表的是一个 `E` 或者一个 `T` 。
- `[a-z]` 匹配所有小写字母中的其中一个，这里使用了连字符 `-` 定义一个连续字符的字符范围。
- `[0-9a-fA-F]` 匹配单个的十六进制数字，且不分大小写

下面看一个例子：

```python
import re
a = 'uav,ubv,ucv,uwv,uzv,ucv,uov'

# 字符集

# 取 u 和 v 中间是 a 或 b 或 c 的字符
findall = re.findall('u[abc]v', a)
print(findall)
# 如果是连续的字母，数字可以使用 - 来代替
l = re.findall('u[a-c]v', a)
print(l)

# 取 u 和 v 中间不是 a 或 b 或 c 的字符
re_findall = re.findall('u[^abc]v', a)
print(re_findall)
```

输出结果：

```
['uav', 'ubv', 'ucv', 'ucv']
['uav', 'ubv', 'ucv', 'ucv']
['uwv', 'uzv', 'uov']
```

正则表达式本身就定义了一些规则，比如 `\d` 匹配所有数字字符,其实它是等价于 `[0-9]`，下面也写了个例子，通过字符集的形式解释了这些特殊字符

```python
import re

a = 'uav_ubv_ucv_uwv_uzv_ucv_uov&123-456-789'

# 概括字符集

# \d 相当于 [0-9] ,匹配所有数字字符
# \D 相当于 [^0-9] ， 匹配所有非数字字符
findall1 = re.findall('\d', a)
findall2 = re.findall('[0-9]', a)
findall3 = re.findall('\D', a)
findall4 = re.findall('[^0-9]', a)
print(findall1)
print(findall2)
print(findall3)
print(findall4)

# \w 匹配包括下划线的任何单词字符，等价于 [A-Za-z0-9_]
findall5 = re.findall('\w', a)
findall6 = re.findall('[A-Za-z0-9_]', a)
print(findall5)
print(findall6)
```

输出结果：

```
['1', '2', '3', '4', '5', '6', '7', '8', '9']
['1', '2', '3', '4', '5', '6', '7', '8', '9']
['u', 'a', 'v', '_', 'u', 'b', 'v', '_', 'u', 'c', 'v', '_', 'u', 'w', 'v', '_', 'u', 'z', 'v', '_', 'u', 'c', 'v', '_', 'u', 'o', 'v', '&', '-', '-']
['u', 'a', 'v', '_', 'u', 'b', 'v', '_', 'u', 'c', 'v', '_', 'u', 'w', 'v', '_', 'u', 'z', 'v', '_', 'u', 'c', 'v', '_', 'u', 'o', 'v', '&', '-', '-']
['u', 'a', 'v', '_', 'u', 'b', 'v', '_', 'u', 'c', 'v', '_', 'u', 'w', 'v', '_', 'u', 'z', 'v', '_', 'u', 'c', 'v', '_', 'u', 'o', 'v', '1', '2', '3', '4', '5', '6', '7', '8', '9']
['u', 'a', 'v', '_', 'u', 'b', 'v', '_', 'u', 'c', 'v', '_', 'u', 'w', 'v', '_', 'u', 'z', 'v', '_', 'u', 'c', 'v', '_', 'u', 'o', 'v', '1', '2', '3', '4', '5', '6', '7', '8', '9']
```

### x.3 **数量词**

**为什么要用数量词**：当要匹配几十上百长度的字符时，一个一个的写太麻烦，所以就出现了数量词。

**数量词的词法**：`{min, max}` 。min 和 max 都是非负整数。如果逗号有而 max 被忽略了，则 max 没有限制。如果逗号和 max 都被忽略了，则重复 min 次。

比如，`\b[1-9][0-9]{3}\b` 匹配的是 1000 ~ 9999 之间的数字( `\b` 表示单词边界），

而 `\b[1-9][0-9]{2,4}\b`，匹配的是一个在 100 ~ 99999 之间的数字。

下面看一个实例，匹配出字符串中 4 到 7 个字母的英文

```python
import re

a = 'java*&39android##@@python'

# 数量词

findall = re.findall('[a-z]{4,7}', a)
print(findall)
```

输出结果：

```
['java', 'android', 'python']
```

注意，上面例子是贪婪模式，它的宗旨是读尽可能多的字符

如果要使用懒惰模式，则加一个 `?` ，上面的例子修改如下：

```python
import re

a = 'java*&39android##@@python'

# 非贪婪
re_findall = re.findall('[a-z]{4,7}?', a)
print(re_findall)
```

输出结果：

```
['java', 'andr', 'pyth']
```

除了`{min, max}`，还有一些特殊字符也是可以表示数量的，比如：

* `?`：告诉引擎匹配前导字符 0 次或 1 次
* `+`：告诉引擎匹配前导字符 1 次或多次
* `*`：告诉引擎匹配前导字符 0 次或多次

把这部分的知识点总结一下,就是下面这个表了:

| 贪   婪 | 惰   性 | 描   述           |
| ------- | ------- |-----------------|
| `？`      | `？？`    | 零次或一次出现，等价于`{0,1}` |
| `+`       | `+？`     | 一次或多次出现，等价于`{1,}` |
| `*`       | `*？`     | 零次或多次出现，等价于`{0,}` |
| `{n}`     | `{n}？`   | 恰好 n 次出现        |
| `{n,m}`   | `{n,m}？` | 至少 n 次，至多 m 次出现 |
| `{n,}`    | `{n,}？`  | 至少 n 次出现        |

### x.4 **边界匹配符和组**

边界匹配符用来匹配位置，而不是字符本身。它们不消耗字符，只断言某个位置满足条件。

一般的边界匹配符有以下几个：

| 语法 | 描述                       |
| ---- |--------------------------|
| `^`    | 匹配字符串开头（在有多行的情况中匹配每行的开头） |
| `$`    | 匹配字符串结尾（在有多行的情况中匹配每行的末尾） |
| `\A`   | 仅匹配字符串开头                 |
| `\Z`   | 仅匹配字符串末尾                 |
| `\b`   | 匹配单词边界 `\w` 和 `\W` 之间    |
| `\B`   | 匹配非单词边界`[^\b]`           |

组用圆括号 `()` 把一部分模式括起来，实现**捕获、复用、分支**等功能。

分组表达式 `(...)` 其实就是把这部分字符作为一个整体，当然，可以有多分组的情况，每遇到一个分组，编号就会加 1 ，而且分组后面也是可以加数量词的。

### x.5 **re.sub**

`re.sub` 是 Python `re` 模块里用来**替换字符串**的函数，全称是 substitute（替换）。

**基本语法**：`re.sub(pattern, repl, string, count=0, flags=0)`

| 参数 | 含义 |
|------|------|
| `pattern` | 正则表达式模式 |
| `repl` | 替换成什么（字符串，或一个函数） |
| `string` | 被处理的原始字符串 |
| `count` | 最多替换几次，默认 `0` 表示全部替换 |
| `flags` | 匹配标志，如 `re.I`（忽略大小写）、`re.M`（多行）等 |

第二个参数是可以传递一个函数的，这也是这个方法的强大之处，例如：

```python
#!/usr/bin/env python3
# -*- coding: UTF-8 -*-

import re

a = 'Python*Android*Java-888'

# 把字符串中的 * 字符替换成 & 字符
sub1 = re.sub('\*', '&', a)
print(sub1)

# 把字符串中的第一个 * 字符替换成 & 字符
sub2 = re.sub('\*', '&', a, 1)
print(sub2)


# 把字符串中的 * 字符替换成 & 字符,把字符 - 换成 |

# 1、先定义一个函数
def convert(value):
    group = value.group()
    if (group == '*'):
        return '&'
    elif (group == '-'):
        return '|'


# 第二个参数，要替换的字符可以为一个函数
sub3 = re.sub('[\*-]', convert, a)
print(sub3)
```

输出的结果：

```
Python&Android&Java-888
Python&Android*Java-888
Python&Android&Java|888
```

### x.6 **re.match 和 re.search**

**re.match 函数**

`re.match` 是 Python `re` 模块里用来从字符串开头匹配的函数。

**基本语法**：`re.match(pattern, string, flags=0)`

| 参数 | 含义 |
|------|------|
| `pattern` | 正则表达式模式 |
| `string` | 被匹配的原始字符串 |
| `flags` | 匹配标志，如 `re.I`（忽略大小写）、`re.M`（多行）等 |

`re.match` 尝试从字符串的起始位置匹配一个模式，匹配成功则返回 `Match` 对象，匹配失败则返回 `None`。

**re.search 函数**

`re.search` 是 Python `re` 模块里用来在字符串中搜索第一个匹配的函数。

**基本语法**：`re.search(pattern, string, flags=0)`

`re.search` 从左到右扫描整个字符串，找到第一个匹配正则表达式的位置，返回 `Match` 对象；找不到返回 `None`。

```python
import re

text = "xabc"

print(re.match(r'abc', text))    # None
print(re.search(r'abc', text))   # <re.Match ... match='abc'>
```

`re.search` 配合捕获组使用：

```python
import re

m = re.search(r'(\d{4})-(\d{2})-(\d{2})', "日期是 2024-01-15 哦")
print(m.group())    # 2024-01-15
print(m.group(1))   # 2024
print(m.group(2))   # 01
print(m.group(3))   # 15
print(m.groups())   # ('2024', '01', '15')
```

最后，正则表达式是非常厉害的工具，通常可以用来解决字符串内置函数无法解决的问题，而且正则表达式大部分语言都是有的。

特别是在 Python 的爬虫和数据分析应用中离不开正则表达式。


## X、闭包

### x.1 **闭包的定义**

闭包（Closure）是一个函数，它记住了自己被定义时所在作用域中的变量，即使那个作用域已经执行结束，这些变量依然可以被访问。

一个函数满足以下条件就是闭包：

* **嵌套函数**：定义在另一个函数内部；
* **引用外部变量**：内部函数引用了外层函数的变量；
* **外层函数返回内层函数**：把内部函数作为返回值返回。

```python
def outer(x):              # 外层函数
    def inner(y):          # 内层函数
        return x + y       # 引用外层变量 x
    return inner           # 返回内层函数

add5 = outer(5)            # x = 5 被"记住"
print(add5(3))             # 8
print(add5(10))            # 15
```

`add5` 就是一个闭包，它记住了 `x = 5`

**闭包的经典例子**

1. **装饰器**：最广泛的应用
2. **工厂函数（如生成乘法器）**：根据参数生成不同行为的函数
3. **保存状态**：不依赖全局变量或类，保存局部状态
4. **回调函数**：记住上下文吗
5. **函数式编程**：配合 `map`、`filter`、`sorted` 等

### x.2 **nonlocal 关键字**

内层函数修改外层变量时，需要 `nonlocal` 关键字

```python
def outer():
    x = 0

    def inner():
        nonlocal x    # 不加会报 UnboundLocalError
        x += 1
        return x

    return inner

f = outer()
print(f())   # 1
print(f())   # 2
```

再看一个例子：

```python
time = 0


def study_time(time):
    def insert_time(min):
        nonlocal  time
        time = time + min
        return time

    return insert_time


f = study_time(time) # 将全局变量 time 的值 0 赋给外层函数，即外层函数的 time 初值为 0
# 如果函数是闭包的话，__closure__ 返回一个由 cell 组成的元组对象
print(f.__closure__) # (<cell at 0x0000000000410C48: int object at 0x000000001D6AB420>,)
print(f(2)) # 2，外层函数的 time 增加 2
print(time) # 0，全局变量 time 没有变化
print(f.__closure__[0].cell_contents) # 2
print(f(10)) # 12，外层函数的 time 再增加 10
print(time) # 0
print(f.__closure__[0].cell_contents) # 12
```

从打印结果可见，传进来的值一直存储在闭包的 `cell_contents` 中,因此，这也就是闭包的最大特点，可以将父函数的变量与其内部定义的函数绑定。就算生成闭包的父函数已经释放了，闭包仍然存在。

闭包的过程其实好比类（父函数）生成实例（闭包），不同的是父函数只在调用时执行，执行完毕后其环境就会释放，而类则在文件执行时创建，一般程序执行完毕后作用域才释放，因此对一些需要重用的功能且不足以定义为类的行为，使用闭包会比使用类占用更少的资源，且更轻巧灵活。

* 状态简单、逻辑少 → 闭包更轻量
* 状态多、方法多 → 用类更清晰



## X、装饰器

### x.1 **函数式编程**

**函数式编程**：把函数当值来传递和组合的编程范式

```python
import time


def punch():
    print('昵称：两点水  部门：做鸭事业部 上班打卡成功')


def add_time(func):
    print(time.strftime('%Y-%m-%d', time.localtime(time.time())))
    func()


def holiday():
    print('天气太冷，今天放假')


add_time(punch)
add_time(holiday)
```

输出结果：

```
2026-09-11
昵称：两点水  部门：做鸭事业部 上班打卡成功
2026-09-11
天气太冷，今天放假
```

这样子就没有改动 `punch` 方法，而且任何需要用到打印当前日期的函数都可以把函数传进 `add_time` 就可以了。

### x.2 **装饰器**

装饰器结合了**函数式编程的思想**与**闭包**

```python
def logger(func):              # 接收一个函数
    def wrapper(*args, **kwargs):   # 内层函数
        print(f"调用 {func.__name__}")
        return func(*args, **kwargs)  # 调用原函数
    return wrapper             # 返回新函数

@logger
def add(a, b):
    return a + b

@logger
def sub(a, b):
    return a - b

print(add(1, 2))
# 调用 add
# 3
```

@`logger` 就是装饰器语法糖，等价于 `add = logger(add)`

执行流程：

1. `@logger` 把 `add` 传给 `logger`
2. `logger` 返回 `wrapper`
3. `add` 这个名字现在指向 `wrapper`
4. 以后调用 `add(1, 2)`，其实调用的是 `wrapper(1, 2)`
5. `wrapper` 里先打印日志，再调用真正的 `add`


## X、Pydantic 数据校验库

Pydantic 是 Python 生态里最火的「数据校验」库，FastAPI 的核心、LangChain 的接口、各种 SDK 的配置类，背后都是它。

它的核心思想很朴素：**数据进入边界时，按声明的 schema 严格校验、必要时强制转换；之后程序内部代码就能放心用了**。

Pydantic 是**第三方库**，不在标准库里，需要安装：

```bash
pip install pydantic
```

### x.1 **第一个 BaseModel**

```python
from pydantic import BaseModel


class Employee(BaseModel):
    name: str
    age: int
    salary: float


e = Employee(name='两点水', age=28, salary=12000.0)
print(e)
```

输出：

```
name='两点水' age=28 salary=12000.0
```

各位是不是觉得这写法跟 dataclass 几乎一样？没错——继承 `BaseModel`，写带类型注解的字段，剩下的 Pydantic 全包了。

它会自动生成 `__init__`、`__repr__`、`__eq__`，还会做一件 dataclass 不做的事——**类型校验和强制转换**。

```python
from pydantic import BaseModel


class Employee(BaseModel):
    name: str
    age: int
    salary: float


# 注意：age 我传的是字符串 '28'，salary 传的是字符串 '12000.5'
e = Employee(name='两点水', age='28', salary='12000.5')
print(e)
print(type(e.age), type(e.salary))
```

输出：

```
name='两点水' age=28 salary=12000.5
<class 'int'> <class 'float'>
```

看到了吗？传的 `age='28'` 是字符串，但 Pydantic 自动转成了 int。

`salary='12000.5'` 也被转成了 float。这就是 Pydantic 的「智能转换」——只要类型能合理转换，它就帮你转。

如果传的是真的转不过去，Pydantic 不仅会抛 ValidationError，还说明哪些字段、为什么错、收到的是啥

## X、异步编程

### x.1 **async/await**

`asyncio` 是 Python 标准库中用于编写并发代码的模块，基于 `async/await` 语法，核心是事件循环。自 Python 3.4 引入，3.7 后 API 趋于稳定。

它的卖点很直白——**一个线程，同时等一百件事**。

**串行 vs 并发**：

下面这段是「正经的」串行代码，三件事各等 1 秒：

```python
import time


def task(name):
    time.sleep(1)
    return f'{name} 完成'


start = time.perf_counter()
r1 = task('打卡')
r2 = task('查询')
r3 = task('上传')
cost = time.perf_counter() - start
print(r1, r2, r3)
print(f'总共 {cost:.2f} 秒')
```
运行结果:

```
打卡 完成 查询 完成 上传 完成
总共 3.00 秒
```

接下来换成异步版本：

```python
import asyncio
import time


async def task(name):
    await asyncio.sleep(1)
    return f'{name} 完成'


async def main():
    start = time.perf_counter()
    r1, r2, r3 = await asyncio.gather(
        task('打卡'),
        task('查询'),
        task('上传'),
    )
    cost = time.perf_counter() - start
    print(r1, r2, r3)
    print(f'总共 {cost:.2f} 秒')


asyncio.run(main())
```

运行结果：

```
打卡 完成 查询 完成 上传 完成
总共 1.00 秒
```

3 秒变 1 秒，三件事居然真的「同时」完成了。先别管 `async` 、 `await` 、 `gather` 这些词是什么意思。

先记住一个事实：**`asyncio.sleep(1)` 在一个协程里等的时候，另一个协程可以去用 CPU 干自己的活**，三个协程的等待是重叠的，所以总时间就是最长那一个，而不是三者之和。

这就是 `asyncio` 的核心魔法。它没有偷偷开线程，也没有把 CPU 加速。它做的事很朴素：**当某个协程在等 IO 的时候，让出 CPU 给其他协程，等回来再继续**。

**async：定义协程函数**

普通函数加一个 `async` 关键字，就成了**协程函数（coroutine function）**：

```python
async def hello():
    return '两点水好'

print(hello())
```

输出：

```
<coroutine object hello at 0x...>
RuntimeWarning: coroutine 'hello' was never awaited
```

同样是 `print(hello())` ，普通函数返回的是字符串 '两点水好' ，协程函数返回的却是一个 **协程对象**——一个 <coroutine object> ，而不是字符串

更要命的是，Python 还甩了一句警告：这协程从来没被 `await` 过（coroutine was never awaited）。

这说明：

- `async def` 定义的不是一个会立刻跑的函数
- 调用 `async def` 定义的函数，**它根本没跑**，只是给你返回一个待执行的任务清单

这张**任务清单就是协程对象**。它必须被 `await` ，或者扔给事件循环去执行，里面的代码才会真正跑起来。

**await：等待协程对象完成**

那怎么让这张任务单真的执行？用 `await`：

```python
import asyncio


async def hello():
    return '两点水好'


async def main():
    result = await hello()
    print(result)


asyncio.run(main())
```

输出：

```
两点水好
```

这下拿到字符串了。`await hello()` 可以理解为：请帮我把 `hello()` 这张任务单完成，然后把结果给我。

**注意**：

- **`await` 只能写在 `async def` 函数体内部**
- **协程不 `await`，等于没写**
- **协程只能被 `await` 一次**
- 多个`await`连着写本质是**串行**的，需要用 `asyncio.gather` 或 `asyncio.create_task` 实现并发
- **为了在普通脚本里调用 `async def` 怎么办？需要用到 `asyncio.run()`**

**asyncio.run()：启动事件循环**

`asyncio.run()` 是**同步代码和异步代码之间的门**。它接收一个协程对象，启动一个**事件循环（event loop）**，把协程跑完，然后关掉事件循环：

```python
import asyncio

async def main():
    print('我在异步世界里')
    await asyncio.sleep(0.01)
    print('我又在异步世界里')

asyncio.run(main())
```

这里隐含了一个非常重要的设计：**整个程序里通常只有一个事件循环**，由 `asyncio.run()` 启动。所有的协程都跑在这个循环里。各位可以这么想象：

* 同步世界 → 一条直直的路，一步接一步
* 异步世界 → 一个调度中心（事件循环），里面挂着一堆协程，谁能跑就让谁跑

`asyncio.run()` 就是从同步世界踏进异步世界的入口。**一个程序只该调用一次**（嵌套调用会报错）。

### x.2 **gather**

**asyncio.gather：多协程并发**

```python
import asyncio
import time


async def task(name):
    await asyncio.sleep(0.5)
    return f'{name} 完成'


async def main():
    start = time.perf_counter()
    r1, r2, r3 = await asyncio.gather(
        task('A'),
        task('B'),
        task('C'),
    )
    cost = time.perf_counter() - start
    print(r1, r2, r3)
    print(f'用了 {cost:.2f} 秒')


asyncio.run(main())
```

输出：

```
A 完成 B 完成 C 完成
用了 0.51 秒
```

**注意**：

- `gather` 返回的是 **所有结果的列表**，顺序和传进去的顺序一致
- 任意一个协程抛异常，默认情况下整个 `gather` 都会抛
- 传进去的可以是协程，也可以是 Task 对象

`gather` 是 `asyncio` 里出现频率最高的函数之一。各位看到同时跑多个的需求，第一反应就该是它（或者后面要讲的 Task）。

### x.3 **Task**

**Task：把协程派出去**

上面的 `gather` 用得很爽，但有时候我们想要更细的控制：先把任务派出去，让它在后台跑着，我先去干别的事，需要的时候再回来收结果。

`asyncio.create_task()` 把一个协程包装成 `Task` 并**立即交给事件循环调度**。

```python
import asyncio
import time


async def slow_job(name, delay):
    await asyncio.sleep(delay)
    return f'{name} 完成'


async def main():
    start = time.perf_counter()
    t1 = asyncio.create_task(slow_job('A', 0.3))
    t2 = asyncio.create_task(slow_job('B', 0.5))
    print('两个任务已经派出去了，main 还能干别的')
    await asyncio.sleep(0.1)
    print('我先打个卡')
    r1 = await t1
    r2 = await t2
    cost = time.perf_counter() - start
    print(r1, r2)
    print(f'用了 {cost:.2f} 秒')


asyncio.run(main())
```

输出：

```
两个任务已经派出去了，main 还能干别的
我先打个卡
A 完成 B 完成
用了 0.49 秒
```

注意：

- **`create_task` 立刻把协程注册到事件循环开始跑了，不等 `await`**。
- `await t1` 这一行只是说现在我要这个结果，没好就在这等一下，等的时候 t2 也在并发地跑。

多种用法对比：

1. 直接 `await` 一个协程：本质串行，立即执行并收回
2. `await` 一个 `gather`（`gather`内包含多个协程）：一次性派一堆任务再一次性收回，写法紧凑
3. 逐个`create_task` + 逐个 `await`：先派一堆任务，后台跑，想要的时候再逐个收回
4. **最推荐的的写法**——逐个`create_task` + `gather(task1, task2,...)`：先派一堆任务，后台跑，想要的时候再一起收回

### x.4 **wait_for 和 timeout**

写网络请求的童鞋一定有个心结：万一对面服务器不返回，我这协程是不是要等到天荒地老？

这就要请出 **超时** 了。`asyncio` 提供了两套写法。

**第一种：`asyncio.wait_for`**，老牌的，写法是**把协程包一层**：

```python
import asyncio


async def slow_job():
    await asyncio.sleep(2)
    return '终于好了'


async def main():
    try:
        r = await asyncio.wait_for(slow_job(), timeout=0.5)
        print(r)
    except asyncio.TimeoutError:
        print('超时了，不等了')


asyncio.run(main())
```

输出：

```
超时了，不等了
```

`wait_for(coro, timeout=0.5)` 的意思是给这个协程 0.5 秒，超过就取消并抛 TimeoutError。

**第一种：`asyncio.timeout`**，Python 3.11+ 新写法，用 `async with` 当上下文：

```python
import asyncio


async def slow_job():
    await asyncio.sleep(2)
    return '终于好了'


async def main():
    try:
        async with asyncio.timeout(0.5):
            r = await slow_job()
            print(r)
    except TimeoutError:
        print('超时了，不等了')


asyncio.run(main())
```

输出：

```
超时了，不等了
```

效果一样，但是写起来更顺手——你想给**这一段**加超时，就把这段 `async with` 包起来。

要给多个 `await` 一起加超时，第二种写法尤其方便。新代码推荐用 `asyncio.timeout` 。

### x.5 **Task.cancel()**

超时本质上是**自动取消**。其实手动取消也很简单：

```python
import asyncio


async def long_running():
    try:
        for i in range(10):
            print(f'还在跑 {i}')
            await asyncio.sleep(0.05)
    except asyncio.CancelledError:
        print('被取消了，清理一下')
        raise # 把刚才捕获到的那个异常，原封不动地重新抛出去


async def main():
    t = asyncio.create_task(long_running())
    await asyncio.sleep(0.12)
    t.cancel()
    try:
        await t
    except asyncio.CancelledError:
        print('任务确实被取消了')


asyncio.run(main())
```

输出：

```
还在跑 0
还在跑 1
还在跑 2
被取消了，清理一下
任务确实被取消了
```

`Task.cancel()` 会向协程内部抛一个 `CancelledError` 。协程可以选择 catch 这个异常做清理。

但 **强烈建议** 在清理完之后 `raise` 出去，让外面知道任务确实被取消了——把 `CancelledError` 默默吞掉，是另一个非常隐蔽的坑。

### x.6 **TaskGroup**

`gather` 用着挺好，为什么 Python 3.11 又搞了个 `TaskGroup` 出来？

gather 的默认行为是**一个失败就把异常往外抛，其他任务继续在事件循环里跑**。

如果 A 里写文件、B 里发请求，它们会跑完（甚至再抛错）才停。这种**一脚走人，别人在背后干活**的行为不安全。

`TaskGroup` 解决了这个问题：

```python
import asyncio


async def good(name, delay):
    await asyncio.sleep(delay)
    return f'{name} ok'


async def bad():
    await asyncio.sleep(0.1)
    raise ValueError('坏了')


async def main():
    try:
        async with asyncio.TaskGroup() as tg:
            t_a = tg.create_task(good('A', 0.5))
            t_bad = tg.create_task(bad())
            t_b = tg.create_task(good('B', 0.5))
    except* ValueError as eg: # except* 是 Python 3.11 引入的 PEP 654「异常组」专用语法
        print('TaskGroup 捕获到异常组：', eg.exceptions)


asyncio.run(main())
```

输出：

```
TaskGroup 捕获到异常组： (ValueError('坏了'),)
```

`TaskGroup` 的几个优点：

* **自动等待全部完成** ：`async with` 退出之前，所有 task 一定都跑完了
* **一个失败，全部取消** ：`bad` 抛错之后，A 和 B 会被立刻 `cancel` ，不会继续在后台跑
* **异常组（ExceptionGroup）**：多个 task 同时失败的时候，所有异常会被打包成一个 `ExceptionGroup` ，用 `except*` 语法捕获

新代码里能用 `TaskGroup` 就用 `TaskGroup` ，比 `gather` 更安全、更可控。

`gather` 适合场景简单、一两个任务、对取消语义要求不高的场合。

### x.7 **async with 和 async for**

`asyncio.timeout(0.5)` 那个例子里，各位看到了 `async with` 的写法。这是「异步上下文管理器」——它的 `__enter__` 和 `__exit__` 都换成了 `async` 版本，叫 `__aenter__` 和 `__aexit__` 。

什么时候需要 `async with`？最常见的就是网络客户端：

<!-- skip-ci -->
```python
import asyncio
import httpx


async def main():
    async with httpx.AsyncClient() as client:
        r = await client.get('https://httpbin.org/get')
        print(r.status_code)


asyncio.run(main())
```

`async with` 之所以是 `async with`，是因为「关闭这个连接池」这件事本身可能涉及 IO，不能是一个普通的同步 `__exit__` 。

类似的还有「异步迭代器」——`async for` 。比如某些数据库驱动支持流式读取：

<!-- skip-ci -->
```python
import httpx
import asyncio


async def main():
    async with httpx.AsyncClient() as client:
        async with client.stream('GET', 'https://httpbin.org/stream/3') as resp:
            async for line in resp.aiter_lines():
                print('line:', line)


asyncio.run(main())
```

`async for line in resp.aiter_lines()` 的语义是：每读到一行（可能要等网络），把这一行交给我，然后继续等下一行。同步的 `for` 做不到这件事，只能 `async for` 。

各位记一下规律：

- 普通的对象、上下文 → `with` 、 `for`
- 涉及到 IO 的、协程的对象 → `async with` 、 `async for`

### x.8 **异步编程的使用场景**

`asyncio` 的强项是 **IO bound** ——程序大部分时间在等：等网络、等磁盘、等数据库、等用户输入。这种场景下「等待」是可以重叠的，async 能让一个线程同时等无数件事，效果立竿见影。

`asyncio` 的弱项是 **CPU bound** ——程序大部分时间在算：加密解密、图像处理、机器学习推理。这种场景下没有「等」可以利用，CPU 一直在干活，async 帮不上忙。一个线程也只能利用一个核，剩下七个核睡大觉。

对照表：

| 场景 | 用什么 |
| --- | --- |
| 抓 100 个 URL | `asyncio` + `httpx` |
| 同时读写一堆文件 | `asyncio` + `aiofiles` |
| 高并发 Web 服务（一台机器扛几千个连接） | `asyncio` + `FastAPI / aiohttp` |
| 视频转码、大矩阵计算 | `multiprocessing` 或 C 扩展 |
| 海量数据本地排序 | 多进程 + 分块 |
| 同时干一点 IO 一点 CPU | async 主框架 + `asyncio.to_thread` 或 `run_in_executor` |

各位可以这么记：**async 是用来「等」的，不是用来「算」的**。

## X、代码风格 ruff

## X、单元测试 pytest

30 秒跑完 100 个用例的测试框架

## X、标准日志 logging

把 `print` 调试升级成正经日志

## X、打包发布

让全世界一行 `pip install` 用上你的工具