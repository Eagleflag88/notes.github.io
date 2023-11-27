---
share: true
---
# 定义
## 参数
- default parameter
	- 如果不作另外修正则使用参数的默认值
	- 在函数定义时通过arg = val来实现，这个表达式需要放在参数列表最后
- keyword parameter
	- 在调用函数时，明确某个参数的值
	- test(a, b) 
	   test(a = 10, 10)
- varargs parameters
	- 可变参数
	- 可定义一个能收取很多无限多参数的函数
	- 实现
		1. ```*para```
					- 在这之后的所有的输入参数都会转换成一个tuple
			- def total(a=5, *numbers, **phonebook): 
				total(10,1,2,3,Jack=1123,John=2231,Inge=1560)
		2. para：在这之后的所有的输入参数都会转换成一个dict
- return
	- 默认会return none
	- 返回值: 可以通过返回一个tuple来使一个函数返回不止一个参数 -> def get_error_details(): 
		return (2, 'details')
## 局部变量
- 函数内部定义的变量留在函数内部

## 全局变量
- 在函数内部通过global修饰
- 函数内部的操作可以控制一个全局的变量

## 参数传递
- 传递的时候会发生赋值操作 -> 形参=实参
- 传递immutable
	- 因为是immutable所以赋值操作会让形参成为另一个对象
	- 函数内部进行的操作不会影响外部的变量
- 传递mutable
	- 因为是mutable所以赋值操作会让形参和实参指向同一个对象
	- 函数内部进行的操作会影响外部的变量
- Ref: https://blog.csdn.net/iMotherBoard/article/details/95725562
- 星号传递
	- Usage
		- def foo(bar, lee):
			print(bar, lee)
		- l = [1,2]
			foo(*l)
		- 通过星号会先把l解包，然后传递进去分别赋值
	- 跟varargs parameters是相反的

# 基本函数
## format
- 创建string
- 示例
	- name = 'smith'
	- print('Why is {0} playing?'.format(name))
- f-string -> print(f'Why is {name} playing?')

## range()
- 参数: range(start, stop [, step])
- 返回一个可迭代对象
	- range对象 -> 不是generator
	- 不包含stop
	- 一次产生一个返回值，不会一次性返回所有序列 -> 但是list已经载入内存了
	- 通过list(range)获得整个list
- xrange()
	- 使用方法和range()一致
	- 返回一个generator对象
		- 使用lazy evaluation
		- 只有在需要时才返回对象 -> list还没有载入内存
	- python3中不存在

## type()
- type(range(0, 5)) -> 返回输入参数的类型

## help()
- help('sys')
- a = [1,2,3] -> help(a)
	
## len()
- 返回长度

## del var
- 删除一个var
	- 不会释放内存
	- 只会删除一个引用

## id()
- id(a)：
	- 返回a的唯一标识符
	- 在Cython中返回地址

## dir()
- dir(a):返会对象a的属性
- dir(): 表示本module的属性

## hasattr(object, name)
- 返回对象是否含有name这个属性
- 就是通过dir获得的属性

## getattr(object, name)
- 返回对象的name属性的值

## isinstance(object, name)
- 返回object是否属于name类

## 拷贝
- copy.copy(): b = copy.copy(a)
	- 返回一个对象，浅拷贝
		- id()返回结果不一样
		- 只拷贝了不可变类型的元素
- copy.deepcopy()
	- b = copy.deepcopy(a)
	- 返回一个对象，深拷贝
		- id()返回结果不一样 -> 完全拷贝
- Ref: https://www.py.cn/jishu/jichu/13241.html

## sys.argv
- 返回命令行的输入，一个list