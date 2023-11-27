---
share: true
---
# Interpreted Language
- Interpreter convert python source code into bytecode
- translate bytecode into the native language of target platform
- Run the native language
	
# 对象
- mutable
	- 初始化之后可以修改
	- list, dict

- immutable
	- 初始化之后便无法修改
	- 如果强行用=赋值，则会把原来的对象销毁，创建一个新的对象，完成赋值
	- int, string,tuple

# iterator
- 是一种被用来迭代可迭代对象的对象

## 方法
- 创建
	- iterator = iter(iterable)
	- 利用可迭代对象创建一个iterator对象
	- 不会返回iterable的所有内容，而是yield
- 迭代
	- value = next(iterator)
	- 调用iterator的next方法返回可迭代对象中的下一个值
## Ref
- https://www.geeksforgeeks.org/iterators-in-python/

# generator
- Problem
	- 一般迭代的时候会先定义iterable，然后在其上迭代 -> 一般的iterable会把其中所有内容存入内存
	- 不经济
- Approach
	- 使用generator expression/function直接创建generator/iterator
	- 一边循环一边计算 -> 保存的是计算下一个对象的算法
		
## generator object
- 创建
	- 创建之后返回的实际上是一个iterator
		- 也是简单的创建iterator的办法
		- 会同时自动实现__next()__和__iter()__方法
	- 使用generator expression
		- csv_gen = (row for row in open(file_name))
		- csv_gen是一个generator object
	- 调用generator function -> 在返回的时候用yield
		- 普通函数变成generator function
		- def gen_func(): yield 1 yield 2
		- x = gen_func() -> x是一个generator object
- Usage
	- x.next()/next(x) -> 返回下一个值
	- x.send() -> 重置generator的状态
	- For循环
		- for i in gen_func() -> 使用generator expression也一样
		- 使用yield返回，占用内存小
- Ref
	- geeksforgeeks
	- https://www.programiz.com/python-programming/generator
	- https://blog.csdn.net/zhong_jay/article/details/91799459
- Best Practice
	- 如果memory是主要concern的话，用generator
	- 如果速度是主要concern的话，用普通iterable对象
# for
- Collection-based loop
	- 程序在一个collection of object上迭代
	- for i in collection -> collection需要是iterable的对象，比如说range()返回的对象
- Process
	1. 调用iter(iterable)返回一个iterator
	2. 调用next(iterator)，返回值赋给i
	3. 执行loop body
	4. 当next()返回错误时停止
- Ref: https://realpython.com/python-for-loop/
- range()
	- 遍历元素
	- for i in range(0, 5) -> range()返回的是一个list，全元素
- enumerate()
	- 遍历的同时还能获得count
	- for count, value in enumerate(values):

# 名称作用域
- local：函数内部变量
- enclosing
	- 包含了非局部(non-local)也非全局(non-global)的变量
	- 主要是考虑函数嵌套中的变量之间的关系
- global -> 模块级别的变量
- built-in -> 系统级别的关键字/变量
	- abs, char
	- 通过 builtin 的标准模块来实现
- Rule
	- 变量的作用域总是由在代码中被赋值的地方所决定的
	- 寻找顺序：L→E→G→B
- Ref：https://www.runoob.com/python3/python3-namespace-scope.html

# Reference
- 对象创建之后会被赋值(=)给变量
	- 此时这个变量是这个对象的引用
	- 赋值的过程被称为binding
- 每binding一次，都会让这个对象的reference count加一

# closure
- 闭包就是引用了自有变量的函数，这个函数保存了执行的上下文，可以脱离原本的作用域独立存在
- Ref: https://www.jianshu.com/p/ee82b941772a

# duck typing
- 动态类型风格
- 一个对象能否被某个父类对象使用，关键不在于他的类型而决定与他所实现的方法和属性
## 例子
- def makeRun(animalType):
    animalType.run()
- 传入的参数类型不一定需要是animalType，只要他实现了run()，就能调用成功
- Ref: https://www.jianshu.com/p/e97044a8169a

# decorator
- 装饰器（Decorator）是一种特殊的函数，用于修改其他函数的功能。装饰器本质上是一个接受函数作为参数并返回一个新函数的函数。
- 它们常用于在不修改原始函数代码的情况下增加额外的功能。
- 装饰器通过在函数前使用 `@` 符号来应用，如下例所示：
```
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()

```
- 在这个例子中，`say_hello` 函数被 `my_decorator` 装饰器装饰，使其在执行前后增加了额外的打印语句。


# 操作符
- is: 比较的是两个对象的id是否一致
- ==
	- 数值比较: 值比较
	- 其他类型比较
		- 默认使用is
		- 可通过实现类的__eq__方法来比较
- 特例
	- 小整数对象池
		- 小整数对象[-5,256]是全局解释器范围内被重复使用，引用的对象都是同一个
		- 区间内的整数不同变量只要值相同，引用地址也相同
		- c = 257, d = 257 -> c is d → true
	- 大整数对象池
		- 处于同一个代码块的不可变对象是同一个对象
		- Ref: https://blog.csdn.net/LLY_A_/article/details/106724213
	- intern机制 -> python对运行中产生的字符串也会形成一个池