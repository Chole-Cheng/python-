# 大数据组 Python V1.0 编程规范

## 背景介绍

### 简介

Python 编程规范主要为了方便实验代码的阅读和管理，同时便于日常调试和维护，在研究课题完成后相关代码需要按照编程规范进行归档。

### 参考资料

* [Google Python风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/)

## 编程规范

### 导入

导入仅对包和模块使用，使得模块间共享代码。使用导入使得命名空间管理约定十分简单；缺点在于模块名仍可能冲突、部分模块名太长。  

**Recommended** 

* 使用`import x`来导入包和模块。

* 使用`from x import y`, x是包前缀，y是不带前缀的模块名。

* 使用```from x import y as z```, 用于两个将要导入的模块重名或导入的模块y名称太长。

  例如，用如下方式导入长名称模块```sound.effects.echo```:

  ```python
  from sound.effects import echo
  ...
  echo.EchoFilter(input, output, delay=0.7, atten=4)
  ```

* 每个导入应占一行

  ```python
  Yes: import os
       import sys
  ```
  
* 导入总应该放在文件顶部，位于模块注释和文档字符串之后，模块全局变量和常量之前。导入应该按照从最通用到最不通用的顺序分组：

  1. 标准库导入

  2. 第三方库导入

  3. 应用程序指定导入

   在每种分组中, 应该根据每个模块的完整包路径按字典序排序, 忽略大小写。
  
  ```python
  import foo
  from foo import bar
  from foo.bar import baz
  from foo.bar import Quux
  from Foob import ar
  ```

**Not Recommended**

* 导入包时不应使用相对名称。即使模块在同一个包中, 也应使用完整包名，以避免无意间将一个包导入两次。

* 导入多个包时，不应同占一行

  ```python
  No:  import os, sys
  ```

  

### 列表推导

列表推导和生成器表达式提供了一种简洁高效的方式来创建列表和迭代器。不必借助map(), filter(), 或lambda(), 比其它的列表创建方法更加清晰简单。因为避免了创建整个列表，生成器表达式可以十分高效；缺点是复杂的列表推导或者生成器表达式可能难以阅读。  

**Recommended** 

* 对于简单情况，每个部分应该单独置于一行: 映射表达式, for语句, 过滤器表达式。

  ```python
  Yes:
    result = []
    for x in range(10):
        for y in range(5):
            if x * y > 10:
                result.append((x, y))
  
    for x in xrange(5):
        for y in xrange(5):
            if x != y:
                for z in xrange(5):
                    if y != z:
                        yield (x, y, z)
  
    return ((x, complicated_transform(x))
            for x in long_generator_function(parameter)
            if x is not None)
  
    squares = [x * x for x in range(10)]
  
    eat(jelly_bean for jelly_bean in jelly_beans
        if jelly_bean.color == 'black')
  ```

**Not Recommended**

* 禁止多重for语句或过滤器表达式，复杂情况下应选择使用循环。

  ```python
  No:
    result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]
  
    return ((x, y, z)
            for x in xrange(5)
            for y in xrange(5)
            if x != y
            for z in xrange(5)
            if y != z)
  ```



### Lambda 函数

Lambda在一个表达式中定义匿名函数，与语句相反。常用于为 `map()` 和 `filter()` 之类的高阶函数定义回调函数或者操作符。  

优点是使用方便；由于通常只包含一个表达式，表达能力有限，比本地函数更难阅读和调试。没有函数名意味着堆栈跟踪更难理解。

**Recommended**

* 适用于单行函数，如果代码超过60-80个字符，最好定义成常规(嵌套)函数。

* 对于常见的操作符，使用模块函数代替Lambda函数。

  例如乘法操作符，使用 `operator` 模块中的函数`operator.mul`以代替lambda函数 `lambda x, y: x * y` 。

**Not Recommended**

* 不适用与非单行函数，代码超过60-80个字符，不应使用Lambda函数，推荐定义成常规(嵌套)函数。



### 条件表达式

条件表达式是对于if语句的一种更为简短的句法规则。 例如: `x = 1 if cond else 2` 。比if语句更加简短和方便，但如果表达式很长，难于定位条件，会比if语句难于阅读。

**Recommended**

* 用于单行函数。

**Not Recommended**

* 不适用于非单行函数，推荐使用完整的if语句。



### 默认参数值

在函数参数列表的最后指定变量的值，例如 `def foo(a, b = 0):` 。如果调用foo时只带一个参数, 则b被设为0. 如果带两个参数, 则b的值等于第二个参数。

默认参数值提供了一种简单的方法覆盖使用大量默认值函数的默认值, python不支持重载方法和函数，默认参数是一种“伪造”重载行为的简单方式。但是默认参数只在模块加载时求值一次，如果参数是列表或字典之类的可变类型, 这可能会导致问题；如果函数修改了对象(例如向列表追加项), 默认值将被修改。

**Recommended**

* 适用于大部分情况，鼓励使用。
```python
Yes: def foo(a, b=None):
         if b is None:
             b = []
```

**Not Recommended**

* 不要在函数或方法定义中使用可变对象作为默认值。

```python
No:  def foo(a, b=[]):
         ...
No:  def foo(a, b=time.time()):  # The time the module was loaded???
         ...
No:  def foo(a, b=FLAGS.my_thing):  # sys.argv has not yet been parsed...
         ...
```



### True/False（详细）

Python布尔值的条件语句更易读且不易犯错，大部分情况下速度更快。Python在布尔上下文中会将某些值求值为False。例如，所有的”空”值0，None，[]，{}都被认为是False。

**Recommended**

* 尽可能使用隐式False，例如: 使用 `if foo:` 而不是 `if foo != []:`。
*  `if x:` 等价于`if x is not None:`。当要测试一个默认值是None的变量或参数是否被设为其它值. 这个值在布尔语义下可能是False!
* 对于序列(字符串, 列表, 元组)，注意空序列是False. 因此 `if not seq:` 或者 `if seq:` 比 `if len(seq):` 或 `if not len(seq):` 更好。
* 处理整数时, 使用隐式False可能会不小心将None当做0来处理。可以将一个已知是整型(且不是函数`len()`的返回结果)的值与0比较。
* 注意‘0’(字符串)会被当做True。

```python
Yes: if not users:
         print 'no users'

     if foo == 0:
         self.handle_zero()

     if i % 10 == 0:
         self.handle_multiple_of_ten()
```



**Not Recommended**

* 不应使用==或者!=来比较单件， 比如None。使用is或者is not。
* 不应使用==将一个布尔量与False相比较。使用 `if not x:` 代替。如果你需要区分False和None， 应使用 `if not x and x is not None:` 这样的语句。

```python
No:  if len(users) == 0:
         print 'no users'

     if foo is not None and not foo:
         self.handle_zero()

     if not i % 10:
         self.handle_multiple_of_ten()
```



### 空格（详细）

按照标准的排版规范来使用空格。

**Recommended AND Not Recommended**

* 括号内不要有空格：

  ```python
  Yes: spam(ham[1], {eggs: 2}, [])
  
  No: spam( ham[ 1 ], { eggs: 2 }, [ ] )
  ```
  
* 不要在逗号, 分号, 冒号前面加空格, 但应该在它们后面加(除了在行尾)：

  ```python
  Yes: if x == 4:
           print x, y
       x, y = y, x
      
  No:  if x == 4 :
           print x , y
       x , y = y , x
  ```

* 参数列表, 索引或切片的左括号前不应加空格：

  ```python
  Yes: spam(1)
       
       dict['key'] = list[index]
      
      
  No: spam (1)
      
      dict ['key'] = list [index]
  ```

* 在二元操作符两边都加上一个空格, 比如赋值(=)， 比较(==, <, >, !=, <>, <=, >=, in, not in, is, is not)，布尔(and, or, not)；

  算术操作符两边的空格该如何使用, 需要你自己好好判断。不过两侧务必要保持一致：
  
  ```python
  Yes: x == 1
     
  No:  x<1
  ```
  
* 当’=’用于指示关键字参数或默认参数值时, 不要在其两侧使用空格：

  ```python
  Yes: def complex(real, imag=0.0): return magic(r=real, i=imag)
      
  No:  def complex(real, imag = 0.0): return magic(r = real, i = imag)
  ```

* 不要用空格来垂直对齐多行间的标记, 因为这会成为维护的负担(适用于:, #, =等)：

  ```python
  Yes:
       foo = 1000  # comment
       long_name = 2  # comment that should not be aligned
  
       dictionary = {
           "foo": 1,
           "long_name": 2,
           }
  
  No:
       foo       = 1000  # comment
       long_name = 2     # comment that should not be aligned
  
       dictionary = {
           "foo"      : 1,
           "long_name": 2,
           }
  ```

  

## 注释规范

## 编程建议

### TODO注释

### pdb调试

## 修订记录

* 2020年7月27日，由王雪 创建了文档
