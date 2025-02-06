---
share: true
---
# 声明
- 新式类
	- `Class myclass(object):`
	- 需要继承内置对象object
- 旧式类：`Class myclass():`
- Ref: https://www.cnblogs.com/btchenguang/archive/2012/09/17/2689146.html#WizKMOutline_1347874388282590

# Method
## 普通方法
- 定义
	- `def mymethod(self):` 一定要有参数self
	- 所有的类方法都是public和virtual的

## 特殊方法
- `static_mothod`
	- 声明: 
		- 使用装饰器@staticmethod
		- 独立的、单纯的函数，它仅仅托管于某个类的名称空间中，便于使用和维护
	- 调用: `obj.static_method`
- `class method`
	- 通过@声明
	- 调用 → `classname.classmethod()`
- `def __new__(cls)`
	- 是静态方法
	- 返回类的实例 → `return super(Person, cls).__new__(cls, name, age)`
	- 应用1
		- 当继承一些不可变的class时(比如int, str, tuple)， 提供一个自定义这些类的实例化过程的方法
		- 假如我们需要一个永远都是正数的整数类型
	- 应用2
		- 实现单例模式
		- 每一次实例化的时候，都只会返回这同一个instance对象
	- Ref: https://zhuanlan.zhihu.com/p/58139772
- `def __init__(self, arg):`
	- 初始化函数
	- 例化类的时候执行
- Ref: https://www.cnblogs.com/wcwnina/p/8644892.html
- 其他
	- `__getitem(self, key)__` → 实现之后可以让对象能够实现`Obj[key]`的操作
	- `__del__(self)` → 析构函数
	- `__len__(self)` →`len(Obj)`时会调用
	- `__str__(self)` → `print(Obj)`时会调用
	- `__call__(self)`
		- 类似于在类中重载 () 运算符
		- 使实例变得可调用 → obj()
		- Ref: http://c.biancheng.net/view/2380.html
# Variable
- Class Variable
	- 类的所有对象共享变量
	- 在method之外定义
- Object Variable: 在method之内定义
- 一般的变量都是public的
- 可以通过双下划线定义被保护的变量和方法 → `__var`

# Inheritance
- 声明 → `class subclass(superclass):`
- 方法重写 → 在子类的方法中需要重写父类的方法
- 初始化
	- 子类没有定义自己的初始化函数，父类的初始化函数会被默认调用
	- 如果子类定义了自己的初始化函数，父类的属性不会被初始化
- super() → 子类调用父类的方法的接口
- Ref: https://blog.csdn.net/brucewong0516/article/details/79121179
	
# Special Attribute
- `__slots__`
	- 限定实例的属性范围
	- 减小实例的内存占用

# Ref
- byte of python.pdf