python是解释型语言，不需要编译和链接，可以节省开发时间。
python的代码更短的原因：
- 高级数据类型允许在单一语句中表述复杂操作；
- 使用缩进，而不是括号实现代码块分组；
- 无需预声明变量或参数。


### 解释器
切换到python的安装路径：
```bash
cd /usr/bin
python3.12
```
便可以在shell中运行python。
另一种启动方式：
```bash
python -c "command" [arg]
```
这将会执行command中的语句，由于python语句经常包含空格或特殊字符，因此将以用引号将整个command括起来。
python模块也可以当作脚本使用：
```bash
python -m module [arg]
```
这样将执行module的源文件。
##### 传入参数
解释器读取命令行参数，把脚本名与其他参数转化为字符串列表存在sys模块的argv变量里。
执行import sys，可以导入该模块，并访问该列表。至少有一个元素。
当未给定参数时，sys.argv\[0]是空字符串。
给定脚本名为 '-' (标准输入)时，sys.argv\[0]是'-'。
使用 -c command时， sys.argv\[0]是 '-c'。
如果使用选项 -m module，sys.argv\[0]就是包含目录的模块全名。
解释器不处理 -c command或-m module之后的选项，而是直接留在 sys.argv中由命令或模块来处理。
##### 运行环境
默认情况下，python源码文件的编码是 UTF-8。
要正确显示字符，编辑器必须能识别UTF-8编码，而且必须使用支持文件中所有字符的字体。
如果不使用默认编码，要声明文件的编码，文件第一行要写成特殊注释：
```python
# -*- coding: encoding -*-
```
encoding可以是python支持的任意一种 codecs。
第一行规则的例外情况：
```python
#!/usr/bin/env python3
#-*- coding:encoding -*-
```

## 控制流
#### if语句
```python
x = int(input("请输入任意整数："))

if x < 0:
	x = 0
	print("已将负数改为0")
elif x == 0:
	print("0")
elif x == 1:
	print("1")
else:
	print("大于1")
```
如果是将一个值与多个指进行比较，或检查特定类型或属性，match语句更有用。
#### match语句
接受一个表达式并把它的值与一个或多个case块给出的一系列模式进行比较。
只有第一个匹配的模式会被执行，并且它还可以提取值的组成部分赋给变量。
将一个主语值与多个字面指进行比较：
```python
def http_error(status):
	match status:
		case 400:
			return "Bad request"
		case 404:
			return "Not found"
		case 418:
			return "I'm a teaport"
		case _:
			return "Something's wrong with the internet"
```
注：最后一个代码块中的"\_" 作为通配符必定会匹配成功。如果没有case匹配成功，则不会执行任何分支。
可以使用 | (or) 在一个模式中组合多个字面值：
```python
def http_error(status):
	match status:
		case 401 | 403 | 404:
			return "Not allowed"
		case _:
			return "Something's wrong with the internet"
```
形如解包赋值的模式可用于绑定变量：
```python
# point is an (x, y) tuple
match point:
	case (0, 0):
		print("Origin")
	case (0, y):
		print(f"Y={y}")
	case (x, 0):
		print(f"X={x}")
	case (x, y):
		print(f"X={x}, Y={y}")
	case _:
		raise ValueError("Not a point")
```
第一个模式有2个字面值，可视为前述字面值模式的扩展；
接下来的两个模式结合了字面值和一个变量，变量绑定了来自主语(point)的一个值。 第四个模式捕获了两个值，使其在概念上与解包赋值 (x,y) = point类似。
如果用类组织数据，可以用"类名后接一个参数列表"这种很像构造器的形式，把属性捕获到变量里：
```python
class Point():
	def __init__(self, x, y):
		self.x = x
		self.y = y

def where_is(point):
	match point:
		case Point(x=0, y=0):
			print("Origin")
		case Point(x=0,y=y):
			print(f"Y={y}")
		case Point(x=x, y=0):
			print(f"X={x}")
		case Point():
			print("Somewhere else")
		case _:
			print("Not a point")
```
在某些为其属性提供了排序的内置库中使用位置参数，也可以通过在类中设置__match_args__特殊属性来为模式中的属性定义一个专门的位置。
如果它被设为("x", "y")，则以下模式均为等价，且都是将y属性绑定到var变量：
```python
Point(1, var)
Point(1, y=var)
Point(x=1, y=var)
Point(y=var, x=1)
```
通过将其视为赋值语句等号左边的一种扩展，来理解各个便来那个被设为何值。
match语句只会为单一的名称赋值，而不会复制给带点号的名称(如foo.bar)、属性名和类名。
模式可以任意嵌套：
如果我们有一个由Point组成的列表，且Point添加了__match_args__时，可以这样来匹配：
```python
class Point():
	__match_args__ = ('x', 'y')
	def __init__(self, x, y):
		self.x = x
		self.y = y

match points:
	case []:
		print("No points")
	case [Point(0,0)]:
		print("The origin")
	case [Point(x, y)]:
		print(f"Single point {x} {y}")
	case [Point(0, y1), Point(0, y2)]:
		print(f"Two on the Y axis at {y1}, {y2}")
	case _:
		print("Something else")
```
可以向一个模式添加if子句，称为”约束项“。
如果约束项为假值，则match将常识下一个case语句块：
```python
match point:
	case Point(x, y) if x == y:
		print(f"Y=X at {x}")
	case Point(x, y):
		print(f"Not on the diagonal")
```
该语句的其他特性：
- 与解包赋值类似，元组和列表模式具有完全相同的含义并且实际上都能匹配任意序列，区别是它们不能匹配迭代器或字符串；
- 序列模式支持扩展解包：\[x,y,\*rest]和 (x,y,\*rest)和相应的解包赋值做的事是一样的。接在 \* 后的名称也可以为 \_, 所以 (x, y, \*\_)匹配含至少两项的序列，而不必绑定剩余的项。
- 映射模式：{"bandwidth":b, "latency":l}从字典中捕获"bandwidth"和"latency"的值。额外的键会被忽略。
- 子模式可使用 as 关键字来捕获：
	- case (Point(x1,y1),Point(x2,y2) as p2): ...
- 大多数字面值所按相等性比较的，但是单例对象True、False和None则是按id比较的。
- 模式可以使用具名常量。它们必须作为带点号的名称出现，以防止它们被解释为用于捕获的变量：
```python
from enum import Enum
class Color(Enum):
	RED = 'red'
	GREEN = 'green'
	BLUE = 'blue'
color = Color(input("Enter your choice of 'red', 'blue' or 'green': "))
match color:
	case Color.RED:
		print("I see red!")
	case Color.GREEN:
		print("Grass is green")
	casee Color.BLUE:
		print("I'm feeling the blues :(")
```
#### for语句
Python的for语句不迭代算数递增数值，或是给予用户定义迭代步骤和结束条件的能力，而是在列表或字符串等任意序列的元素上迭代，按它们在系列中出现的顺序：
```python
words = ['cat', 'window', 'defenestrate']
for word in words:
	print(word ,len(word))
```
很难正确地在迭代多项集的同时修改多项集的内容。
更简单的做法是迭代多项集的副本或创建新的多项集。
#### range()函数
内置函数range()用于生成等差数列：
```python
for i in range(5):
	print(i)
```
按索引迭代序列：
```python
a = ['Mary', 'had', 'a', 'little', 'lamb']
for i in range(len(a)):
	print(i, a[i])
```
大多数情况下， enumerate()函数更方便。
range()返回的对象和列表不同，该对象只有在被迭代时才一个一个地返回所期望的列表项，并没有真正生成一个含有全部项的列表，从而节省空间。
这种对象被成为 可迭代对象 iterable，适合作为需要获取一系列值的函数或程序构件的参数。 for语句就是这样的程序构件， 以可迭代对象作为参数的函数例如sum():
```python
sum(range(4))
```
#### 循环中的 break, continue语句及else子句
brank语句将跳出最近的一层 for 或 while 循环。
for 或 while 循环可以包括 else子句。
在for循环中， else子句会在循环成功结束最后一次迭代之后执行。
在while循环中， else子句会在循环条件变为False后执行。
如果因为break子句结束循环， 则else子句不会执行。
```python
for n in range(2,10,1):
	for x in range(2, n):
		if n % x == 0:
			print(n, 'equals', x, '*', n//x)
			break
	else:
		print(n, 'is a prime number')
```
continue语句， 借鉴自C语言，以执行循环的下一次迭代来继续：
```python
for num in range(2, 10):
	if num % 2 == 0:
		print("Found an even number", num)
		continue
	print("Found an odd number", num)
```
#### pass语句
pass语句不执行任何操作。
```python
while True:
	pass
```
语法上需要一个语句，但不需要程序执行任何动作，使用该语句。
pass常用于创建一个最小的类：
```python
class MyClass:
	pass
```
pass可用作函数或条件语句体的占位符：
```python
def initlog(*args):
	pass
```
### 定义函数
```python
def fib(n):
	"""Print a Fibonacci series up to n."""
	a, b = 0, 1
	while a < n:
		print(a, end=' ')
		a, b = b, a+b
	print()

fib(2000)
```
函数内第一条语句时字符串时，该字符串就是文档字符串(docstring)。
利用文档字符串可以自动生成在线文档或打印版文档，还可以让开发者在浏览代码时直接查阅文档。
函数在执行时使用函数局部变量符号表，所有函数变量赋值都存在局部符号表中；引用变量时，首先在局部符号表里查找变量，然后，是外层函数局部符号表， 再是全局符号表，最后是内置名称符号表。
最好不要在函数内直接赋值(除非是global语句定义的全局变量或nonlocal语句定义的外层函数变量)。

在调用函数时会将实参引入到被调用函数的局部符号表中；因此，实参是使用*按值调用*来传递的(值始终是对象的引用， 而不是对象的值)。
**当一个函数调用另一个函数时，会为该调用创建一个新的局部符号表。**

函数定义在当前符号表中把函数名与函数对象关联在一起。
解释器把函数名指向的对象作为用户自定义函数。还可以使用其他名称指向同一个函数对象， 并访问该函数：
```python
fib    #<function fib at 10042ed0>
f = fib
f(100)
```
在python中，即使没有return语句的函数也会有返回值，None。
通常解释器会屏蔽单独的返回值None，如果需要的话，可以用print()查看它：
```python
fib(0)
print(fib(0))
```
编写不直接输出运算结果，而是返回运算结果列表的函数也简单：
```python
def fib2(n):
	"""Return a list containing the Fibonacci series up to n."""
	result = []
	a, b = 0, 1
	while a < n:
		result.append(a)
		a, b = b, a+b
	return result

f100 = fib2(100)
f100
```
=>上述例子的python功能：
- return语句返回函数的值。return语句不带表达式参数时，反或None。函数执行完毕退出也返回None。
- result.append(a)调用了列表对象result的方法。 
	- 方法是“从属于”对象的函数，名称为 obj.methodname。不同的对象定义了不同的方法。

#### 自定义函数参数
##### 默认值
为参数指定默认值是很有用的方式，调用函数时，可以使用比定义时更少的参数：
```python
def ask_ok(prompt, retries=4, reminder='Please try again')
	pass
```
**警告： 默认值只计算一次。**
默认值为列表、字典或类实例等可变对象时，会产生与该规则不同的结果。
```python
def f(a, l=[]):
	L.append(a)
	return L

print(f(1)) #[1]
print(f(2)) #[1,2]
print(f(3)) #[1,2,3]
```
如果不想在后续调用之间共享默认值时，应以如下方式编写函数：
```python
def f(a, L=None):
	if L is None:
		L = []
	L.append(a)
	return L
```
##### 关键字参数
kwarg=value形式的关键字参数也用于调用函数：
```python
def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
```
函数调用时，关键字参数必须跟在位置参数后面。
所有传递的关键字参数都必须匹配一个函数接受的参数，关键字参数的顺序并不重要。

当最后一个形参为\*\*name形式时，接受一个字典，该字典包含与函数中已定义形参对应之外的所有关键字参数。
\*name形参接受一个元组，该元组包含形参列表之外的位置参数。
```python
def cheeseshop(kind, *arguments, **keywords):
	print("--Do you have any", kind, "?")
	print("--I'm sorry, we're all out of", kind)
	for arg in arguments:
		print(arg)
	print("-" * 40)
	for kw in keywords:
		print(kw, ":", keywords[kw])

cheeseshop("Limburger", "It's very runny, sir.",
		   "It's really very, VERY runny, sir.",
		   shopkeeper="Michael Palin",
		   client="John Cleese",
		   sketch="Cheese Shop Sketch")
```
##### 特殊参数
默认情况下， 参数可以按位置或显式关键字传递给Python函数。
为了让代码易读、高效，最好限制参数的传递方式。
```python
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
```
注： /和\*是可选的。这些符号表明形参如何把参数值传递给函数：位置、位置或关键字、关键字。 （关键字形参也称为命名形参）
说明：
- 使用仅限位置形参，可以让用户无法使用形参名。形参名无实际意义时，强制调用函数的实参顺序时，或同时接收位置形参和关键字时，这种方式很有用；
- 当形参名有实际意义，且显式名称可以让函数定义更易理解时，阻止用户以来传递实参的位置时，才使用关键字。
- 对于API，使用仅限位置形参，可以防止未来修改形参名时造成破坏性的API变动。

##### 任意实参列表
比较少见，这些实参包含在元组中，在可变数量的实参之前，可能有若干个普通参数：
```python
def write_multiple_items(file, separator, *args):
	file.write(separator.join(args))
```
*variadic*参数用于采集传递给函数的所有剩余参数，因此，它们通常在形参列表的末尾。
\*args形参后的任何形式参数只能是仅限关键字参数，即只能用作关键字参数，不能用作位置参数：
```python
def concat(*args, sep="/"):
	return sep.join(args)
```
##### 解包实参列表
函数调用要求独立的位置参数，但是实参在列表或元组里时，要执行相反的操作。
例如，内置的range()函数要求独立的start和stop实参。如果这些参数不是独立的，则要在调用函数时，用\*操作符把实参从列表或元组中解包出来：
```python
list(range(3, 6, 1))
args = [3, 6]
list(range(*args))
```
同样，字典可用\*\*操作符传递关键字参数：
```python
def parrot(voltage, state='a stiff', action='voom'):
	...

dic = {'voltage':'four million', 'state':'bleedin', 'action':'VOOM'}
parrot(**dic)
```
##### Lambda表达式
Lambda关键字用于创建小巧的匿名函数。
lambda a, b: a+b  函数返回两个参数的和。Lambda函数可用于任何需要函数对象的地方。
在语法上，匿名函数只能是单个表达式。
在语义上，它只是常规函数定义的语法糖。
与嵌套函数定义一样，lambda函数可以引用包含作用域中的变量：
```python
def make_incrementor(n):
	return lambda x: x + n

f = make_incrementor(42)
f(0)
f(1)
```
lambda表达式还能把匿名函数用作传递的实参：
```python
pairs.sort(key=lambda pair:pair[1])
```
##### 文档字符串
以下是文档字符串内容和各式的约定：
第一行应为对象用途的简短摘要。 为保持简洁，不要显式说明对象名或类型，因为可通过其他方式获取这些信息。 这一行应以大写字母开头，以句点结尾。
文档字符串为多行时，第二行应为空白行，在视觉上将摘要与其余描述分开。后面的行可包含若干段落，描述对象的调用约定、副作用等。

Python解析器不会删除Python中多行字符串字面值的缩进，因此，文档处理工具应在必要时删除缩进。
>文档字符串第一行之后的第一个非空行决定了整个文档字符串的缩进量，然后，删除字符串中所有行开头处与此缩进"等价"的空白符。不能有比此缩进更少的行，但如果出现了缩进更少的行，应删除这些行的所有前空白符。转化制表附后，应测试空白符的等效性：

```python
def my_function():
	"""DO nothing, but document it.

	No, really, it doesn't do anything.
	"""
	pass

print(my_function.__doc__)
```
##### 函数注解
可选的用户自定义函数类型的元数据完整信息。
标注 以字典形式存放在函数的 \_\_annotations\_\_属性中而对函数的其他部分没有影响。
形参标注的定义方式时在形参名后加冒号，后面跟一个会被求值为标注的值的表达式。
返回值标注的定义方式是加组合符号 ->, 后面跟一个表达式，这样的校注位于形参列表和表示def语句结束的冒号。
```python
def f(ham:str, eggs:str = 'eggs') -> str:
	print("Annotations:", f.__annotations__)
	print("Arguments:", ham, eggs)
	return ham + ' and ' + eggs

f('spam')
```
##### 编码风格
Python 项目大多遵循PEP8的风格指南：
- 缩进， 用4个空格，不要用制表符；
- 换行，一行不超过79个字符；
- 用空行分隔函数和类，及函数内较大的代码块；
- 最好和注释放到单独一行；
- 使用文档字符串；
- 运算符前后、逗号后要用空格，但不要直接在括号内使用：a=f(1,2) + g(3, 4)。
- 类和函数的命名要一致；按惯例，命名类用UpperCamelCase，命名函数与方法用lowercase_with_underscores.
	- 命名方法中第一个参数总是用self。
- 编写用于国际多语环境的代码时，不用生僻的编码。(UTF-8与纯ASCII)
- 不要在标识符中使用非 ASCII字符。


下一节：
[[数据结构]]

