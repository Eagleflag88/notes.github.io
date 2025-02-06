---
share: true
---
- 在别的模块中reuse函数

# Creation
- .py
	- 包含变量和函数
	- 实际上每个.py文件都是一个module
- 使用c代码编写，编译成module

# Package
- 一个包含module的文件夹
- 需要有__init__.py
	- 有没有内容都可以
	- 这样才能成为一个package

# Usage
- `import module_name`
	1. 从sys.path中搜索module
		- sys.path包含本路径
		- custom module需要被放到sys.path中
	2. 搜索到后，module_name中的代码会被执行一遍
	3. 可以利用module_name中的函数和变量
- `from package_name import module_name`
	- 能直接使用module_name
	- Process → 导入时会执行一次__init__.py
	- `import package_name` → 如果__init__.py没有导入子package/module的代码，子module不会被导入
	- `from package_name import *` 
		- import所有显式的符号
		- 不包括带__的
	- Ref: https://www.jianshu.com/p/178c26789011
# __name__
- 当这个module独立运行时 → `__name__ = '__main__'`
- 当被imported时 → `__name__  = 'module_name'`

# 保护成员
- 可以是函数和变量
- 通过单下划线修饰
	- `_foo`
	- `import*`无法导入